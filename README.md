gFlex
=====

***Multiple methods to solve elastic plate flexure, designed for applications to Earth's lithosphere.***

These instructions are meant to take an user familiar with computers but new to (or a beginner with) Python through the basics of how to get gFlex to work. The Python scripting part towards the end should be pretty straightforward as well, insofar as information is provided on how to get and set the chosen values inside gFlex. *Please leave a message if you have trouble working with gFlex; your comments could assist both you and the more general improvement of this documentation.*

## Download and Installation

#### Python

gFlex has been tested on **Python 2.7**, and should work (with a few possible changes) on future versions of Python 2.X. It has not been tested on Python 3.X.

In order to run properly, gFlex requires the following Python dependencies:
* numpy
* scipy
* matplotlib
* setuptools
* pip (optional)

*For users who are new to Python, follow these directions to install the Python interpreters onto your computer.*

###### Linux

Use your package manager to download and install the required Python packages. For Debian/Ubuntu, it will be something like:

```
# Basic packages
sudo apt-get install python python-numpy python-scipy python-setuptools python-matplotlib 

# Pip (recommended for automatic installs via setuptools)
python-pip

# iPython console -- very useful (optional)
sudo apt-get install ipython

# Sypder IDE (I don't personally use it but many others like it: optional)
sudo apt-get install spyder
```

###### Windows

