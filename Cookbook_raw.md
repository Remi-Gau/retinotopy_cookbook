Freesurfer Notes

    1. Went to Freesurfer Wiki and downloaded latest stable version for OS X. Checked X11 installed and up-to-date. Went to 3D Slicer website and downloaded stable release.

    2. Looked for a tutorial/manual. Downloaded from http://surfer.nmr.mgh.harvard.edu/pub/docs/FreeSurferTutorial-2007-08-20_N.pdf

    3. Downloaded sample dataset. Anonymous ftp to ftp surfer.nmr.mgh.harvard.edu. cd pub/dist. get buckner_data-tutorial_subjs.tar.gz. Is enormous (11Gb!)

    4. Reviewed manual. Nothing about retinotopic mapping or SPM overlays. Does have information about surface reconstruction and has information about FSL overlays.

    5. Found SPM SurfRend. Appears to be package for surface overlays in Freesurfer. Yay! http://spmsurfrend.sourceforge.net/index.html. Downloaded rendered surfaces, manual and package (SPM toolbox)

    6. Downloaded installed Freesurfer. Moved to /usr/local/freesurfer. Configured FREESURFER_HOME envelope variable and ran the csh script SetUpFreeSurfer.

    7. Tested tkmedit with tkmedit bert orig.mgz. Opened tksurfer too, then tkmedit buttons stopped working. Works fine if both opened from separate shells

    8. Moved slicer into /usr/local/slicer. Worked fine first time but won’t move across multiple monitors

    9. Got Meridian dataset and copied into freesurfer /subjects directory

    10. Got annoyed with setting environment variables. Set default tcsh in Terminal->Preferences. Then created .cshrc in user home directory with setenv and source commands. Works fine.

    11. Labelling subject director and moved .img anatomical into that directory. Typed in mri_convert AD.img 001.mgz and it worked fine. Found we could open using tkmedit but couldn’t figure out how to alter contrast

    12. Started reconstruction using recon-all –autorecon1 –subjid 001 and it started working…for a while.

    13. Meanwhile noticed directories created in subject directory. Used tkmedit to load orig.mgz and this looks to be intensity normalised. Could navigate around OK.

    14. Autorecon1 finished. Noticed this appears to be one of three steps and basically intensity normalises the volume and skull strips. Opened brainmask.mgz in tkmedit and noticed skull stripped OK but with some errors. Need to figure out how to fix! Went to page 69 in the manual which describes what’s going on in the autorecon steps. Decided to press on with autorecon2 anyway (same syntax as in point 12)

    15. Started autorecon2. Run to finish and produced pial surfaces. Started autorecon3. Ran to completion and produced inflated surfaces. Could view by issuing commands tksurfer 001 lh pial or tksurfer 001 lf inflated.

    16. Started Inspection of Output Freesurfer tutorial. Initially on 001 brain. Issued command

Tkmedit 001 brainmask.mgz lh.white \ -aux T1.mgz –aux-surface rh.white \ -segmentation aseg.mgz $FREESURFER_HOME/FreeSurferColorLUT.txt

This produces tkmedit image window of coronal section on which color overlays are shown. Hooray! Did exercises to look at stuff. Quit.

    17. Quit tkmedit. Continued exercises to look at surfaces using tksurfer. Cool!

    18. Started troubleshooting tutorial. Ignored examples and concentrated on subject 001. Noticed a bit of ?skull still present in mid-sagittal plane. Experimented with lowering watershed to 23

recon-all –skullstrip –wsthresh 23 –clean-bm –subjid 001

Overlays

    1. Downloaded SPM_surfrend. Extraction to custom w-surface appears to break, needing spm_get at line 82 which now doesn’t exist in SPM5. Got around this by hard coding the pathway at line 82. SPM_surfrend broke again later, calling a function called read_surf which is not in the distribution. Googled to find this is part of a distribution of ROI tools as add-on to FSL, to be found at http://froi.sourceforge.net/. Downloaded the tar file, added to Matlab path and SPM_surfrend now runs to completion.

    2. Attempted to load resultant SPM contrasts onto Freesurfer surface. Didn’t work, produced garbage. Checked registration of example beta image from analysis directory and original structural, looked fine. Wonder if the .mat file associated with the structural is important but being ignored by Freesurfer?

    3. After poking around wikis, Issued command tkregister2 –help and found help on creating a register.dat file from an SPM .mat file. Issued command tkregister2 --mov /usr/local/freesurfer/subjects/meridian_tmp/meridianStats/spmT_0003.img --s 001 --regheader --noedit --reg register.dat where the first path is to an SPM of the image to overlay and the subject number is the ‘001’. Didn’t work, but produced a more sensible looking ‘blob’ overlay…on the temporal lobe.

    4. Instead created register.dat not from an SPMT file but from an srfM*.img file. Didn’t work. Next re-did coregistration (only) from srfM*.img to original structural and then re-estimated register.dat. Eliminated –noedit flag to tkregister2 and inspected manually through GUI. Looked OK. Reran SPM_surfrend.

    5. OK figured out the reason for all this crap is that the original MRI image fed into Freesurfer is accompanied by a .mat file. This .mat file ensures coregistration with the functionals, so removing it eliminated the coregistration. Suspect that Freesurfer ignored this, so generating register.dat from tkregister2 is simply ignoring this ‘flip’ and thus resulting in rendering on temporal lobe

    6. Solution. Resliced the original anatomical into its OWN space, creating rAD.img WITHOUT .mat file. Then mri_convert into Freesurfer format and ran recon-all

    7. Downloaded canonical brain surfaces and moved to Freesurfer subject directory. Can view easily with tksurfer. Presumably need analysis in normalised space to display.

