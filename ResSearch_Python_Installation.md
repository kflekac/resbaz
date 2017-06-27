
# Installing Python Through Miniconda

ResSearch will use a variety of available Python packages to interrogate, clean and visualise data - but be aware that these are not necessarily the only packages available for these functions. 


## Installation Instructions


### Windows Setup

1) Download the `python 3 exe installer` from https://conda.io/miniconda.html (If you are not sure and you are using windows 7, 8, 9 or 10, then download the 64-bit exe installer)

2) Run the exe installer and install using default choices.  By clicking next.  (The installation might take a few minutes if the computer is slow, you can click "Show Details" to see the installation progress.

3) When installation is complete, find and open a program called `Select Anaconda Prompt` from the Windows Start Menu. 

This will open a new window with a black background (it might keep blinking if the computer is slow).  Wait until you can start typing


4) Type `conda install jupyter pandas matplotlib basemap` and hit `ENTER` key.  When asked do you want to proceed, type `y` and hit `ENTER`.

   This will take a a few minutes to download the jupyter, pandas and matplotlib packages.

5) To view a list of packages and versions installed, or to confirm that a package has been added or removed, type `conda list`. Confirm that jupyter, pandas and matplotlib have been installed

6) The Python session of ResSearch will be using jupyter notebooks. When you open Jupyter, the directory in which you are currently located becomes the "home directory" for Jupyter. To change this home directory (preferably to C:/ drive, or your user folder), in your command line you enter  `cd C:\Users\myUsername`, or `cd <file path to navigate to>`

7) Type `jupyter-notebook` and press `ENTER` key. This will start jupyter notebook in the default browser.  It might open in Internet Explorer.  If you prefer to use Chrome or Firefox, then you need to change the windows settings to use Chrome or Firefox as the default browser. If this does not open automatically, you can copy the "local host" URL on your command line to a new browser tab. 

This instance of jupyter notebooks is running through your command line, and closing your command terminal will also terminate your Jupyter session. Alternatively, if you wish to exit Jupyter through the command line, use `ctrl-C`.

8) If you need to uninstall miniconda for any reason, you can do this through "Add or remove Program" in the control panel, by removing "Python 3.6(Miniconda)"


### OS X Setup

1) In your browser download the Python 3.6 Miniconda installer for OS X from https://conda.io/miniconda.html, then in your terminal window type the following:

`bash Miniconda3-latest-MacOSX-x86_64.sh`

2) Follow the prompts on the installer screens. If unsure about any setting, simply accept the defaults as they all can be changed later. After installation is complete, close your terminal window.

3) Re-open your terminal window, and type `conda --version` into the command terminal to test that miniconda has been installed. Conda should respond with the version number that you have installed, like: conda 3.11.0

NOTE: If you see an error message, check to see that you are logged into the same user account that you used to install Anaconda or Miniconda, and that you have closed and re-opened the terminal window after installing it.

4) Type `conda install jupyter pandas matplotlib basemap` and hit `ENTER` key.  When asked do you want to proceed, type `y` and hit `ENTER`.

5) To view a list of packages and versions installed, or to confirm that a package has been added or removed, type `conda list`. Confirm that jupyter, pandas and matplotlib have been installed

6) The Python session of ResSearch will be using jupyter notebooks. When you open Jupyter, the directory in which you are currently located becomes the "home directory" for Jupyter. To change this home directory (preferably to C:/ drive, or your user folder), in your command line you enter  `cd C:\Users\myUsername`, or `cd <file path to navigate to>`

7) Type `jupyter-notebook` and press `ENTER` key. This will start jupyter notebook in the default browser.  It might open in Internet Explorer.  If you prefer to use Chrome or Firefox, then you need to change the windows settings to use Chrome or Firefox as the default browser. If this does not open automatically, you can copy the "local host" URL on your command line to a new browser tab. 

This instance of Jupyter Notebooks is running through your command line, and closing your command terminal will also terminate your Jupyter session. Alternatively, if you wish to exit Jupyter through the terminal, use `command-C`.

8) If you need to uninstall miniconda for any reason, open a terminal window and remove the entire miniconda install directory by typing: `rm -rf ~/miniconda` 

You may also edit ~/.bash_profile and remove the miniconda directory from your PATH environment variable, and remove the hidden .condarc file and .conda and .continuum directories which may have been created in the home directory with: `rm -rf ~/.condarc ~/.conda ~/.continuum`


## After Installation

After you install Python, try running an instance of jupyter notebooks. In the first code box, trying running the following commands:

`import pandas as pd`

`import matplotlib.pyplot as plt`

If this produces an error, please try uninstalling and re-installing Python according to the previous instructions. If this doesn't fix the issue, please either:
+ Send me an email at kflekac.resplat@gmail.com before the event, or
+ Visit Hacky Hour this Thursday 29th at Tsubu Bar from 3-4pm, or
+ Speak to me or one of our helpers at the beginning of the event

In addition to the previous steps, also download the ResSearch Instructions iPython Notebook present in the https://github.com/kflekac/resbaz/, as well as the pedestrian counts data available at https://github.com/resbaz/pedestriancounts. Make sure that both of these files are located within the same folder on your computer.



```python

```
