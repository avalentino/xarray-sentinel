# xarray-sentinel

**WARNING: development stage is Alpha**

Xarray backend to explore and load Copernicus Sentinel-1 satellite data products.

This Open Source project is sponsored by B-Open - https://www.bopen.eu

## Features

- access to metadata: product, orbit, attitude, GCPs, calibration - **Alpha**
- access to metadata: deramp - in roadmap
- access to SLC burst data - **technology preview**
- products:
  - Sentinel-1 SLC IW (Interferometric Wide Swath): **Alpha**
  - Sentinel-1 SLC EW (Extended Wide Swath): **technology preview**
  - Sentinel-1 SLC SM (Stripmap): in roadmap
  - Sentinel-1 GRD SM/IW/EW: **technology preview**
  - Sentinel-2 L1C/L2A: in roadmap

## Install

The easiest way to install *xarray-sentinel* is via *conda*.
Create a new environment, activate it, install the package and its dependencies,
as follows:

```shell
    conda create -n XARRAY-SENTINEL
    conda activate XARRAY-SENTINEL
    conda install -c conda-forge rioxarray xmlschema
    pip install xarray-sentinel
```

## Sentinel-1 SLC IW

### Data

Currently, xarray-sentinel provides access as Xarray datasets to the following data:

- gcp
- orbit
- attitude
- calibration
- swath data

using `azimuth_time` and `slant_range_time` dimensions.

## Examples:

### Open the root dataset

```python-repl
>>> from xarray_sentinel import sentinel1
>>> product_path = "tests/data/S1B_IW_SLC__1SDV_20210401T052622_20210401T052650_026269_032297_EFA4.SAFE"
>>> sentinel1.open_dataset(product_path)
<xarray.Dataset>
Dimensions:  ()
Data variables:
    *empty*
Attributes: ...
    constellation:              sentinel-1
    platform:                   sentinel-1b
    instrument:                 ['c-sar']
    sat_orbit_state:            descending
    sat_absolute_orbit:         26269
    sat_relative_orbit:         168
    ...                         ...
    sar_product_type:           SLC
    xs_instrument_mode_swaths:  ['IW1', 'IW2', 'IW3']
    group:                      /
    subgroups:                  ['IW1', 'IW1/VH', 'IW1/VH/gcp', 'IW1/VH/orbit...
    Conventions:                CF-1.8
    history:                    created by xarray_sentinel-...

```

The attribute `subgroups` shows the available groups to be loaded. The keyword `group`
shall be used to select the dataset to be loaded.

### Open the gcp dataset

To load the gcp relative to the VV polarisation of first swath use the key `group="IW1/VV/gcp"`:

```python-repl
>>> sentinel1.open_dataset(product_path, group="IW1/VV/gcp")
<xarray.Dataset>
Dimensions:           (azimuth_time: 10, slant_range_time: 21)
Coordinates:
  * azimuth_time      (azimuth_time) datetime64[ns] 2021-04-01T05:26:24.20973...
  * slant_range_time  (slant_range_time) float64 0.005343 0.00536 ... 0.005679
    line              (azimuth_time) int64 0 1501 3002 ... 10507 12008 13508
    pixel             (slant_range_time) int64 0 1082 2164 ... 19476 20558 21631
Data variables:
    latitude          (azimuth_time, slant_range_time) float64 ...
    longitude         (azimuth_time, slant_range_time) float64 ...
    height            (azimuth_time, slant_range_time) float64 ...
    incidenceAngle    (azimuth_time, slant_range_time) float64 ...
    elevationAngle    (azimuth_time, slant_range_time) float64 ...
Attributes: ...
    constellation:              sentinel-1
    platform:                   sentinel-1b
    instrument:                 ['c-sar']
    sat_orbit_state:            descending
    sat_absolute_orbit:         26269
    sat_relative_orbit:         168
    ...                         ...
    xs_instrument_mode_swaths:  ['IW1', 'IW2', 'IW3']
    group:                      /IW1/VV/gcp
    Conventions:                CF-1.8
    title:                      Geolocation grid
    comment:                    The dataset contains geolocation grid point e...
    history:                    created by xarray_sentinel-...

```

### Open the orbit dataset

Similarly for orbit data use `group="IW1/VV/attitude"`:

```python-repl
>>> sentinel1.open_dataset(product_path, group="IW1/VV/orbit")
<xarray.Dataset>
Dimensions:       (axis: 3, azimuth_time: 17)
Coordinates:
  * azimuth_time  (azimuth_time) datetime64[ns] 2021-04-01T05:25:19 ... 2021-...
  * axis          (axis) int64 0 1 2
Data variables:
    position      (axis, azimuth_time) ...
    velocity      (axis, azimuth_time) ...
Attributes: ...
    reference_system:           Earth Fixed
    constellation:              sentinel-1
    platform:                   sentinel-1b
    instrument:                 ['c-sar']
    sat_orbit_state:            descending
    sat_absolute_orbit:         26269
    ...                         ...
    xs_instrument_mode_swaths:  ['IW1', 'IW2', 'IW3']
    group:                      /IW1/VV/orbit
    Conventions:                CF-1.8
    title:                      Orbit information used by the IPF during proc...
    comment:                    The dataset contains a sets of orbit state ve...
    history:                    created by xarray_sentinel-...

```

### Attitude and calibration datasets

For attitude data use `group="IW1/VV/attitude"`:

