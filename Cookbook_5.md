# Freesurfer Cookbook 5 – making ROIs
Version 1.0, March 2008
Geraint Rees (g.rees@fil.ion.ucl.ac.uk)

This cookbook assumes you have installed Freesurfer, got hold of a FIL structural and completed the steps in Cookbook 2 (generating surfaces etc) and Cookbook 3 (rudiments of how to use tkmedit and tksurfer) and Cookbook 4 (knowing how to make an overlay). This Cookbook is all about how to make ROIs (yay!).

The Cookbook assumes there are going to be two basic circumstances when making an ROI. Either you are going to want to take the cortical parcellation (or subcortical segmentation) produced by Freesurfer and make an anatomical ROI from that for use in SPM (e.g. small volume correction for the fusiform gyrus). Or you are going to want to do retinotopic mapping e.g. overlay meridians on a flattened structural, draw the boundaries of V1, V2d, V2v etc and then export to SPM.

Anatomical ROIs from parcellation

    1. The automatic cortical parcellation produces annotations that can be converted into labels (a label is a set of surface vertices forming an ROI: see point 5 below under ‘Flattening, overlays and retinotopy’). Use for example:

mri_annotation2label --subject 002 --hemi lh --outdir ./trial_labels

to take subject 002 and create a whole load of labels in a subdirectory of that subject directory that you have created called ‘trial_labels’. The resultant labels should have meaningful names.

    2. The new labels you have created are anatomical ROIs defined in surface coordinates. You can visualise them by starting tksurfer e.g.

tksurfer 002 lh inflated

and then using File -> Label -> Load Label you can load in specific labels from the subdirectory you just created in step 1.

In the subject directory there is a subdirectory ./label that contains a file aparc.annot.a2005s.ctab. This appears to contain the names and numbers of the labels (see below). There are 35 labels in this file and they correspond to the label names created in 20 above.

    3. To make one or more of the labels (collection of surface points) into an ROI (three dimensional structure) you need to use mri_label2vol. Try reading the help first

mri_label2vol --help

You then need a command like

mri_label2vol --label ./trial_labels/lh.corpuscallosum.label --temp ./stats/spmT_0003.img  --reg stats/register.dat --fillthresh .5 --o cent_lh_000.bshort

Here the variable stuff if the path after –label (I’m trying to create an ROI from the callosal label), the stuff after –temp (a template image which is basically the bounding box in which the ROI will be created; here I’m using a stats volume which is from the functional data in the same space as the anatomical data), the stuff after –reg (a register.dat file; see Cookbook 4) and the stuff after –o (this is the name of the output file). This will produce an output bshort image/images

    4. The final step is to use mri_convert to take the bshort images and make an Analyze format image. In this worked example use

mri_convert  cent_lh_000.bshort.hdr callosum.img

    5. In Matlab and SPM you can now use the ‘check reg’ button to check the correspondence between this ROI and the original T1 image used for putting into Freesurfer in the first place. There should be an excellent correspondence, and you can now use this ROI for SVCs or extracting betas in SPM.

Flattening, overlays and retinotopy

    1. You will first need to cut up your inflated brain and flatten it. A skeleton tutorial is available at http://surfer.nmr.mgh.harvard.edu/fswiki/FreeSurferOccipitalFlattenedPatch and follow the instructions for cutting the occipital surface. Start tksurfer and visualise an inflated hemisphere with the curvature overlaid onto it (see Cookbook 3 for details). Then make the cuts. You basically put points on the surface by clicking where you want to place points, then join the two points you’ve placed by using the tksurfer control panel – the icons at the left hand of the second row are the useful ones.

    2. Keep going with the tutorial and you should save the patch you create using File -> Patch -> Save Patch As. At this point everything is still in 3d. Now you need to flatten by using

mris_flatten –w 10 lh.occip.patch.3d lh.occip.flat.patch.3d

Again, mris_flatten –help is very helpful and replace the input and output file names as appropriate. Takes a little while (minutes for occipital lobe, hours for whole brain), depending on your CPU.

    3. Now you should have a flat patch. This is just a surface like all the other surfaces (pial, inflated) you have been playing with and so you can visualise it in tksurfer, overlay stuff on it and so on in exactly the same way. Start by visualising the patch overlaid with your stats (e.g. meridians) like this

tksurfer 002 lh inflated -patch lh.patch_flat -overlay ./stats/spmT_0003.img

In this example the subject ID is ‘002’, the flattened bit of left occipital lobe is named lh.patch_flat (it could be lh.occip.flat.patch.3d as in the example in 2) and the stats image is an spmT living in a subdirectory.

    4. You can then zoom in to the patch and proceed to draw an ROI. This is done by placing points on the surface (by clicking on the image in the tksurfer display window) and then joining them up (using the ‘MakePath’ or ‘MakeClosedPath’ icons on the second row of the tksurfer control window). Once you’ve drawn the ROI you need to fill it using the adjacent ‘CustomFill’ button that will bring up a dialog box where you need to fill ‘Up to and including paths’. This should (if you’ve done it right and the fill hasn’t leaked out!) fill your ROI and you can now save it. http://surfer.nmr.mgh.harvard.edu/fswiki/TkSurferGuide_2fTkSurferWorkingWithData_2fTkSurferLabel is useful to tell you about the menu options.

    5. Use File -> Label -> SaveSelectedLabel to save your ROI as a ‘label’ preferably in the ‘label’ subdirectory of your subject directory e.g. named test_yellow.label. A label is simply a collection of vertices forming an ROI. You can read lots about them at http://surfer.nmr.mgh.harvard.edu/fswiki/TkSurferGuide_2fTkSurferWorkingWithData_2fTkSurferLabel

    6. You can check what this flatmap ROI looks like on the inflated surface by starting another tksurfer and using File -> Load label and selecting the label you just saved in step 5. It should display on the inflated surface.

    7. Now you need to convert the label (collection of vertices on a surface) to a volume (something that can be visualised in tkmedit or exported to SPM). This uses the command mri_label2vol. Issue a command like

mri_label2vol --label ./label/test_yellow.label --temp ./stats/spmT_0003.img --reg ./stats/register.dat --fillthresh .5 --o test_yellow.bshort

Again, mri_label2vol –help is very helpful. In this command we’re specifying the label (the flat ROI we just created) and after the –temp flag a template volume, which specifies the size of the box the volume ROI is going to be placed in. Here I’ve specified the stats image SPM that we made the ROI from – it’s like the ‘bounding box’. Again, we need to use a register.dat as described in earlier cookbooks. Finally, the stuff after the –o output flag tells us what the output images are going to be.

    8. This produces a whole load of files with a .bshort.hdr and .bshort images which presumably is something to do with the images. You can visualise the images that have been produced with something like

tkmedit 002 orig.mgz -overlay ./test_yellow.bshort.bhdr -overlay-reg ./stats/register.dat –fthresh .5 -fmid 1.

In this example the subject is 002, orig.mgz is the original T1 file and the test_yellow.bshort.hdr was produced in the previous step. Again, use –help for more help!

    9. The final step is to use mri_convert to take the bshort images and make an Analyze format image. In this worked example use

mri_convert test_yellow.bshort.bhdr test_yellow.img

    10. The result is a test_yellow.img. In Matlab and SPM you can now use the ‘check reg’ button to check the correspondence between this ROI and the original T1 image used for putting into Freesurfer in the first place. There should be an excellent correspondence, and you can now use this ROI for SVCs or extracting betas in SPM.
