# Demonstration of `soapy`, an end-to-end Monte Carlo AO simulation in python

## Introduction 

This repository contains example config files for soapy, allowing you to explore some of the functionality. You are encouraged to modify the files to see what happens! 

## Installation 

Assuming you already have a python environment up and running, it is advised you get the latest version of soapy from [github](https://github.com/AOtools/soapy). Once you have cloned/downloaded the code, navigate to the soapy directory and run `python setup.py install`. After this you should have a new command line tool `soapy` that will run a simulation. Documentation is [here](https://soapy.readthedocs.io/).

## Running the simulations 

It is recommended to run the files in graphical user interface (GUI) mode, so that you can easily see what is going on. This is done with the following command 
```
soapy --gui config_file.yaml
```
replacing `config_file` with the name of the config file you wish to run. This should open a soapy GUI window which you can use to control the simulation. 

When the GUI first opens, soapy will go through an initialisation process. When the text at the top left of the GUI reads "Initialisation Complete!", you can run the simulation.  

The most important GUI controls are
- **Run!**, starts or restarts the simulation 
- **Stop**, stops the simulation
- **Make IMat**, makes or loads an interaction matrix. This must be pressed before **Run!** if you have DMs in your simulation.

## The config files 

Each config file is listed below, with a brief description and instructions. It is recommended to go through in this order as each builds upon the previous.

### workshop01_uncorrected.yaml 

Simple simulation of a single turbulent layer, including an 8x8 Shack-Hartmann wavefront sensor and two science cameras, imaging at $\lambda=600$ nm and $\lambda = 1550$ nm. 

Click **Run!** in the GUI to start the simulation. You should see the turbulent phase translating across the telescope pupil in the *WFS Phase* and *Residual Phase* displays (top right of the GUI). Since we have no AO system yet, the residual phase will be identical to the WFS phase. 

Below this you should see the Shack-Hartmann spots moving, and the two science cameras with quite different PSFs, due to their different wavelengths. You can switch between instantaneous PSFs and long exposure using the buttons above. 

On the left of the GUI, a display of strehl ratio with each simulation iteration is shown for each science camera. 

### workshop02_uncorrected_multilayer.yaml 

The only difference between this and the first example is the inclusion of multiple layers in the atmopshere. You can see this by comparing the `Atmosphere` sections of the files. 

When you **Run!** this simulation, you will see a very similar picture however the phase seems more random, as you have multiple phase screens translating in different directions. This is a much more realistic model of atmospheric turbulence. 

### workshop03_tiptilt.yaml 

Here, we add a tip/tilt mirror into the simulation, see the `DM` section of the config file. 

Since we now have a phase correcting element in our simulation, before running we must make an interaction matrix, which will be inverted by our reconstructor into a command matrix, that converts WFS slopes into commands for our DM. Click the **Make IMat** button to do this. Since there are only two DM actuators (tip and tilt), this will be very fast. 

Clicking **Run!**, you will now see that the residual phase has the tip/tilt removed, and the shape of the tip/tilt mirror in the top left of the GUI. Our longer wavelength science PSF has been fairly well stabilised, greatly increasing our strehl ratio. For the shorter wavelength, we still have significant speckling, so we must add higher order correction!

### workshop04_SCAO.yaml

This example replaces the tip/tilt mirror of the previous example with an 9x9 piezo DM. We now have a fully working high(ish) order AO system. 

Clicking **Make IMat** in this example, depending on the speed of your machine you may be able to see what is happening in the interaction matrix process. Each DM actuator is "poked", and the WFS spots move. This movement is recorded in the interaction matrix for each poke. You can press the **Make IMat** button again if you missed it. After the calibration process has completed, you can view the interaction matrix by using the ipython console in the bottom left of the GUI. Type in the following commands:
```
%pylab # for plotting 
imshow(sim.recon.interaction_matrix)
```

Click **Run!**, and you should see a significant improvement from the previous config file. The DM is providing a much higher order correction as shown in the top right. Both science PSFs are now almost diffraction limited, and this is reflected in the strehl ratio. Congratulations!

### workshop05_SCAO_aniso.yaml 

This example adds two more science cameras at the same wavelengths, but 10 arcseconds away from the optical axis (see the `Position` field of each entry in the `Science` section of the config file). Crucially, our WFS is still observing on-axis, so these new science cameras will experience angular anisoplanatism as the turbulence is different along these different lines of sight. 

Click **Make IMat** as before then **Run!**. We see that the shorter wavelength PSF is significantly degraded, with the longer wavelength less so. 


