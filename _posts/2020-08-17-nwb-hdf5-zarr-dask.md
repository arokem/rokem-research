---
layout: post
title:  "NWB+HDF5+ZARR+Dask"
date:   2020-08-17
---

This was not meant to be a usual year of research, after all. Sigh. 
Trying to pick this back up, though. 

One of the things that I have been working in the past week or so is 
a new and rather exciting development, spearheaded by Ben Dichter and 
Daniel Sotoude. Based on [previous work](https://medium.com/pangeo/cloud-performant-reading-of-netcdf4-hdf5-data-using-the-zarr-library-1a95c5c92314) in the geoscience community, they have been working on enabling reading of NWB files stored as HDF5 into ZARR: https://github.com/catalystneuro/HDF5Zarr. 
This means that a large NWB file containing multi-channel recordings of neurophysiology can be stored in 
cloud storage and through a combination of gcsfs and their work read into a ZARR accessible on a 
jupyterhub node. 

My first set of experiments with this approach uses a 70 GB neurophysiology recording, provided by Yoni Browning and Beth Buffalo. 
The details of their experimental setup and what exactly is in these time-series is interesting, but not so important for 
what follows. 

The first thing that can be done in this setup is to look at the data. All of it. Even though the hub is running on a 
machine with only 13 GB of RAM, we can access it through ZARR/gcsfs at near-interactive speed. For example, 
I implemented the following widget: 

```
chan = widgets.SelectMultiple(
    options=np.arange(arr.shape[1]),
    value=[0],
    description='Channels:',
    disabled=False
    
)

time = widgets.IntSlider(
    value=0,
    min=0,
    max=arr.shape[-1]-5000,
    step=1,
    description='Starting timepoint:',
    disabled=False,
    continuous_update=False,
    orientation='horizontal',
    readout=True,
)

time_d = widgets.IntSlider(
    value=5000,
    min=0,
    max=10000,
    step=1,
    description='Window duration',
    disabled=False,
    continuous_update=False,
    orientation='horizontal',
    readout=True,
)



def f(time, time_d=5000, chan=[0]):
    fig, ax = plt.subplots()
    for c in chan:
        if c < 124:
            ax.plot(arr[c, time:time+time_d], label=f"Channel {c}")
    ax.legend()
    ax.set_xlabel("Time (samples)")
    ax.set_ylabel("Voltage")
    plt.show()

out = widgets.interactive_output(f, {'chan': chan, 'time': time, 'time_d': time_d})

widgets.HBox([widgets.VBox([chan, time, time_d]), out])

```
This is not painful: I can select a channel, or even several channels and see their time-series appear within a very short time. In fact, 
I suspect that most of that time is Matplotlib dealing with the rendering of all this data locally. The key seems to be that data access patterns matter. So, data should be  addressed along the chunks stored in the zarr. That is `arr[chan, x:x+delta]` is good, but `arr[chan1:chan2, x:x+delta]` can be disastrous. But I need to experiment a bit more.

Second, by attaching this hub to a Dask kube cluster, we can process the data. For example, if we want to do a spectral decomposition using wavelets, we can write something like: 

```

from mne.time_frequency import tfr_array_morlet
from functools import partial
freqs = np.logspace(1, 2, 20)
my_morlet = partial(tfr_array_morlet, sfreq=fs, freqs=freqs)
morlet_arr = arr.map_blocks(my_morlet, dtype=np.complex128, new_axis=2)

```

This generates the Dask array that could compute the Morlet wavelet decompisition for the entire dataset, for 
20 frequency bands. This would result in a rather large variable: about 0.5 TB here. But this can still be 
viewed at near interactive speed: 

```
chan = widgets.Select(
    options=np.arange(arr.shape[1]),
    value=[0],
    description='Channel:',
    disabled=False
    
)

time = widgets.IntSlider(
    value=0,
    min=0,
    max=arr.shape[-1]-500,
    step=1,
    description='Starting timepoint:',
    disabled=False,
    continuous_update=False,
    orientation='horizontal',
    readout=True,
)

time_d = widgets.IntSlider(
    value=50,
    min=0,
    max=1000,
    step=1,
    description='Window duration',
    disabled=False,
    continuous_update=False,
    orientation='horizontal',
    readout=True,
)



def f(time, time_d=5000, chan=0):
    fig, ax = plt.subplots()
    result = np.abs(morlet_arr[0, chan, :, time:time+time_d].compute())
    ax.matshow(result)
    ax.legend()
    ax.set_xlabel("Time (samples)")
    ax.set_ylabel("Frequency bin")
    plt.show()

out = widgets.interactive_output(f, {'chan': chan, 'time': time, 'time_d': time_d})

widgets.HBox([widgets.VBox([chan, time, time_d]), out])
```

Again, this is not painful, although it can only show one channel at a time and does this with a 
small, maybe one second, delay. 

But this doesn't do everything we'd like to do. For example, we want to normalize to z-score in 
every frequency band in every channel. I think that this could be done by creating a new array: 

```
morlet_mean = da.mean(morlet_arr, axis=-1)
morlet_std = da.std(morlet_arr, axis=-1)
zscored = (morlet_arr[:,0,:,:] - morlet_mean[:, 0, :]) / morlet[:, 0, :]
```

Calculating the z-scored value here is challenging, because this would require aggregating across all of the channel 
data. And this is where Dask should help. I haven't done this entire computation yet, but 
calculating the mean for the channel is about 10 minutes of computing. We do also need to worry about the 
edges of each block when doing this computation, so one of the next set of experiments to do would use 
the Dask `arr.map_overlap` method and a more elaborate Morlet function that also windows each chunk to 
combine with neighboring chunks.
