# XORCA

master: [![Build Status](https://travis-ci.org/willirath/xorca.svg?branch=master)](https://travis-ci.org/willirath/xorca)
[![codecov](https://codecov.io/gh/willirath/xorca/branch/master/graph/badge.svg)](https://codecov.io/gh/willirath/xorca)

develop: [![Build Status](https://travis-ci.org/willirath/xorca.svg?branch=develop)](https://travis-ci.org/willirath/xorca)
[![codecov](https://codecov.io/gh/willirath/xorca/branch/develop/graph/badge.svg)](https://codecov.io/gh/willirath/xorca)

## What is this about?

XORCA brings [XGCM](https://xgcm.readthedocs.io) and
[Xarray](https://xarray.pydata.org) to the ORCA grid.  (It actually brings all
this to NEMO output.  But [xnemo was already
taken](https://github.com/serazing/xnemo).)

It allows for opening all output files from a model run into one Xarray dataset
that is understood by XGCM.  With this, grid-aware differentiation /
differencing and integration / summation is possible.


### Example: Calculate the barotropic stream function in 2 lines

After a short preamble which imports the package and loads the data:

```python
import xarray as xr
import xgcm
from xorca.lib import load_xorca_dataset

ds = load_xorca_dataset(data_files=list_of_all_model_output_files,
                        aux_files=list_of_mesh_mask_files)
grid = xgcm.Grid(ds, periodic=["Y", "X"])
```

This is all that's needed to define and calculate the barotropic stream
function for all time steps:
```
U_bt = (ds.vozocrtx * ds.e3u).sum("z_c")
psi = grid.cumsum(- U_bt * ds.e2u, "Y") / 1.0e6
```

And this triggers the actual computation and produces the image:
```
psi.mean("t").plot(size=9);
```

![barotropic stream function](doc/images/barotropic_stream_function.png)


### More examples

See the example notebook for hints on where this might end:
[notebooks/xorca_demo_ORCA05.ipynb](notebooks/xorca_demo_ORCA05.ipynb).


## Installation

First, install all dependencies (assuming you have conda installed and in the
path):
```bash
curl \
    https://raw.githubusercontent.com/willirath/xorca/master/environment.yml \
    -o xorca_environment.yml
conda env create -n xorca_env -f xorca_environment.yml
```

Then install XORCA:
```bash
source activate xorca_env
pip install git+https://github.com/willirath/xorca.git@master
```

To use, `source activate xorca_env` before, e.g., starting `jupyter notebook`.