Download [**python(x,y)**](https://code.google.com/p/pythonxy/wiki/Downloads) or another full-featured distribution such as **Anaconda**; both of these distributions have been tested successfully with gFlex. Python(x,y) and several others also contain the required packages (including the numerical libraries), the iPython console, and the Spyder IDE; [**Spyder**](https://code.google.com/p/spyderlib/) is a nice IDE that will provide a familiar-looking interface for users accustomed to Matlab.

###### Mac

The current recommendation is to use a package manager like [**homebrew**](http://brew.sh/). With this you can install Python, and then move on to using **pip** to install the Python modules. A good introduction to this can be found here: http://www.thisisthegreenroom.com/2011/installing-python-numpy-scipy-matplotlib-and-ipython-on-lion. See the **Linux** instructions for the list of packages that you will need; after installing pip, these commands can be substituted as follows, e.g.,
```
# OLD
sudo apt-get install python-numpy
# NEW
pip install numpy
```

Recent efforts to download Python distributions (both **Anaconda** and **Enthought**) have not met with success with both gFlex and GRASS, though **Anaconda** has been tested successfully with Windows. As a result, it should be more successful to keep the Python packages managed better by something like **homebrew** with **pip**.

##### Setuptools and ez_setup (Windows and Mac with distributions)

The distributions for Mac and Windows do not come with setuptools, which is required to install gFlex. However, if you install ez_setup, the gFlex install script will automatically install setuptools for you. Simply type:
```
pip install ez_setup # Windows or Mac without special privileges required
sudo pip install ez_setup # Mac where sudo privileges are required
```
Of course, one can also bypass the need for the install script to install setuptools by using pip preemptively:
```
pip install setuptools # Windows or Mac without special privileges required
sudo pip install setuptools # Mac where sudo privileges are required
```

#### gFlex

##### Downloading and Installing in One Step from PyPI using pip

gFlex is downloadable from the Python Package Index ([PyPI](https://pypi.python.org/pypi)); see https://pypi.python.org/pypi/gFlex.

If you have **pip**, you may simply type:
```
pip install 
pip install gflex
# Or if the destination install folder requires sudo access
# (for UNIX-like systems)
sudo pip install gflex
# pip install gFlex works too -- install is caps-insensitive
```
and you will have a full, running copy of the latest release version of gFlex.

##### Downloading

gFlex may be downloaded here at GitHub, by either:
* Copying the link at right and pasting it into the command prompt as follows:
```
git clone <LINK>
```
* Downloading and extracting the compressed ZIP file (link at right)
* Clicking on the link to add gFlex to your local GitHub desktop app (for Windows or Mac)

# Installing

Install gFlex at the command prompt using [setuptools](https://pypi.python.org/pypi/setuptools). If you have administrator privileges, which *is often also the case when doing this install under Windows*, you may drop the "sudo". For standard Linux or Mac users, the "sudo" will remain necessary, and you will have to enter your administrator password for the program to be added to your local set of applications (e.g., as "/usr/local/bin/gflex").

```
# For standard Linux/Mac users:
sudo python setup.py install
# OR
sudo python setup.py develop # If you want the install to see instantly
                             # any changes made in the source repository

# For Windows users or Unix-type users with SuperUser privileges:
python setup.py install
# OR
python setup.py develop # If you want the install to see instantly
                        # any changes made in the source repository
```

## Running

Once gFlex is installed, it is possible to run it in five ways:
 1. With a configuration file
 2. Within a Python script
 3. Within GRASS GIS
 4. As part of the Landlab Earth-surface modeling framework
 5. As a coupled component of a set of models via the Community Surface Dynamics Modeling System [Component Model Interface (CMI)](http://csdms.colorado.edu/wiki/CMI_Description)

For options 1 and 2, there are pre-built methods that can be selected along the way to visualize results. These use Python's Matplotlib plotting library. For option 3, GRASS GIS is used for visualization. In Option 4, output from CSDMS sets of models can be visualized using tools such as [VisIt](https://wci.llnl.gov/simulation/computer-codes/visit/) ([CSDMS page about VisIt](http://csdms.colorado.edu/wiki/CMT_visualization)) and [ParaView](http://www.paraview.org/). ParaView also now has [Python bindings](http://www.paraview.org/python/), which can further be used to visualize outputs produced with any of these methods.

#### With configuration file

A configuration file can be generated to run gFlex; see examples in the **input/** directory. To run gFlex using this file, one simply opens a terminal window and types:

```
# run like this:
gflex <path-to-configuration-file>
```

For help constructing configuration files, see the blank template files **input/template1D** and **input/template2D**, as well as the other examples found in the **input/** directory. The **input/** directory also contains **input/README.md**, which provides a further local description of the files available. **input/input_help** provides a longer explanation of what the parameters are, and is therefore reproduced immediately below for reference:

```
; input_help
; All units are SI. Not all entries are needed.
; Standard parameter values for Earth are included.

[mode]
dimension=2 ; 1 (line) or 2 (surface) dimensions
method=SPA ; Solution method: FD (Finite Difference), FFT (Fast Fourier 
;          ; Transform, not yet implemented), SAS (Spatial domain analytical 
;          ; solutions), or SAS_NG (SPA, but do not require a uniform grid
;          ; - NG = "no grid")
;          ; For SAS_NG, 1D data must be provided and will be returned in 
;          ; two columns: (x,q0) --> (x,w). 2D data are similar, except
;          ; will be of the form (x,y,[q0/in or w/out])
;          ; I am working on gridded output for these, so this might change
;          ; in the future.
;          ; Both the FFT and SPA techniques rely on superposition 
;          ; of solutions, because they can be combined linearly, whether in 
;          ; the spectral or the spatial domain)
;
PlateSolutionType=vWC1994 ; Plate solutions can be:
;                         ; * vWC1994 (best), or
;                         ; * G2009 (from Govers et al., 2009; not bad, but not 
;                         ;          as robust as vWC1994)

[parameter]
YoungsModulus=65E9
PoissonsRatio=0.25
GravAccel=9.8
MantleDensity=3300
InfillMaterialDensity=0 ; This is the density of material (e.g., air, water) 
;                       ; that is filling (or leaving) the hole that was 
;                       ; created by flexure. If you do not have a constant 
;                       ; density of infilling material, for example, at a 
;                       ; subsiding shoreline, you must instead iterate (see
;                       ; [numerical], below).

[input]
; space-delimited array of loads
; stresses (rho*g*h) if gridded (dx (and if applicable, dy) will be applied
;   to convert them into masses
; forces (rho*g*h*Area) if not gridded (SAS_NG)
; If the solution method (above) is selected as "SAS_NG", then this file
; will actually be of the format (x,[y],q0) and the code will sort it out.
; (Once again, working on a gridded output option for ungridded inputs)
Loads=q0_sample/2D/central_square_load.txt
;
; scalar value or space-delimited array of elastic thickness(es)
; array used for finite difference solutions
ElasticThickness=Te_sample/2D/10km_const.txt
;
; xw and yw are vectors of desired output points for the SAS_NG method.
; If they are not specified and a SAS_NG solution is run, the solution will be 
; calculated at the points with the loads.
; they are ignored if a different solution method is chosen.
xw=
yw=

[output]
; DeflectionOut is for writing an output file. 
; If this is blank, no output is printed.
; Otherwise, a space-delimited ASCII file of 
; outputs is with this file name (and path).
DeflectionOut=tmpout.txt
;
; Acceptable inputs to "Plot" are q0 (loads), w (deflection), or both; any 
; other entry here will result in no plotting.
; Automatically plots a 1D line or 2D surface based on the choice 
; of "dimension" variable in [mode]
Plot=both

[numerical]
GridSpacing_x= ; dx [m]
;
; Boundary conditions can be:
; (FD): 0Slope0Shear, 0Moment0Shear, Dirichlet0, Mirror, or Periodic
; For SAS or SAS_NG, NoOutsideLoads is valid, and no entry defaults to this
BoundaryCondition_West=
BoundaryCondition_East=
;
; Solver can be direct or iterative
Solver=
;
; If you have chosen an iterative solution type ("Solver"), it will iterate
; until this is the difference between two subsequent iterations.
; Set as 0 if you don't want to iterate
convergence=1E-3 ; Tolerance between iterations [m]

[numerical2D]
GridSpacing_y= ; dy [m]
;
; Boundary conditions can be:
; (FD): 0Slope0Shear, 0Moment0Shear, Dirichlet0, Mirror, or Periodic
; For SAS or SAS_NG, NoOutsideLoads is valid, and no entry defaults to this
BoundaryCondition_North=
BoundaryCondition_South=
; 
; Flag to enable lat/lon input. By default, this is false
latlon= ; true/false: flag to enable lat/lon input. Defaults False.
PlanetaryRadius= ; radius of planet [m], for lat/lon solutions

[verbosity]
Verbose= ; true/false. Defaults to True.
Debug= ; true/false. Defaults to False.
Quiet= ; true/false -- total silence if True. Defaults to False.
```

#### Within a Python script (with or without a configuration file)

You may run gFlex from other Python programs. When you install it (above), this also produces a Python module that you may import to access it while scripting.

##### With no configuration file
**gflex/input/run_in_script_2D.py**, reproduced below, is a good example of how to set the variables and run the model. This method requires no input file, as all of the values are set inside the Python script that imports gflex. This is essentially how the GRASS GIS interface was written, and is a way to embed the abilities of gFlex into another model. A one-dimensional example, **gflex/input/run_in_script_1D.py**, is also available.

```
import gflex
import numpy as np
from matplotlib import pyplot as plt

flex = gflex.F2D()

flex.Quiet = False

flex.Method = 'FD'
flex.PlateSolutionType = 'vWC1994'
flex.Solver = 'direct'

flex.g = 9.8 # acceleration due to gravity
flex.E = 65E9 # Young's Modulus
flex.nu = 0.25 # Poisson's Ratio
flex.rho_m = 3300. # MantleDensity
flex.rho_fill = 0. # InfiillMaterialDensity

flex.Te = 35000. # Elastic thickness -- scalar but may be an array
flex.qs = np.zeros((50, 50)) # Template array for surface load stresses
flex.qs[10:40, 10:40] += 1E6 # Populating this template
flex.dx = 5000.
flex.dy = 5000.
flex.BC_W = 'Dirichlet0' # west boundary condition
flex.BC_E = '0Moment0Shear' # east boundary condition
flex.BC_S = 'Periodic' # south boundary condition
flex.BC_N = 'Periodic' # north boundary condition

flex.initialize()
flex.run()
flex.finalize()

# If you want to plot the output
flex.plotChoice='both'
# An output file could also be defined here
# flex.wOutFile = 
flex.output() # Plots and/or saves output, or does nothing, depending on
              # whether flex.plotChoice and/or flex.wOutFile have been set
```

##### With a configuration file

If you would like to use a Python script with a configuration file, this is also possible.

```
import gflex

# To use a configuration file:
filename = '../gflex/input/input_f1d_test' # it works for usage (1) and (2)
obj = gflex.WhichModel(filename)

## SET MODEL TYPE AND DIMENSIONS HERE ##
########################################
if obj.dimension == 1:
  obj = gflex.F1D(filename)
elif obj.dimension == 2:
  obj = gflex.F2D(filename)

# Then run the code!
obj.initialize(filename)
obj.run()
obj.finalize()

# Standalone plotting output if you so desire
flex.plotChoice='w'
obj.output()
```


#### Within GRASS GIS

To run gFlex inside of GRASS GIS 7, run the following commands from within a GRASS GIS session:

```
g.extension r.flexure
g.extension v.flexure
```

This will reach into the GRASS GIS subversion repository, download the source code, and install the packages. These are stored at and have help files located at, respectively:

* **r.flexure**
** Source: http://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.flexure
** Manual page (HTML): http://grass.osgeo.org/grass70/manuals/addons/r.flexure.html
* **v.flexure**
** Source: http://trac.osgeo.org/grass/browser/grass-addons/grass7/vector/v.flexure
** Manual page (HTML): http://grass.osgeo.org/grass70/manuals/addons/v.flexure.html

When running **r.flexure**, it is important to ensure that the elastic thickness map is at or properly interpolated to the computational region (**g.region**) resolution before solving. A nearest-neighbor interpolated Te map will cause perceived gradients in elastic thickness to be very sharp, and this will strongly affect (and misdirect) the flexural solutions.

#### As part of Landlab

Landlab is an in-development (but nearing release) Earth-surface modeling framework built to facilitate easy integration of geomorphic, ecological, hydrological, geological, etc. Earth-surface related models to simulate and investigate the links between multiple processes. gFlex can be linked with Landlab, and the code to do this is available within the Landlab repository at https://github.com/landlab/landlab/tree/master/landlab/components/gFlex.

#### As part of the CSDMS CMI

To run gFlex within the CSDMS Component Model Interface (CMI) environment, see **gflex_bmi.py**. The Landlab interface to gFlex (above) also provides gFlex with a CSDMS CMI interface, making this local CMI interface redundant, but useful if one does not want to install the entire Landlab modeling framework (https://github.com/landlab/landlab).
