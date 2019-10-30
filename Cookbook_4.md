# Freesurfer Cookbook 4 – making overlays

This cookbook assumes you have installed Freesurfer, got hold of a FIL structural and completed the steps in [Cookbook 2](Cookbook_2.md) (generating surfaces etc) and [Cookbook 3](Cookbook_3.md) (rudiments of how to use `tkmedit` and `tksurfer`). This Cookbook is all about how to make SPM overlays (yay!).

## Making overlays

Most problems – as always – come about from coregistration. This tutorial assumes that you are using anatomical and functional images that have been coregistered by SPM, AND that you have followed the steps outlined in [Cookbook 2](Cookbook_2.md) in ensuring that the T1-weighted structural you took into Freesurfer did NOT have a .mat file associated with it (by reslicing it in SPM first).

When making overlays in Freesurfer you need to have a file called ‘register.dat’ that tells freesurfer how to map the functional (SPM) image onto the surface of your choice. This is created from the .mat files associated with your SPM image using a third programme called tkregister2. This is an all-purpose coregistration programme that some people use exclusively for doing coregistrations, rather than SPM/FSL.

Get an idea of what is going on by issuing the command

```bash
tkregister2 --help
```

In general, the response of the command line tools to `-- help` is very impressive and indeed helpful!

Get your SPM data (for example, some meridians – or perhaps an experiment) and identify the image you are going to want to overlay. This might be an SPM{t}, or a con.img, or something else. Now use tkregister2 to make a register.dat file

```bash
tkregister2 --mov /usr/local/freesurfer/subjects/meridian_tmp/meridianStats/spmT_0003.img \
						--s 001 --regheader --noedit --reg register.dat
```

In this example, the first long path is to the stats image I want to overlay; the ‘001’ after the `–s` flag is the subject ID. These two things will need editing to suit your own needs. This will automatically generate a `register.dat` that you should transfer into the directory where the stats images are living so as not to loose track of it. Try also running `tkregister2` without the `–noedit` flag and this will bring up the interactive GUI that you can play with and figure out how it works.

Now you can overlay the stats image, either on the volume

```bash
tkmedit 001 T1.mgz -aparc+aseg -overlay /usr/local/freesurfer/subjects/meridian_tmp/meridianStats/spmT_0003.img.
```

or on the inflated volume:

```bash
tksurfer 001 lh inflated -overlay /usr/local/freesurfer/subjects/meridian_tmp/meridianStats/spmT_0003.img
```

Again, replace subject numbers (‘001’) and path to the stats image as appropriate.

You can also interactively do this from the tksurfer menu e.g.

```bash
tksurfer 001 lh inflated
```

then `File --> Load` Overlay and in the dialog box navigating to your stats file AND your `register.dat` in the two file entry bits of the dialog box. Note the multiple overlay fields (presumably for creating multiple overlays…)

You now should be able to create SPM overlays!

## Canonical brains

If you want to make an SPM overlay of normalised data, then you’re going to want a canonical SPM surface from the canonical SPM brain. You can do this – I presume – by running autorecon-all on the canonical SPM brain, or you can try using the surface that comes with the package `spm_surfrend` which can be found [here](http://spmsurfrend.sourceforge.net/)

You don’t really need the whole package – or at least we didn’t. What you need is the ‘SPM Canonical Brain Surface’ from the [downloads page](http://spmsurfrend.sourceforge.net/Downloads/Downloads.html). If you make a new subject directory in `/usr/local/Freesurfer/subjects` then you can use `tksurfer` to look at this e.g.

```bash
tksurfer canonical lh pial
```

You can then create overlays in the same way, but from normalised data.

If you want to download and use SPM SurfRend in its entirety, then beware that some of the functionality calls a Matlab function called `read_surf.m` that is not in the downloaded package. In fact this is contained in a distributed of FSL ROI tools to be found [here](http://froi.sourceforge.net/). If you download the tar file, and add it to Matlab path then SPM_surfrend now runs to completion. You do NOT need to install all of FSL.