```python-repl
>>> sentinel1.open_dataset(product_path, group="IW1/VV/attitude")
<xarray.Dataset>
Dimensions:       (azimuth_time: 25)
Coordinates:
  * azimuth_time  (azimuth_time) datetime64[ns] 2021-04-01T05:26:24.750001 .....
Data variables:
    q0            (azimuth_time) float64 ...
    q1            (azimuth_time) float64 ...
    q2            (azimuth_time) float64 ...
    q3            (azimuth_time) float64 ...
    wx            (azimuth_time) float64 ...
    wy            (azimuth_time) float64 ...
    wz            (azimuth_time) float64 ...
    pitch         (azimuth_time) float64 ...
    roll          (azimuth_time) float64 ...
    yaw           (azimuth_time) float64 ...
Attributes: ...
    constellation:              sentinel-1
    platform:                   sentinel-1b
    instrument:                 ['c-sar']
    sat_orbit_state:            descending
    sat_absolute_orbit:         26269
    sat_relative_orbit:         168
    ...                         ...
    xs_instrument_mode_swaths:  ['IW1', 'IW2', 'IW3']
    group:                      /IW1/VV/attitude
    Conventions:                CF-1.8
    title:                      Attitude information used by the IPF during p...
    comment:                    The dataset contains a sets of attitude data ...
    history:                    created by xarray_sentinel-...

```

and for calibration data use `group="IW1/VV/calibration"`:

```python-repl
>>> sentinel1.open_dataset(product_path, group="IW1/VV/calibration")
<xarray.Dataset>
Dimensions:       (line: 30, pixel: 542)
Coordinates:
  * line          (line) int64 -1042 -556 91 577 ... 13042 13688 14175 14661
  * pixel         (pixel) int64 0 40 80 120 160 ... 21520 21560 21600 21631
Data variables:
    azimuth_time  (line) datetime64[ns] ...
    sigmaNought   (line, pixel) float64 ...
    betaNought    (line, pixel) float64 ...
    gamma         (line, pixel) float64 ...
    dn            (line, pixel) float64 ...
Attributes: ...
    constellation:              sentinel-1
    platform:                   sentinel-1b
    instrument:                 ['c-sar']
    sat_orbit_state:            descending
    sat_absolute_orbit:         26269
    sat_relative_orbit:         168
    ...                         ...
    xs_instrument_mode_swaths:  ['IW1', 'IW2', 'IW3']
    group:                      /IW1/VV/calibration
    Conventions:                CF-1.8
    title:                      Calibration coefficients
    comment:                    The dataset contains calibration information ...
    history:                    created by xarray_sentinel-...

```

### Open a swath and polarisation dataset

Finally the measurement data is found in a swath / polarisation groups accessed for
example as `group="IW1/VV"` for the VV polarisation of the first IW swath:

```python-repl
>>> sentinel1.open_dataset(product_path, group="IW1/VV")
<xarray.Dataset>
Dimensions:           (pixel: 21632, line: 13509)
Coordinates:
  * pixel             (pixel) int64 0 1 2 3 4 ... 21627 21628 21629 21630 21631
  * line              (line) int64 0 1 2 3 4 5 ... 13504 13505 13506 13507 13508
    slant_range_time  (pixel) float64 0.005343 0.005343 ... 0.005679 0.005679
Data variables:
    measurement       (line, pixel) complex64 ...
Attributes: ...
    number_of_bursts:            9
    lines_per_burst:             1501
    azimuth_time_interval:       0.002055556299999998
    bursts_first_azimuth_times:  ['2021-04-01T05:26:24.209990', '2021-04-01T0...
    constellation:               sentinel-1
    platform:                    sentinel-1b
    ...                          ...
    sar_product_type:            SLC
    xs_instrument_mode_swaths:   ['IW1', 'IW2', 'IW3']
    group:                       /IW1/VV
    subgroups:                   ['gcp', 'orbit', 'attitude', 'calibration']
    Conventions:                 CF-1.8
    history:                     created by xarray_sentinel-...

```

## Xarray integration

Starting with Xarray v0.18.0, xarray-sentinel will be automatically available as
an Xarray backend:

```python-repl
>>> import xarray as xr
>>> ds = xr.open_dataset(product_path, engine="sentinel-1")

```

## Project badges

[![on-push](https://github.com/bopen/xarray-sentinel/actions/workflows/on-push.yml/badge.svg)](https://github.com/bopen/xarray-sentinel/actions/workflows/on-push.yml)
[![codecov](https://codecov.io/gh/bopen/xarray-sentinel/branch/main/graph/badge.svg?token=OLw9it0i18)](https://codecov.io/gh/bopen/xarray-sentinel)

## Contributing

The main repository is hosted on GitHub,
testing, bug reports and contributions are highly welcomed and appreciated:

https://github.com/bopen/xarray-sentinel

Lead developer:

- [Aureliana Barghini](https://github.com/aurghs) - [B-Open](https://www.bopen.eu/)

Main contributors:

- [Alessandro Amici](https://github.com/alexamici) - [B-Open](https://www.bopen.eu/)
- [Corrado Avolio](https://github.com/corrado9999) - [e-GEOS](https://www.e-geos.it/)

See also the list of [contributors](https://github.com/bopen/xarray-sentinel/contributors) who participated in this project.

## License

```
Copyright 2021, B-Open Solutions srl and the xarray-sentinel authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
