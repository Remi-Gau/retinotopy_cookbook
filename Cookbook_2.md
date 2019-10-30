# Freesurfer Cookbook 2 – basic reconstruction

This cookbook assumes you have installed Freesurfer. If you haven’t, follow [cookbook 1](Cookbook_1.md)! You will need a structural image to do this part.

1.  In the `/subjects` directory of Freesurfer, create a directory for your new subject (for this example, let’s assume it’s named `001`) and a subdirectory `/mri`. Put the T1-weighted anatomical in there.

2.  Your anatomical image may have a .mat file associated with it if you have been busy coregistering it to functional data or other data. If it doesn’t then just go to step 4. This needs to be taken account of before proceeding, as Freesurfer neither reads nor takes account of .mat files. But you don’t want to just delete it, because that will just eliminate your nice coregistration with the functional data.

3.  To take account of the .mat file, start Matlab and SPM and set up a `Coreg --> Reslice`. Then you need to select the ‘Image Defining Space’ as your structural and the ‘Images to Reslice’ as exactly the same structural. Run this and your structural will be resliced into a new structural (if the original was `AD.nii` then the new one will be `rAD.nii`) but – crucially – without a .mat file. This resliced structural will be the one you will work with in Freesurfer.

4.  Convert your structural into .mgz format. In a terminal window issue

```
mri_convert rAD.nii 001.mgz
```

(assuming in this example the image you want to use is `rAD.nii` and the subject directory is `001`). It is really very important that you use the same name for the `001.mgz` file as for the directory in which it is contained. Make sure the `001.mgz` image exists and place it in the `mri` subdirectory of that subject directory.

5. Now you can reconstruct! The super-lazy way is to do all steps at once by typing

```
recon-all –autorecon-all –subject 001
```

You can also specify the three individual steps by replacing the `–autorecon-all` flag with
-   `–autorecon1` (for the skull strip)
-   `–autorecon2` (for the subcortical segmentation through make final surfaces)
-   `–autorecon3` (spherical morph and automatic cortical parcellation).

6. Go away overnight. Come back and it should be cooked.

7. Visualise output using a command like

```
tksurfer 001 lh pial
```

or

```
tksurfer 001 lh inflated
```

8. There are quite a few problems that might arise. For a general tutorial on troubleshooting them see this [tutorial](http://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/OutputData) (which assumes you have downloaded the [tutorial data](http://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/Data)) but can easily be adapted for your data if you just substitute your subject ID in the command line commands given. Alternatively, have a look at [Cookbook 3](Cookbook_3.md).
