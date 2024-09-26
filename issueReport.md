# Issues in ConnectomeSnake Pipeline

Started recording issues from 25/Sep/2024

## 1. Error in rule normalize_intensity:

### WHO: gilctrl1_ses-a_run-001
### WHAT:
uoa03264/ecla535/connectomeSnake/logs/nesi/50386248-normalize_intensity.out
mtnormalise: [ERROR] Non-positive tissue balance factor was computed. Balance factors: -nan -nan -nan
[Wed Sep 25 13:17:21 2024]

### WHY
1. fod.mif images for this particular subject are incorrect, containing wild values.
2. DWI image in | dwi.mif -> fod.mif | is not brain extracted.
3. *A.* All functions use non-brain extracted images. This could contribute to the problem.
3. *B.* This may be mitigated by the fact that the brain mask is supplied to the dwi2response, dwi2fod, mtnormalise.

Will continue to monitor

## 2. Error in freesurder_cross_sectional

### WHO: ALL

### WHAT:
uoa03264/ecla535/connectomeSnake/logs/nesi/50388240-freesurfer_cross_sectional.out
recon_all: [ERROR]: You are trying to re-run an existing subject with (possibly)
 new input data (-i). If this is truly new input data, you should delete
 the subject folder and re-run, or specify a different subject name.
 If you are just continuing an analysis of an existing subject, then 
 omit all -i flags.
[Wed Sep 25 14:48:25 2024]

### WHY
1. [160, 161] 
output:
        directory("{resultsdir}/bids/derivatives/freesurfer/sub-{subject}_ ses-{session}")
                                                                            ↑
                                                                            Session 2 times
### Fix
Changed the string and it ran through the freesurfer subjects overnight. This allowed it to progress to 5ttgen.

## 3. Error in generate_5tt

### WHO: ALL

### WHAT: 
uoa03264/ecla535/connectomeSnake/logs/nesi/50417220-generate_5tt.out
/usr/bin/bash: line 1: 5ttgen: command not found

### WHY: 
5ttgen doesn't have the mrtrix docker so the shell cannot find the function.

## 4. error in desikan_to_mrtrix
labelconvert: scanning DICOM folder "/nesi/noba...rfer/sub-gil1_ses-a_run-001"... ......done
labelconvert: [ERROR] no DICOM images found in "/nesi/nobackup/uoa03264/ecla535/dti_pipeline_data/processed/bids/derivatives/freesurfer/sub-gil1_ses-a_run-001"
**WHY** A folder is provided to snakemake, but the A folder was provided to the function instead of an image.
Needs to be like this {input.FS_dir}/mri/aparc+aseg.mgz not {input.aparc_aseg_mgz} which is just a folder. Specifying a folder causes it to scan for dicoms.

## 5. error in sift2_df_connectome
tck2connectome: [WARNING] The following nodes are missing from the parcellation image:
tck2connectome: [WARNING] 36, 43
tck2connectome: [WARNING] (This may indicate poor parcellation image preparation, use of incorrect or incomplete LUT file(s) in labelconvert, or very poor registration)

The fs_default.txt had the incorrect left and right thalamus (36, 43) entries:
Left Thalamus Proper -> Left Thalamus
Right Thalamus Proper -> Right Thalamus
