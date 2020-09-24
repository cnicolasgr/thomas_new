# THOMAS: Thalamus-Optimized Multi-Atlas Segmentation (version 2.0)
Segmentation of the thalamus into 12 nuclei using White-matter-nulled MPRAGE images and PICSL's joint label fusion. Note that this version supports the much faster cropped FOV version (called ST THOMAS in ISMRM abstracts) and the slower original full FOV (THOMAS) using v2 and v0 arguments for -a respectively. 

<b>If you wish to run THOMAS on conventional MPRAGE (where CSF is dark) as opposed to White-matter-nulled MPRAGE which is a special pulse sequence (a WIP on GE, FGATIR on Philips, modified MPRAGE on Siemens), please contact me for further instructions at manojar@email.arizona.edu. Just running THOMAS on conventional MPRAGE will likely not work.</b>

Update 8/31/2020. A single atlas version of THOMAS suitable for any input (conventional MPRAGE, FLAIR) is available here https://zenodo.org/record/3966531. It is slightly less accurate than running THOMAS multi atlas with majority voting but for the larger nuclei like Pul, MD, VLP, this is miniscule. 



## Requirements
- [ANTs](https://github.com/ANTsX/ANTs/releases)
- [FSL](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FslInstallation)
- [convert3d](http://www.itksnap.org/pmwiki/pmwiki.php?n=Downloads.C3D)
- [PICSL-MALF](https://www.nitrc.org/frs/?group_id=634)
 
Note: you might have to install ITK from scratch to make PICSL-MALF work esp running on CentOS. Ubuntu seems to work fine.

## Installation
- git Due to large files, you will need to install git lfs and then download. Else you will only get soft links and not the actual files. Please email manojsar@email.arizona.edu if you have any issues
- python require.py Right now PYTHON 3 is NOT SUPPORTED so please point python to PYTHON 2

## Usage
- set an environment variable THOMAS_HOME in .cshrc to where you install thomas (e.g. ~/thomas_new)
- use the thomas_csh wrapper provided for WMn MPRAGE data
  
  Usage: thomas_csh WMnMPRAGE_file \<ro/lo\> 

  Note 1: the first argument is the white matter nulled MPRAGE file in NIFTy nii.gz format. Make sure it is just the file name and not a full path (e.g. wmn.nii.gz not ~foo/data/case1/wmn.nii.gz. Basically, run the script in the directory where the file is located. If you have each subject in a directory, go to each directory and call thomas_csh wmn.nii.gz \<r> which can be in a simple csh script
  
  - use the thomas_csh_mv wrapper provided for standard MPRAGE or T1 (FSPGR in GE) data
  
  Usage: thomas_csh_mv MPRAGEorT1_file \<ro/lo\> 
  
  Note 2: the second argument if set to ro/lo would only segment the right/left side (default is both left and right)
- For full usage of THOMAS, type python THOMAS.py -h
- Example: python THOMAS.py -a v2 -p 4 -v --jointfusion --tempdir temp wmnmpragefilename ALL
	- tempdir is often useful in case something goes wrong, you can resume from previous attempts. Delete this directory if you want to rerun the full segmentation or it will just use the warps from here.
- jointfusion calls the original implementation of the [PICSL MALF algorithm](https://www.nitrc.org/projects/picsl_malf) instead of antsJointFusion.  This was used in the publication. For MACS, it will skip PICSL due to library issues and just call antsJointFusion which is almost identical but a bit slower.
- swapdimlike.py - reorients an image to match the orientation of another
- form_multiatlas.py - combines many independent labels together into a single atlas
