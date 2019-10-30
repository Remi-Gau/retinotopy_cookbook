# Freesurfer Cookbook 6 – Phase-encoded retinotopic mapping

This tutorial explains how to display phase-encoded maps in Freesurfer. It assumes that the time series of your retinotopic mapping scan has been subjected to Fourier analysis and the power of the stimulus cycle frequency has been extracted.

This leaves you with a volume of complex numbers. You need to separate the imaginary and real components of these numbers and save them as separate maps. These can be interpreted as Y and X, respectively, of the phase angle in Cartesian coordinates (or as the sine and cosine part of the modulation function). Thus, the phase angle and the magnitude (power) are given by:

```matlab
	phase = atan2 ( Y, X );
	magnitude =	sqrt ( X.^2 + Y.^2 );
```

None of this is terribly important, however, as Freesurfer will conveniently do the calculations for you, and you can threshold based on the magnitude. Here's how you display the maps:

1. Open the surface mesh and overlay the imaginary component (here called `Ret_imag.nii`), for example by calling:

```bash
tksurfer 001 lh inflated -overlay Ret_imag.nii
```

2. In Freesurfer then load the real component by `File --> Load Overlay` into Overlay Layer 2. Pick the map and the associated `register.dat` (because `tksurfer` forgets about it otherwise).

3. Next, you need to set up the overlay for displaying complex data. Go to `View --> Configure --> Overlay` then check the buttons RYGB Wheel (this is the colour scheme) and Complex (this calculates the phase from the two overlays).

You should now see a nicely coloured map. The transparent bits are near-threshold voxels. You can change the threshold just as with a normal SPM t-map.

4. The map might be enough for some applications, but the colours cycle only once through all 360 degrees of phase. In order to allow you to map higher visual areas, you need to change the precision of the colour scheme.

Go to `View --> Configure --> Phase Encoded Data Display` and change `Angle Cycles` to something higher than 1. I went with 2, which means that there is a green-red-blue range of colours for each cortical hemisphere. So far I have not quite figured out how the colours map onto phases, but you can delineate borders by mirror reversals.

You may want to smooth the map by applying `Tools --> Surface --> Smooth Overlay` with 4-8 or so smoothing steps. You may want to apply this to both overlay layers. For my high-resolution data I had actually smoothed the data in SPM already, and only applied 4 smoothing steps on the surface. That may not be the best course of action, although as the example PDF shows, the maps can be quite nice.

5. The resulting map should look similar to those in the examples PDF. Now you can delineate visual areas along the lines where the phase angle reverses.
