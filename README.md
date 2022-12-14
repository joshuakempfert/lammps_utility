# lammps_utility
Python For Engineers: Project

## Description
This package automates common data analysis tasks for the molecular dynamics software LAMMPS. Namely, this package offers a parser for reading LAMMPS therodynamic data from log files, a dump file parser/manipulator, and an interactive GUI implementing basic plotting features. 


## Download Instructions

1) Download folder - 'lammps_utility'
2) Ensure all Packaged Files are in the same working directory as specified below
3) Set environment using 'XXXXX.yaml' files
4) Open 'XXXX.ipynb'. Utilize the Jupyter Notebook as an instructional on how to use the package.

## Files 
- 'example.log': Example log file to test notebook functionality on
- 'example.dump': Example dump file to test notebook functionality on
- 'example.ipynb': Example notebook
- 'lammps_utility': lammps_utility python package
 	- 'thermo_reader.py': Package for extracting information from .log file and plotting to Plotly
 	- 'data_gui.py': Program for generating GUI with plotting features
 	- 'units_info.yaml': !!!!JOSHUA - WRITE DESCRIPTION!!!!
	- 'dump_reader': Subpackage for parsing and manipulating LAMMPS dump files
		- 'box.py': Internal module implementing Box class
		- 'common.py': Internal module containing some utilities
		- 'ovito_tool.py': Internal module containing Ovito interfacing
		- 'snapshot.py': Internal module implementing Snapshot class
		- 'snapshots.py': Internal module implementing Snapshots class
		- 'sources.py': Internal module for parsing LAMMPS dump file format
		- 'visualize.py': Internal module implementing Ovito view window
	- Subfolder: 'GUI_figures': Includes Images Displayed in GUI
		- 'background.png': GUI Background Image
		- 'Happy Holidays.png': Initial Image Displayed on GUI Main Screen
		- 'logo.gif': GIF of LAMMPS logo
## Basic Usage
The documentation here is not exhaustive, but rather an overview of the most common features and functions. Refer to the docstrings for complete documentation.

### dump_reader

dump_reader implements parsing and manipulation of LAMMPS dump files through its Snapshot and Snapshots objects. These should be your only point of interface with the module, as shown. Snapshots and Snapshot are implemented to minimize RAM usage so that large dump files can be easily manipulated. Per-atom data is only loaded into memory when it is needed.

```python
from lammps_utility.dump_reader import Snapshot, Snapshots
```

A Snapshot object, as its name suggests, is a snapshot of the system and its atoms. A Snapshots object is a container of Snapshot objects. A Snapshots object has a fixed collection of Snapshot objects, which it always owns. To create a Snapshots object from a LAMMPS dump file, call the `from_dump` constructor:

```python
snapshots = Snapshots.from_dump("example.dump")
```

Snapshots objects can be sliced to obtain the underlying Snapshot objects as tuples

```python
snapshot_object = snapshots[0]
snapshot_objects = snapshots[0:10]
```

Further, a new Snapshots object can be sliced:

```python
reversed_snapshots = snapshots.new[::-1]
```

Snapshots can be summed with a Snapshot object or another Snapshots object

```python
snapshots_sum = snapshots + snapshots

snapshot_sum = snapshots + snapshots[0]
```

The Snapshot object collects relevant data, including the timestep and box information, which may be edited. However, per-atom data cannot be directly edited. Rather, the Snapshot must be converted to another form (e.g. atomman System) and then converted back.

```python
snapshot.timestep = 10
snapshot.box.bounds[0] = (-10, 10)
```

This data is conglomerated by Snapshots, so it may be edited on the Snapshots level.

```python
snapshots.timesteps = 0 # Assign 0 to all timesteps
snapshots.boxes.bounds *= 2
```


Snapshot objects implement a dict-like object called `custom` where custom global properties can be defined and will be stored and read from LAMMPS dump files. `custom` will contain any custom global data from dump files read in. New custom data should be created at the Snapshots level:


```python
snapshots.custom["my_property"] = 0 # Initialize property as 0 for all snapshots

# The following are equivalent:
snapshots[0].custom = 1
#
snapshots.custom["my_property"][0] = 1
```

Snapshot objects can be visualized in an interactive window if the Ovito module is installed:

```python
snapshot.render()
```

A Snapshot object can be converted to an Atomman system object. Note that Atomman system objects do not track timestep or custom data.

```python
system = snapshot.to_atomman()
```

To convert an Atomman system object to a snapshot:

```python
new_snapshot = Snapshot.from_atomman(system, timestep = 0, custom = None)
```

Finally, Snapshots objects can be written to a LAMMPS file:

```python
snapshots.write_dump("MyPath.dump")
```


## Creators
Joshua Kempfert, Alan Smith, Matthew Nguyen