Continuing Saga!

    8. Checked registration of SPM-t from meridian analysis and original resliced structural MR going into Freesurfer. Looked OK

    9. Used tkmedit to overlay stats onto structural. Command is tkmedit 002 T1.mgz -aparc+aseg -overlay /usr/local/freesurfer/subjects/meridian_tmp/meridianStats/spmT_0003.img. Worked very well, obviously good coregistration.

    10. Just overlaid using tksurfer as follows. tksurfer 002 lh inflated -overlay /usr/local/freesurfer/subjects/meridian_tmp/meridianStats/spmT_0003.img. It worked!

    11. Worked fine overlaying other SPM t maps. Worked fine overlaying con.img.

    12. Configured display using View->Configure->Overlay. Need some documentation. ‘Threshold’ alteration seems most effective, pulling around the histogram vertical bars. Can alter threshold max/min (not clear what this means with con.img

    13. Figured out how to do cuts. http://surfer.nmr.mgh.harvard.edu/fswiki/FreeSurferOccipitalFlattenedPatch works fine to produce occipital patch. Then use mris_flatten – w 10 input.patch output.patch to flatten. Seems to run fine (takes a few minutes…)

    14. Figured out why overlay button didn’t work from tksurfer. Reran tkregister2 to make register.dat, made sure this was in same directory as spmT AND manually specified its location.

    15. Found out how to overlay cortical parcellations WITH SPM on inflated surface. Issue command tksurfer 002 lh inflated -overlay /usr/local/freesurfer/subjects/002/stats/spmT_0003.imgoverlay-reg /usr/local/freesurfer/subjects/002/stats/register.dat -annot aparc.annot and then go to View->LabelStyle->Outline. Clicking on surface displays name of cortical structure in the control panel and outputs some coordinate junk in the terminal window. Nice!

Segmentations & making ROIs

    16. Looked at this tutorial which is mainly about feat. http://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/MapSegmentationsToFunctionalSpace. Tried running aseg2feat but this failed saying it was looking for something called anat2exf.register.dat. This appears to be a registration file created by running reg-feat2anat which uses FSL-specific tools to do coregistration and write a register.dat file.

    17. Decided to fake an FSL register.dat file. Created ‘test’ subdirectory in subjects/002 as the ‘fake’ FSL directory and in that created ./reg/freesurfer and copied the register.dat I’d created in step 14 above. Renamed that register.dat as anat2exf.register.dat and then issued the command aseg2feat –feat test –aseg aparc+aseg. This ran to completion and created aparc+aseg2feat.log but no niftii files as I was expecting

    18. Tried a different tack. Created a directory trial_labels in subject directory 002 and issued command mri_annotation2label --subject 002 --labelbase trial_labels/test --hemi lh. This ran to completion producing a whole load of files with the labelbase test-***.label (34 in total)

    19. Read the output mri_annotation2label –help and tried again, deleting files in 20 and issuing mri_annotation2label --subject 002 --hemi lh --outdir ./trial_labels. This worked but now produces label files with meaningful names! Now using tksurfer lh inflated and menu File->Label->Load label you can load in specific labels e.g. lh.corpuscallosum. Yay!

    20. Now tried to make this into an ROI. Read mri_label2vol –help. Issued mri_label2vol --label ./trial_labels/lh.corpuscallosum.label --temp ./stats/spmT_0003.img  --reg stats/register.dat --fillthresh .5 --o cent_lh_000.bshort. Here I’m using the spmT image as the geometric image (perhaps wrong) and the register.dat I created in 19 as the registration file. Ran to completion and produced a whole load of .bshort stuff in the main subject directory. Each bshort image seems to be a binary mask of the labelled area. Yay!

    21. Learnt how to visualise these masks using tkmedit 002 orig.mgz -overlay ./cent_lh_000.bshort -overlay-reg ./stats/register.dat -fthresh .5 -fmid 1. This overlays the mask (seems to be callosum) onto the T1 in tkmedit. Again using the register.dat created for the statistics overlay from 18.

    22. In tksurfer, used File->Label->Load Label to load the lh.callosum label. This overlaps pretty much with the bshort overlay I created. Not clear how the numbering of the bshorts relates to the labelling though.

    23. Converted this overlay image into analyze by issuing mri_convert cent_lh_000.bshort test.img

    24. Fired up SPM and used mri_convert cent_lh_000.bshort test.img to make an analyze format image called test.img. This has associated .hdr and .mat files. Then used check-reg between this image and the structural rAD.img (the one resliced so it had no .mat file). This worked! Yay! So now we can make ROIs from the segmentation in SPM space.

    25. Naming of labels. In the subject directory there is a subdirectory ./label that contains a file aparc.annot.a2005s.ctab. This appears to contain the names and numbers of the labels (see below). There are 35 labels in this file and they correspond to the label names created in 20 above.

Drawing retinotopic ROIs

    26. Started with tksurfer 002 lh inflated -patch lh.patch_flat -overlay ./stats/spmT_0003.img. This displayed the cut-out left hemisphere patch with the SPM overlaid on top of it. I’m going to try and draw round the blue stuff. Don’t really care at this stage what this represents, just want to see if I can get to an ROI that looks roughly peri-calcarine.

    27. Noted you can use the –annot aparc.annot flag on tksurfer command above to display parcellation overlays

    28. Zoomed in. Clicked on a large number of vertices and then on the Path tool in the tksurfer GUI. A nice red line was drawn along this path outlining a border (in this case, around the calcarine cut)

    29. Clicked inside the region and then on Custom Fill. That filled the whole region ‘cos I hadn’t made a closed path. Aargh! Found that Tools->Fill->DeleteSelectedPath allows you to delete the whole thing

    30. Tried outlining a different region. Made a closed path and CustomFill-> UpToAndIncludingPaths. http://surfer.nmr.mgh.harvard.edu/fswiki/TkSurferGuide_2fTkSurferWorkingWithData_2fTkSurferLabel is useful to tell you about the menu options.

    31. Used File->Label->SaveSelectedLabel to save as test_yellow.label

    32. Started another tksurfer using command tksurfer 002 lh inflated -overlay /usr/local/freesurfer/subjects/002/stats/spmT_0003.img -reg /usr/local/freesurfer/subjects/002/stats/register.dat then File->LoadLabel and selected the test_yellow.label made in step 32. Looks good (note not enough anterior extension due to positioning of cut)

    33. Now converted to volume like step 21 with mri_label2vol --label ./label/test_yellow.label --temp ./stats/spmT_0003.img --reg ./stats/register.dat --fillthresh .5 --o test_yellow.bshort but this produced about 31 output bshort images. Aargh! Perhaps this is just how the bshort images work? Seemed to visualise OK with tkmedit 002 orig.mgz -overlay ./test_yellow.bshort.bhdr -overlay-reg ./stats/register.dat -fthresh .5 -fmid 1. Then used mri_convert test_yellow.bshort.bhdr test_yellow.img and in SPM then used check_coreg

    34.





Stuff To Do

    1. Find out about color labelling scheme – what is it based on for cortex and for subcortical structures? Action: Lauri

    2. Figure out how to use tkmedit to edit white/gray matter topology etc. Action: Todd

    3. Figure out if 1.5T phased array coil is better (‘VBM structurals’). Need example structural to find out. Action: Christian to talk to Nikolaus

    4. Figure out how to make subcortical segmentation volumes. http://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/MapSegmentationsToFunctionalSpace and talk to Bogdan.


Things that might be useful but we haven’t checked out fully

http://www.nmr.mgh.harvard.edu/~smosher/manuals/sue_cortical_inflation.pdf (guide to cortical inflation using freesurfer)

http://surfer.nmr.mgh.harvard.edu/fswiki/UserContributions_2fScripts_2fTwo shell script to reconstruct multiple subjects as batch

Neurolens. Can open surfaces, but not get lighting correct. Need to Google search

Mailing lists

http://mail.nmr.mgh.harvard.edu/mailman/listinfo/freesurfer

Labels

  0  unknown                          25   5  25    0
  1  bankssts                         25 100  40    0
  2  caudalanteriorcingulate         125 100 160    0
  3  caudalmiddlefrontal             100  25   0    0
  4  corpuscallosum                  120  70  50    0
  5  cuneus                          220  20 100    0
  6  entorhinal                      220  20  10    0
  7  fusiform                        180 220 140    0
  8  inferiorparietal                220  60 220    0
  9  inferiortemporal                180  40 120    0
 10  isthmuscingulate                140  20 140    0
 11  lateraloccipital                 20  30 140    0
 12  lateralorbitofrontal             35  75  50    0
 13  lingual                         225 140 140    0
 14  medialorbitofrontal             200  35  75    0
 15  middletemporal                  160 100  50    0
 16  parahippocampal                  20 220  60    0
 17  paracentral                      60 220  60    0
 18  parsopercularis                 220 180 140    0
 19  parsorbitalis                    20 100  50    0
 20  parstriangularis                220  60  20    0
 21  pericalcarine                   120 100  60    0
 22  postcentral                     220  20  20    0
 23  posteriorcingulate              220 180 220    0
 24  precentral                       60  20 220    0
 25  precuneus                       160 140 180    0
 26  rostralanteriorcingulate         80  20 140    0
 27  rostralmiddlefrontal             75  50 125    0
 28  superiorfrontal                  20 220 160    0
 29  superiorparietal                 20 180 140    0
 30  superiortemporal                140 220 220    0
 31  supramarginal                    80 160  20    0
 32  frontalpole                     100   0 100    0
 33  temporalpole                     70  70  70    0
 34  transversetemporal              150 150 200    0


---------------------------------------------------------------------------------
[geraint-rees-computer:subjects/002/label] Geraint% mri_label2vol --help
USAGE: mri_label2vol

   --label labelid <--label labelid>
   --annot annotfile : surface annotation file
   --seg   segpath : segmentation
   --aparc+aseg  : use aparc+aseg.mgz in subjectdir

   --temp tempvolid : template volume
   --reg regmat : VolXYZ = R*LabelXYZ
   --invertmtx : Invert the registration matrix

   --fillthresh thresh : between 0 and 1 (def 0)
   --labvoxvol voxvol : volume of each label point (def 1mm3)
   --proj type start stop delta

   --subject subjectid : needed with --proj or --annot
   --hemi hemi : needed with --proj or --annot

   --o volid : output volume
   --hits hitvolid : each frame is nhits for a label
   --label-stat statvol : map the label stats field into the vol

   --native-vox2ras : use native vox2ras xform instead of tkregister-style
   --version : print version and exit
   --help

$Id: mri_label2vol.c,v 1.25 2007/07/13 16:48:31 greve Exp $


Help Outline:
  - SUMMARY
  - ARGUMENTS
  - RESOLVING MULTI-LABEL AMBIGUITIES
  - CHECKING YOUR RESULTS
  - EXAMPLES
  - KNOWN BUGS
  - SEE ALSO

SUMMARY

Converts a label or a set of labels into a volume. For a single label,
the volume will be binary: 1 where the label is and 0 where it is not.
For multiple labels, the volume will be 0 where no labels were found
otherwise the value will the the label number. For a voxel to be
declared part of a label, it must have enough hits in the voxel and
it must have more hits than any other label.

ARGUMENTS

--label labelfile <--label labelfile>

Enter the name of the label file. For multiple labels, use multiple
--label flags. Labels can be created manually with tkmedit and
tksurfer or automatically from a subcortical segmentation or cortical
annotation. Labels are simple text files. The first line is a header.
Each following line contains data with 5 columns. The first column is
the vertex number of the label point. The next 3 columns are the X, Y,
and Z of the point. The last can be ignored. If the label is not a
surface-based label, then the vertex number will be -1. Not with --annot
or --seg

--annot annotfile

FreeSurfer can perform automatic parcellation of the cortical surface,
the results of which are stored in an annotation file. This is a
binary file that assigns each surface vertex to a cortical area
designated by a unique number. These annotations can be converted to
separate labels using mri_annotation2label. These labels can then be
input to mri_label2vol using --label. Or, the annotation file can be
read in directly using --annot. The map of annotation numbers to
annotation names can be found at Simple_surface_labels2002.txt
in $FREESURFER_HOME. Not with --label or --seg.

--seg segpath

Path to a segmentation. A segmentation is a volume in which each voxel
is assigned a number indicating it's class. The output volume will keep
the same numbering. The registration in this
case goes from the seg to the template volume. Not with --label or
--annot.

--temp tempvolid

Template volume. The output volume will have the same size and geometry
as the template. Template must have geometry information (ie, direction
cosines and voxel sizes). Required.

--reg regmatfile

tkregister-style registration matrix (see tkregister2 --help) which maps
the XYZ of the label to the XYZ of the template volume. If not specified,
then the identity is assumed.

--fillthresh thresh

Relative threshold which the number hits in a voxel must exceed for
the voxel to be considered a candidate for membership in the label. A
'hit' is when a label point falls into a voxel. thresh is a value
between 0 and 1 indicating the fraction of the voxel volume that must
be filled by label points. The voxel volume is determined from the
template. It is assumed that the each label point represents a voxel
1mm3 in size (which can be changed with --labvoxvol). So, the actual
number of hits needed to exceed threshold is
thresh*TempVoxVol/LabVoxVol. The default value is 0, meaning that any
label point that falls into a voxel makes that voxel a candidate for
membership in the label.  Note: a label must also have the most hits
in the voxel before that voxel will actually be assigned to the label
in the volume. Note: the label voxel volume becomes a little ambiguous
for surface labels, particularly when they are 'filled in' with
projection.

--labvoxvol voxvol

Volume covered by each label point. Default is 1mm3. This only affects
the fill threshold (--fillthresh). Note: the label voxel volume
becomes a little ambiguous for surface labels, particularly when they
are projected.

--proj type start stop delta

Project the label along the surface normal. type can be abs or frac.
abs means that the start, stop, and delta are measured in mm. frac
means that start, stop, and delta are relative to the thickness at
each vertex. The label definition is changed to fill in label
points in increments of delta from start to stop. Requires subject
and hemi in order to load in a surface and thickness. Uses the
white surface. The label MUST have been defined on the surface.

--subject subjectid

FREESURFER subject identifier. Needed when using --proj.

--hemi hemi

Hemisphere to use loading the surface for --proj. Legal values are
lh and rh.

--o volid

Single frame output volume in which each voxel will have the number of
the label to which it is assigned (or 0 for no label). The label
number is the order in which it appears on the command-line.  Takes
any format accepted by mri_convert (eg, spm, analyze, bshort, mgh).

--hits hitvolid

Hit volume. This is a multi-frame volume, with one frame for each
label. The value at each voxel for a given frame is the number of hits
that voxel received for that label. This is mostly good as a debugging
tool, but you could use it to implement your own multi-label
arbitration routine. Or you could binarize to have each label
represented separately. Takes any format accepted by mri_convert (eg,
spm, analyze, bshort, mgh). With --seg, this is a single frame volume
with the number of hits from the winning seg id.

--native-vox2ras

Use the 'native' voxel-to-RAS transform instead of the tkregister-style.
The 'native' vox2ras is what is stored with the volume (usually scanner)
This may be needed depending upon how the label was created (eg, with scuba).

--label-stat labelstatvol

Create a volume in which the value at a voxel is the label stat for that voxel.
Note: uses the value from last label point hit. Not recommended for multiple
labels.

RESOLVING MULTI-LABEL AMBIGUITIES

When there are multiple labels, it is possible that more than one
label will map to a single voxel in the output volume. When this
happens, the voxel is assigned to the label with the most label
points in that voxel. Note that the voxel must still pass the
fill threshold test in order to be considered part of the label.

CHECKING YOUR RESULTS

It is very important to check that the conversion of the label to the
volume was done correctly. It may be that it is way off or it could be
off around the edges. This is particularly true for surface-based
labels or when converting a label to a low-resolution space.
To check the result, load the orig volume into tkmedit. The orig
volume should be in the label space. Load the mri_label2vol output
volume as an overlay; this makes the labeled voxels appear as
'activity'.  Finally, load the label itself. You should see the label
(in green) sitting on top of the 'activity' of the labeled volume.
See EXAMPLE 1 for an example.


EXAMPLES

1. Convert a label into a binary mask in the functional space; require
that a functional voxel be filled at least 50% by the label:

mri_label2vol
  --label lh-avg_central_sulcus.label
  --temp f_000.bshort
  --reg register.dat
  --fillthresh .5
  --o cent-lh_000.bshort

To see how well the label is mapped into the functional volume, run

tkmedit bert orig
  -overlay ./cent-lh_000.bshort
  -overlay-reg ./register.dat -fthresh .5 -fmid 1

Then load the label with File->Label->LoadLabel. The label should
overlap with the overlay. The overlap will not be perfect but it
should be very close.

2. Convert a surface label into a binary mask in the functional space.
Fill in all the cortical gray matter. Require that a functional voxel
be filled at least 30% by the label:

mri_label2vol
  --label lh-avg_central_sulcus.label
  --temp f_000.bshort
  --reg register.dat
  --fillthresh .3
  --proj frac 0 1 .1
  --subject bert --hemi lh
  --o cent-lh_000.bshort

3. Convert a surface label into a binary mask in the functional space.
Sample a 1mm ribbon 2mm below the gray/white surface. Do not require a
fill threshold:

mri_label2vol
  --label lh-avg_central_sulcus.label
  --temp f_000.bshort
  --reg register.dat
  --proj abs -3 -2 .1
  --subject bert --hemi lh
  --o cent-lh_000.bshort

4. Convert two labels into a volume in the same space as the labels:

mri_label2vol
  --label lh-avg_central_sulcus.label
  --label lh-avg_calcarine_sulcus.label
  --temp $SUBJECTS_DIR/bert/orig
  --o cent_calc.img

The voxels corresponding to lh-avg_central_sulcus.label will have a of
value of 1 whereas those assigned to lh-avg_calcarine_sulcus.label will
have a value of 2.

KNOWN BUGS

1. When the output type is COR, all the voxels will be zero. The work-around
is to save it as some other type, then use mri_convert with --no_rescale 1
to convert it to COR.

2. Cannot convert surface labels with different hemispheres.



SEE ALSO

mri_label2label, mri_cor2label, mri_annotation2label, mri_mergelabels,
tkregister2, mri_convert, tkmedit, tksurfer.

http://surfer.nmr.mgh.harvard.edu/docs/tkmedit_guide.html
http://surfer.nmr.mgh.harvard.edu/docs/tksurfer_doc.html



USAGE: mri_annotation2label

   --subject    source subject
   --hemi       hemisphere (lh or rh) (with surface)
   --labelbase  output will be base-XXX.label
   --outdir dir :  output will be dir/hemi.name.label

   --annotation as found in SUBJDIR/labels <aparc>
   --surface    name of surface <white>

   --table : obsolete. Now gets from annotation file

   --help       display help
   --version    display version

This program will convert an annotation into multiple label files.
User specifies the subject, hemisphere, label base, and (optionally)
the annotation base and surface. By default, the annotation base is
aparc. The program will retrieves the annotations from
SUBJECTS_DIR/subject/label/hemi_annotbase.annot. A separate label file
is created for each annotation index. The output file names can take
one of two forms: (1) If --outdir dir is used, then the output will
be dir/hemi.name.lable, where name is the corresponding name in
the embedded color table. (2) If --labelbase is used, name of the file conforms to
labelbase-XXX.label, where XXX is the zero-padded 3 digit annotation
index. If labelbase includes a directory path, that directory must
already exist. If there are no points in the annotation file for a
particular index, no label file is created. The xyz coordinates in the
label file are obtained from the values at that vertex of the
specified surface. The default surface is 'white'. Other options
include 'pial' and 'orig'.

The human-readable names that correspond to the annotation indices for
aparc are embedded in the annotation file itself. It is no longer
necessary (or possible) to specify the table explicitly with the
--table option.

Bugs:

  If the name of the label base does not include a forward slash (ie, '/')
  then the program will attempt to put the label files in
  $SUBJECTS_DIR/subject/label.  So, if you want the labels to go into the
  current directory, make sure to put a './' in front of the label base.

Example:

  mri_annotation2label --subject LW --hemi rh
        --labelbase ./labels/aparc-rh

  This will get annotations from $SUBJECTS_DIR/LW/label/rh_aparc.annot
  and then create about 94 label files: aparc-rh-001.label,
  aparc-rh-002.label, ... Note that the directory 'labels' must already
  exist.

Example:

  mri_annotation2label --subject LW --hemi rh
        --outdir ./labels

  This will do the same thing as above except that the output files
  will have names of the form lh.S_occipital_anterior.label

Testing:

  1. Start tksurfer:
       tksurfer -LW lh inflated
       read_annotations lh_aparc
     When a point is clicked on, it prints out a lot of info, including
     something like:
       annot = S_temporalis_sup (93, 3988701) (221, 220, 60)
     This indicates that annotion number 93 was hit. Save this point.

  2. Start another tksurfer and load the label:
       tksurfer -LW lh inflated
       [edit label field and hit the 'Read' button]
     Verify that label pattern looks like the annotation as seen in
     the tksurfer window from step 1.

  3. Load label into tkmedit
       tkmedit LW T1
       [Load the label]
      [Goto the point saved from step 1]
---------------------------------------------------------------------------------
% tkregister2 --help

USAGE: tkregister2.bin

   --help : usage and documentation

   --mov  movable volume  <fmt>
   --targ target volume <fmt>
   --fstarg : target is relative to subjectid/mri
   --reg  register.dat : input/output registration file
   --check-reg : only check, no --reg needed
   --regheader : compute regstration from headers
   --fsl-targ : use FSLDIR/etc/standard/avg152T1.img
   --fsl-targ-lr : use FSLDIR/etc/standard/avg152T1_LR-marked.img
   --fstal : set mov to be tal and reg to be tal xfm
   --no-zero-cras : do not zero target cras (done with --fstal)
   --movbright  f : brightness of movable volume
   --no-inorm  : turn off intensity normalization
   --plane  orient  : startup view plane <cor>, sag, ax
   --slice  sliceno : startup slice number
   --volview volid  : startup with targ or mov
   --fov FOV  : window FOV in mm (default is 256)
   --movscale scale : scale size of mov by scale
   --surf surfname : display surface as an overlay
   --lh-only : only load/display left hemi
   --rh-only : only load/display right hemi
   --xfm file : MNI-style registration input matrix
   --xfmout file : MNI-style registration output matrix
   --fsl file : FSL-style registration input matrix
   --fslregout file : FSL-Style registration output matrix
   --vox2vox file : vox2vox matrix in ascii
   --lta ltafile : Linear Transform Array
   --feat featdir : check example_func2standard registration
   --identity : use identity as registration matrix
   --s subjectid : set subject id
   --sd dir : use dir as SUBJECTS_DIR
   --noedit : do not open edit window (exit) - for conversions
   --nofix : don't fix old tkregister matrices
   --float2int code : spec old tkregister float2int
   --title title : set window title
   --tag : tag mov vol near the col/row origin
   --mov-orientation ostring : supply orientation string for mov
   --targ-orientation ostring : supply orientation string for targ
   --int intvol intreg : use registration from intermediate volume
   --gdiagno n : set debug level


$Id: tkregister2.c,v 1.86.2.1 2007/08/15 15:39:42 nicks Exp $
SUMMARY

tkregister2 is a tool to assist in the manual tuning of the linear
registration between two volumes, mainly for the purpose of
interacting with the FreeSurfer anatomical stream. The GUI displays a
window in which the user can toggle between the two volumes to see how
well they line up. It is also possible to display the cortical surface
to assist in alignment. The user can edit the registration by
translation, rotation, and stretching.  The initial registration can
be based on: (1) a previously existing registration, (2) the spatial
information contained in the headers of the two input volumes, or (3)
an FSL registration matrix. The output takes the form of a FreeSurfer
registration matrix. It is also possible to output an FSL registration
matrix. It is possible to run tkregister2 without the manual editing
stage (ie, it exits immediately) in order to compute and/or convert
registration matrices. tkregister2 can also compute registration
matrices from two volumes aligned in SPM or FSL.


FLAGS AND OPTIONS

  --targ target-volume-id

  This is the path to the target volume. It can read anything readable
  by mri_convert. See also --fstarg.

  --fstarg

  This flag implicitly sets the target volume to be the T1 volume found
  in the subjects directory under the subject name found in the
  input registration matrix.

  --mov movable-volume-id

  This is the path to the target volume. It can read anything readable
  by mri_convert. See also --fstal.

  --fstal

  Check and edit the talairach registration that was created during
  the FreeSurfer reconstruction. Sets the movable volume to be
  $FREESURFER_HOME/average/mni305.cor.mgz and sets the registration file to be
  $SUBJECTS_DIR/subjectid/transforms/talairach.xfm. User must have
  write permission to this file. Do not specify --reg with this
  flag. It is ok to specify --regheader with this flag. The format
  of the anatomical is automatically detected as mgz or COR. By default,
  the target c_ras is temporarily set to 0 to assure that the target
  is properly centered. This is taken into account when computing
  and writing the output xfm. To turn this off, add --no-zero-cras.

  --plane <orientation>

  Set the initial orientation. Valid values are cor, sag, and ax.
  The default is cor.

  --slice sliceno

  Set the initial slice to view.

  --fov FOV

  Set the view port field-of-view. Default is 256. Note, the zoom
  can also be controlled interactively with - and =.

  --movscale scale

  Adjust registration matrix to scale mov volume by scale. This has
  the same effect as adjusting the scale manually. It also flags
  the matrix as being edited, and so you will be asked to save it
  even if you have not made any manual edits.

  --movframe frame

  Use frame from moveable volume.

  --surf <surfacename>

  Load the cortical surface (both hemispheres) and display as an
  overlay on the volumes. If just --surf is supplied, then the white
  surface is loaded, otherwise surfacename is loaded. The subject
  name (as found in the input registration file or as specified
  on the command-line) must be valid, and the surface must exist
  in the subject's anatomical directory. The surface display can
  be toggled by clicking in the window and hitting 's'.

  --reg registration-file

  Path to the input or output FreeSurfer registration file. If the user
  specifies that the initial registration be computed from the input
  headers or from an FSL matrix, then this will be the output file.
  Otherwise, the initial registration will be read from this file
  (it will still be the output file). Note: a FreeSurfer registration
  file must be specified even if you only want the FSL registration
  file as the output. See also --fstal.

  --regheader

  Compute the initial registration from the information in the headers
  of the input volumes. The contents of the registration file, if it
  exists, will be ignored. This mostly makes sense when the two volumes
  were acquired at the same time (ie, when the head motion is minimal).

  --fsl FSL-registration-file

  Use the matrix produced by the FSL routines as the initial registration.
  It should be an ascii file with a 4x4 matrix. Note: the matrix should
  map from the mov to the target. See also --feat and --fslregout.

  --xfm MNI-Style registration matrix

  Use the matrix produced by an MNI prgram as the   initial registration.
  Note: the matrix should map from the mov to the target.

  --lta ltafile

  RAS-to-RAS linear transform array file.  Note: the matrix should map from the mov to the target.

  --ltaout ltaoutfile

  RAS-to-RAS linear transform array file.
  --vox2vox vox2voxfile

  Input registration is a vox2vox ascii file. Vox2Vox maps target
  indices (c,r,s) to mov indices. Lines that begin with '#' are ignored.

  --s identity

  Use identity as input registration. Same as simply creating
  the identity matrix in a register.dat.

  --s subjectid

  Subject identifier string that will be printed in the output registration
  file when no the input registration file is not specified (ie, with
  --regheader or --fsl).

  --sd subjectsdir

  Set the path to the parent directory of the FreeSurfer anatomical
  reconstructions. If unspecified, this will default to the SUBJECTS_DIR
  environment variable. This only has an effect with --fstarg.

  --noedit

  Do not bring up the GUI, just print out file(s) and exit. This is mainly
  useful saving the header-computer registration matrix (or for converting
  from FreeSurfer to FSL, or the other way around).

  --fslregout FSL-registration-file

  Compute an FSL-compatible registration matrix based on either the
  FreeSurfer matrix or the header. This can be helpful for initializing
  the FSL registration routines.

  --feat featdir

  View/modify the FSL FEAT registration to standard space (ie,
  example_func2standard.mat. Manual edits to the registration
  will change this file. There will also be a 'tag', ie, a grid of
  grid dots near the col-row-slice origin of the example_func.
  This might be helpful for determining if the reg is left-right
  reversed.

  --nofix

  This is only here for debugging purposes in order to simulate the behavior
  of the old tkregister.

  --float2int code

  This is only here for debugging purposes in order to simulate the
  behavior of the old tkregister.

  --title title

  Set the window titles to title. Default is subjectname.

  --tag

  Creates a hatched pattern in the mov volume prior to resampling.
  This pattern is in all slices near the col/row origin (ie, near
  col=0,row=0). This can help to determine if there is a left-right
  reversal. Think of this as a synthetic fiducial. Can be good in
  combination with --mov-orientation.

  --mov-orientation ostring
  --targ-orientation ostring

  Supply the orientation information in the form of an orientation string
  (ostring). The ostring is three letters that roughly describe how the volume
  is oriented. This is usually described by the direction cosine information
  as originally derived from the dicom but might not be available in all data
  sets. --mov-orientation will have no effect unless --regheader is specified.
  The first  character of ostring determines the direction of increasing column.
  The second character of ostring determines the direction of increasing row.
  The third  character of ostring determines the direction of increasing slice.
  Eg, if the volume is axial starting inferior and going superior the slice
  is oriented such that nose is pointing up and the right side of the subject
  is on the left side of the image, then this would correspond to LPS, ie,
  as the column increases, you move to the patients left; as the row increases,
  you move posteriorly, and as the slice increases, you move superiorly. Valid
  letters are L, R, P, A, I, and S. There are 48 valid combinations (eg, RAS
  LPI, SRI). Some invalid ones are DPS (D is not a valid letter), RRS (can't
  specify R twice), RAP (A and P refer to the same axis). Invalid combinations
  are detected immediately, an error printed, and the program exits. Case-
  insensitive.


  --int volid regmat

  Use registration from an intermediate volume. This can be useful when the
  FOV of the moveable volume does not cover the entire brain. In this case,
  you can register a full-volume COLLECTED IN THE SAME SESSION AS THE MOVEABLE
  to the target. Then specify this volume and its registration with --int.
  regmat will be the registration resulting from a separate invocation of
  tkregister2 in which the intermediate volume is specified as the moveable.

  --gdiagno N

  Set the diagnostic/debug level. Default is 0.

FREESURFER REGISTRATION CONVENTIONS

For the purposes of FreeSurfer, the registration matrix maps the XYZ
of the anatomical reference (ie, the subjects brain as found in
$SUBJECTS_DIR) to the XYZ of the functional volume. The anatomical
reference is the 'target' volume (argument of --targ) and the
functional volume is the 'movable' volume (argument of --mov).
The XYZ of a given col, row, and slice is defined
based on the field-of-view by the following matrix:

          (-dc 0   0  dc*Nc/2)
     T =  ( 0  0  ds -ds*Ns/2)
          ( 0 -dr  0  dr*Nr/2)
          ( 0  0   0     1   )

where dc, dr, and ds are the voxel resolutions in the column, row,
and slice directions, respectively, and  Nc, Nr, and Ns are the
number of columns, rows, and slices.  Under this convention,

  XYZ = T*[r c s 1]

The FreeSurfer registration matrix is then defined by:

   XYZmov = R*XYZtarg

FREESURFER REGISTRATION FILE FORMAT

The FreeSurfer registration is stored in an ASCII file with the
following format:

      subjectname
      in-plane-res-mm
      between-plane-res-mm
      intensity
      m11 m12 m13 m14
      m21 m22 m23 m24
      m31 m32 m33 m34
      0   0   0   1

The subject name is that as found in the SUBJECTS_DIR. The in-plane-res-mm
is the in-plane pixels size in mm. The between-plane-res-mm is
the distance between slices in mm. Intensity only affect the display
of the movable in the GUI.

USING THE GUI

Two volumes are compared slice-by-slice by hitting the Compare button;
this will alternatively display one volume and then the other. If held
down, it will flash. The relative position of the movable volume can
be changed with three sliders (1) Translate, (2) Rotate, and (3) Scale.
Each operates in-plane; the Rotate rotates about the red cross-hair.
The current orientation can be changed by hitting either the CORONAL,
SAGITTAL, or HORIZONTAL buttons. The matrix can be saved by hitting the
SAVE REG button. The intensity of the movable can be changed by editing
the value of the 'fmov:' text box. The brightntess and contrast of the
target can be changed by editing the 'contrast:' and 'midpoint:' text
boxes. The target can be masked off to the FOV of the movable by
pressing the 'masktarg' radio button. If a surface has been loaded, it
can be toggled by clicking in the display window and hitting 's'.

SUMMARY OF KEYPRESS COMMANDS

0 swap buffers (same as Compare)
1 dispaly target
2 dispaly moveable
a increase moveable frame by 1
b decrease moveable frame by 1
e toggle slice prescription indicator
i intensity normalize images
n use nearest neighbor interpolation
t use trilinear interpolation
s toggle display of cortical surface
x show sagittal view
y show horizontal view
z show coronal view
- or _ zoom out
+ or = zoom in
p translate up
. translate down
l translate left
; translate right
g translate into screen
h translate out of screen
[ rotate counter-clockwise about image normal
] rotate clockwise about image normal
q rotate about horiztonal image axis (neg)
w rotate about horiztonal image axis (pos)
r rotate about vertical image axis (neg)
f rotate about vertical image axis (pos)
Insert increase scale horizontally
Delete decrease scale horizontally
Home   increase scale vertically
End    decrease scale vertically


USING WITH FSL and SPM

In order to apply the FreeSurfer tools to data analyzed in FSL or SPM, it
is necessary that you create a FreeSurfer-style registration matrix. This
matrix is stored in a text file which is usually called 'register.dat'.
It is not the same as an SPM .mat file. It is not the same as an FSL .mat
file. It is not the same as an analyze .hdr file. You can obtain a
FreeSurfer-style registration matrix from those things but it is not the
same as those things. If you do not obtain a FreeSurfer-style registration
matrix, then you will not be able to use the FreeSurfer tools with your
functional data.

You can obtain a FreeSurfer-style registration matrix by directly editing the
registration from within tkregister2, however, it will be easier if you
use FSL or SPM to perform the registration and then use tkregister2 to
generate the FreeSurfer-style registration matrix. To do this, first convert
the anatomical (ie, the one in SUBJECTS_DIR/subjectname/mri/orig)
to analyze format (use mri_convert SUBJECTS_DIR/subjectname/mri/orig cor.img).
Convert the functional to analyze format (eg, f.img).

When using the SPM registration tool, select the functional (f.img) as the object
and the anatomical (cor.img) as the target, and select the 'Coregister only' option.
The registration will be written into the functional .mat file. Run tkregister2
with the --regheader option. Use the --noedit option to suppress
the GUI. Editing the registration will not affect the registration
as seen by SPM. An example command-line is

  tkregister2 --mov f.img --s yoursubject --regheader --noedit --reg register.dat

This will create/overwrite register.dat.

For FSL, coregister the functional (f.img) to the anatomical (cor.img) (ie,
use the anatomical (cor.img) as the reference). This will produce an FSL
registration file (an ASCII file with the 4x4 matrix). Run tkregister2 specifying
this file as the argument to the --fsl flag. Note, it is possible
to generate an FSL matrix from the headers, which can be useful to
initialize the FSL registration routines. To do this, just run
tkregister2 with the --fslregout fsl.mat and --regheader flags,
where fsl.mat is the name of the FSL matrix file. Use the --noedit
option to suppress the GUI. Editing the registration will not affect
the registration as seen by FSL unless --fslregout is specfied.
An example command-line is

  tkregister2 --mov f.img --fsl fsl.mat --s yoursubject --regheader
      --noedit --reg register.dat

This will create/overwrite register.dat.

If you don't include the --noedit, then the GUI will come up, and you
can flip back and forth between the functional and the structural. If
they are not well aligned then you are going to see garbage when you
when you use tkmedit and tksurfer to view your data. So, CHECK YOUR
REGISTRATION!!!

BUGS

It used to be the case that the GUI would not work if the target
was not 256x256x256 voxels, isotropic at 1mm. This has been fixed
by resampling the target into this space. This does not affect the
final registration, ie, the registration matrix maps the original
target (regarless of size) to the movable.

AUTHORS

The original tkregister was written by Martin Sereno and Anders Dale
in 1996. The original was modified by Douglas Greve in 2002.
