# Freesurfer Cookbook 1 – Installing Freesurfer for SPM & retinotopy

Version 1.0, March 2008

Geraint Rees (g.rees@fil.ion.ucl.ac.uk)

This tutorial assumes familiarity with the command line and with basic Unix shell commands and directory trees. If you don’t know about the C shell then a basic tutorial is available [here](LINK MISSING). But you really don’t need to read much of that to get going.

## For Macintosh

Needs a 2GHz processor or better with 2Gb of RAM and 2.3Gb free disk space. Has been tested on 2x2.66GHz dual-core Intel Xeon with 4Gb memory and runs fine.

Go to the [freesurfer wiki](http://surfer.nmr.mgh.harvard.edu/fswiki) and download the current stable version from the ‘Software’ section. Installation instructions are [here](http://surfer.nmr.mgh.harvard.edu/fswiki/MacOsInstall).

Recommended install location is in `/usr/local/freesurfer`. This can be tricky to get to in the Finder, so suggest opening your hard drive from the Finder after installation. Then menu Go->GoToFolder and type in `/usr/local/freesurfer`. This will go to that folder; then menu File->AddToSidebar will add an alias to freesurfer in the side bar.

Drag Application->Terminal into the Dock if you haven’t already. Setup and configuration is [here](http://surfer.nmr.mgh.harvard.edu/fswiki/SetupConfiguration). This basically involves configuring some environment variables and then running the csh script `SetUpFreeSurfer`

Test the installation by typing `tkmedit bert orig.mgz`. This should open an MRI scan.

You are going to get annoyed setting the path `etc` every time you fire up a terminal window so set default tsch in the terminal Preferences menu. Then open a text editor (e.g. pico) and create a text file containing

```bash
setenv FREESURFER_HOME /usr/local/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.csh
```

and save this in `/Users/YourUserName` as a file named .csrhc.

For the new data you want to process, create a directory in `/usr/local/freesurfer/subjects` named `001` (all subject-specific data must be in named directories at this place in the directory tree for freesurfer to find it) and in a subdirectory called `./mri` put the images.

Cookbook 2 tells you how to do the first reconstruction

## For Linux

Still to be written!

## For Linux Virtual Machines running under Windows

At present doesn’t appear to work as critical OpenGL flaw
