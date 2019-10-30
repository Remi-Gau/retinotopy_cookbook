
# Freesurfer Cookbook 3 – inspecting data and basic editing

This cookbook assumes you have installed Freesurfer, got hold of a structural and completed the steps in [Cookbook 2](Cookbook_2.md). This Cookbook is all about inspecting the output and if it looks rubbish or has ‘pimples’, the basics of how to go about editing it.

## Inspecting the output

There are two basic tools in Freesurfer:
-   `tkmedit` (for looking at image volumes)
-   `tksurfer` (for looking at…surfaces!).

The `autorecon` pipeline will have generated a lot of stuff, including new directories in your subject directory.

### tkmedit

```bash
tkmedit 001 brainmask.mgz lh.white \
 -aux T1.mgz –aux-surface rh.white \
 -segmentation aseg.mgz $FREESURFER_HOME/FreeSurferColorLUT.txt
```

If your subject ID is not 001 then just substitute the right subject ID, everything else should be fine. This should bring up `tkmedit` with a coronal section of a T1 overlaid on which are some lines and some shading. The lines are the surfaces that Freesurfer found when it was making the surfaces; the shading is both white/gray and subcortical segmentation.

Play with the display by clicking on the square in the second icon row of the GUI and selecting the ‘four squares’ option. This will bring up four display panels. Clicking anywhere on the display panels enables you to navigate through the image volume. On the control panel, the ‘M’, ‘O’ and ‘P’ buttons on the first row allow you to toggle on and off the ‘main’, ‘original’ and ‘pial’ surfaces found by Freesurfer. Toggling the buttons next to these shows the ‘auxiliary volume’, which in this case is the original T1 weighted scan, complete with skull.

### tksurfer

`tksurfer` is straightforward. You can look at the surfaces and inflated volumes by issuing a command like

```bash
tksurfer 001 lh pial
```

Then use the buttons in the first icon row of the GUI labelled ‘M’, ‘W’ and ‘O’ to visualise the different surfaces. The second icon row is for drawing stuff on the surface like ROIs; the third row allows you to rotate/zoom the image in a clunky fashion.

Freesurfer does nice cortical labelling and parcellation. Take a look at this by going to the `tksurfer` control menu and menu `File --> Label --> LoadLabel`. In the dialog box the pops up navigate to the `./label` subdirectory of your subject directory and select `lh.aparc.annot`. Hey presto a beautifully coloured pial surface should emerge. If you’re interested in how this labelling works, go [here](http://surfer.nmr.mgh.harvard.edu/fswiki/Articles) and download the articles under `cortical parcellation`. Note that all of these can be exported for ROIs for use in SPM, as can the subcortical segmentation. See [Cookbook 4](Cookbook_4.md) for details.

Bahador: don't make the mistake of trying `File --> Load Label Value Files`. Which is at the top of the menu. The `File --> Label` option is further at the bottom of the file menu.

Now have a look at the inflated surface. Look at the `tksurfer` menus and try `File --> LoadSurfaceConfiguration --> InflatedVertices`. Click ‘OK’ on the dialog box that comes up (or navigate to the `surf` directory in the subject directories). This should produce an inflated surface that is just plain gray.

Start adding curvature data (of the gyri and sulci) to it by going to the menu `File --> Curvarture --> LoadCurvature` and again clicking ‘OK’ on the dialog box (or going to `lh.curv` which lives in the surf directory of your subject directory). This should produce nice green and red lines which can now be toggled on and off using the button on the first icon row of the `tksurfer` control panel that looks like red and green concentric stripes.

Finally, figure out how to link cursors in `tksurfer` and `tkmedit`. You should have both running. In `tksurfer`, click on any point in the display window and you should see a ‘splodge’ appear on the cortical surface. Go to the control window and click on the `save point` button in the first row of icons. Now go to `tkmedit` and in the control window click on `GotoSave point` which is ergonomically placed in the second row. `Tkmedit` will now move the cursor to the point in the 3D volume that corresponds to the same point on the inflated surface. This will be useful when editing topological defects on the surface (see `Editing volumes` below).

A brief tutorial on editing volumes continues below. There is a fuller tutorial on inspecting output data [here](http://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/TroubleshootingData) (which assumes you have downloaded the [tutorial data](http://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/Data") but can easily be adapted for your data if you just substitute your subject ID in the command line commands given.

## Editing volumes to get better segmentation

NB: Many of the operations described here require a mouse with 3-button functionality. On a Mac you need a ‘Mighty Mouse’ (the one with the scrollwheel) and use the SystemPrefs to configure appropriately.

### Correcting unsegmented white matter.

Following recon-all autorecon2, load the surface (e.g. `tksurfer subjecid rh inflated`) and the volume (e.g. tksurfer subject id brainmask.mgz lh.white –aux wm.mgz rh.white –aux-surface rh.white). Look for areas where the white matter or pial boundaries have improperly excluded segments of the brain.

Again, load surface and volume as described above, and look for areas where the white matter boundary or the pial boundary have improperly excluded segments of the brain.

Turn on the `Edit Ctrl Pts Tool` (5th button in the top row of the GUI, hot key “t”).

Add control points by clicking Button 3 over unclassified white matter. Erase erroneous control points by right clicking.

Be sure to go through multiple slices, from multiple orientations if necessary, to make sure you have sufficiently identified the omitted white matter.

Save the control points (`File --> Save Control Points`), and redo the segmentation using `recon-all autorecon2-cp`.

### Getting rid of problem areas (excess skull, misclassified white matter, etc).

Again, load surface and volume as described above. Inspect the surface, and use the save point/goto saved point buttons on the GUI to locate surface anomalies in the volume.

“Pimples” are usually pieces of volume inaccurately classified white matter. These can be corrected by “erasing” white matter. Load the white matter volume (in the above example it is loaded as the auxiliary volume).

Turn on the `Edit Voxel Tool` (third button in the top row of the GUI; hotkey “a”). Alter the settings of the brush as desired:
`Tools --> Configure Brush Info`
Select the Target as the appropriate volume (in this example, it is the Aux volume). Change the radius and shape of the brush as necessary.

To erase voxels, click Button 2 (right click) over the appropriate voxel. If you make a mistake, use `Edit -> Undo Last Edit`, or ctrl-z. Be aware that this only works on the last edit made; to correct larger mistakes, see Painting and Cloning, below.

Again, be sure to look at multiple slice and multiple orientations to completely erase the offending piece of volume.

Save the volume, and redo the segmentation using recon-all autorecon2-wm.

### Painting and Cloning, to fill in gaps or correct editing mistakes.

Load whatever auxiliary surface is appropriate for the needed correction (File -> Aux Volume -> Load Aux Volume).

Turn on the Edit Voxels Tool.

Go to `Tools --> Configue Brush Info` to set up brush options and make sure you are painting on the correct surface.

Go to Tools --> Configure Volume Brush. If painting, (e.g. filling in ventricles) make sure the appropriate paint value is selected. If cloning, make sure the appropriate source volume is selected.

Use Button 3 to paint/clone in the desired volume.

Save any changes.
