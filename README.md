# Structural Connectivity Processing Workflow

This repository provides a Snakemake workflow to produce structural connectivity networks from diffusion MRI data and FreeSurfer parcellations. 


## Installation

*If you are using the [NeSI](https://www.nesi.org.nz) platform, please follow the [NeSI related documentation](NESI.md).*

To run this workflow on your workstation, you need to install the following softwares:

- `mamba`, a fast cross-platform package manager (see [installation instructions](https://mamba.readthedocs.io/en/latest/installation.htm))
- `apptainer`, a container system (see [installation instructions](https://apptainer.org/docs/admin/main/installation.html))
- `snakemake`, the workflow management system (see [installation instructions](https://snakemake.readthedocs.io/en/stable/getting_started/installation.html))
- `git`, the distributed version control system (see [download page](https://git-scm.com/downloads))

Clone this repository using:

```
git clone https://github.com/eclnz/connectomeSnake
```

## Setup: 
Then edit the configuration file `config/config.yml`, setting the following entries:

- the ethics prefix `ethics_prefix` for your input files,
- the input data folder `datadir`, which must follow the conventions below. 
- the results folder `resultsdir`,
- the path to your Freesurfer license `fs_license`, (by default is licence.txt in config/
- the path to the aquisition parameter file `aqcparams` and the index file `index` 
    - (see [--acqp](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/eddy/UsersGuide#A--acqp) and [--index](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/eddy/UsersGuide#A--index) sections in eddy user guide).
- the path to your `heudiconv` heuristic script (`heuristic` entry under `heudiconv` section).

You may want to edit other entries, in particular:


Once this configuration is finished, you can run `snakemake` to start the workflow.

Use a dry-run to check that installation and configuration is working:

```
srun --account=uoa03264 --qos=debug profiles/nesi/snakemake.sl -n
```

## Formats

The workflow assumes that input scan data are:

- folders or .zip files (you can mix both),
- stored in the `datadir` folder configured `config/config.yml`,
- they are named using the convention `<ethics_prefix>_<subject>_<session>`, where
  - `<ethics_prefix>` is set in [`config/config.yml`](config/config.yml),
  - `<session>` can be omitted, but will then be considered as `a`.

Within a input folder (or .zip file), only the parent folder of DICOM files will be kept when tidying the data.
Any other level of nesting will be ignored.

Once the workflow has completed, results are organised as follows:

* `+`: Files and folders generated by the pipeline *
* `>`: Files and folders specifically required by the pipeline *

The goal is to fully integrate this workflow with previous workflows such that it can produce all files in the directory tree.


```
<resultsdir>
└── bids
    ├── derivatives
    │   ├── eddy
    │   │   └── sub-<subject>
    │   │       └── ses-<session>
    │   │           └── dwi
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_cnr_maps.nii.gz
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_command_txt
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy.json
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_movement_over_time
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_movement_rms
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_outlier_free_data.nii.gz
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_outlier_map
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_outlier_n_sqr_stdev_map
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_outlier_n_stdev_map
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_outlier_report
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_parameters
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_post_eddy_shell_alignment_parameters
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_post_eddy_shell_PE_translation_parameters
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_residuals.nii.gz
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_restricted_movement_rms
>   │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_rotated_bvecs
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_shell_indicies.json
    │   │               ├── sub-<subject>_ses-<session>_<entities>_eddy.eddy_values_of_all_input_parameters
>   │   │               └── sub-<subject>_ses-<session>_<entities>_eddy.nii.gz
    │   ├── eddy_quad
    │   │   └── sub-<subject>_ses-<session>_<entities>
    │   │       ├── avg_b0.png
    │   │       ├── avg_b1000.png
    │   │       ├── avg_b2000.png
    │   │       ├── avg_b3000.png
    │   │       ├── cnr0000.nii.gz.png
    │   │       ├── cnr0001.nii.gz.png
    │   │       ├── cnr0002.nii.gz.png
    │   │       ├── cnr0003.nii.gz.png
    │   │       ├── eddy_msr.txt
    │   │       ├── qc.json
    │   │       ├── qc.pdf
    │   │       ├── ref_list.png
    │   │       └── ref.txt
    │   ├── eddy_squad
    │   │   ├── group_db.json
    │   │   ├── group_qc.pdf
    │   │   ├── ref.txt
    │   │   └── ref_list.png
    │   ├── hd_bet
    │   │   └── sub-<subject>
    │   │       └── ses-<session>
    │   │           └── anat
    │   │               ├── sub-<subject>_ses-<session>_<entities>_T1w_brain_mask.nii.gz
>   │   │               └── sub-<subject>_ses-<session>_<entities>_T1w_brain.nii.gz
    │   ├── **tractography**
    │   │   └── sub-<subject>
    │   │       └── ses-<session>
+   │   │            ├── sub-<subject>_ses-<session>_run-001_node_assignments.csv    
+   │   │            ├── sub-<subject>_ses-<session>_run-001_prop.const.txt
+   │   │            ├── sub-<subject>_ses-<session>_run-001_str_con_mat.csv
+   │   │            ├── sub-<subject>_ses-<session>_run-001_tck_100k.tck
+   │   │            ├── sub-<subject>_ses-<session>_run-001_WB_tracks_100K_wei.txt
+   │   │            ├── sub-<subject>_ses-<session>_run-001_WB_tracks_10M_wei.txt
+   │   │            └── sub-<subject>_ses-<session>_run-001_WB_tracks_10M.tck
    │   ├── mriqc
    │   │   ├── logs
    │   │   │   └── ...  # log files in case MRIQC crashes
    │   │   ├── sub-<subject>
    │   │   │   ├── figures
    │   │   │   │   ├── sub-<subject>_ses-<session>_<entities>_<suffix>.svg
    │   │   │   │   └── ...
    │   │   │   └── ses-<session>
    │   │   │       ├── <modality>
    │   │   │       │   └── sub-<subject>_ses-<session>_<entities>_<suffix>.json
    │   │   │       └── ...
    │   │   ├── dataset_description.json
    │   │   ├── quality_control.tsv
    │   │   ├── sub-<subject>_ses-<session>_qc.yaml
    │   │   ├── sub-<subject>_ses-<session>_<entities>_<suffix>.html
    │   │   └── ...
    │   ├── mrtrix3
    │   │   └── sub-<subject>
    │   │       └── ses-<session>
    │   │           ├── dwi
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_bias.mif
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_csf_response.txt
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_csffod_norm.mif
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_csffod.mif
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwi_brain_mask.mif
>   │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwi_brain_mask.nii.gz
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwi_brain.nii.gz
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwi_denoise.mif
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwi_mask.mif
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwi.mif
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwidnbc.mif
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwidnbcdegibbs.mif
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_dwidnbcdg.nii.gz
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_gm_response.txt
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_gmfod_norm.mif
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_gmfod.mif
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_mean_bO_dwi.nii.gz
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_nodif.nii.gz
    │   │           │   ├── sub-<subject>_ses-<session>_run-001_noise.mif
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_T1_rig_dwi.mat
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_T1_rig_dwi.txt
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_wm_response.txt
+   │   │           │   ├── sub-<subject>_ses-<session>_run-001_wmfod_norm.mif
+   │   │           │   └── sub-<subject>_ses-<session>_run-001_wmfod.mif
+   │   │           └── anat
+   │   │               ├── sub-gill_ses-a_run-001_dk_parcel_coreg.mif
+   │   │               ├── sub-gill_ses-a_run-001_dk_parcel.mif
+   │   │               ├── sub-gill_ses-a_run-001_T1_gmwm_coreg.mif
+   │   │               ├── sub-gill_ses-a_run-001_T1_5tt_coreg_visual.mif
+   │   │               ├── sub-gill_ses-a_run-001_T1_5tt_coreg.mif
+   │   │               └── sub-gill_ses-a_run-001_T1_5tt_nocoreg.mif
    │   └── synb0_disco
    │       └── sub-<subject>_ses-<session>_<entities>
    │           ├── ANTS0GenericAffine.mat
    │           ├── ANTS1InverseWarp.nii.gz
    │           ├── ANTS1Warp.nii.gz
    │           ├── b0_all.nii.gz
    │           ├── b0_all.topup_log
    │           ├── b0_all_topup.nii.gz
    │           ├── b0_d_lin_atlas_2_5.nii.gz
    │           ├── b0_d_nonlin_atlas_2_5.nii.gz
    │           ├── b0_d_smooth.nii.gz
    │           ├── b0_u_lin_atlas_2_5_FOLD_1.nii.gz
    │           ├── b0_u_lin_atlas_2_5_FOLD_2.nii.gz
    │           ├── b0_u_lin_atlas_2_5_FOLD_3.nii.gz
    │           ├── b0_u_lin_atlas_2_5_FOLD_4.nii.gz
    │           ├── b0_u_lin_atlas_2_5_FOLD_5.nii.gz
    │           ├── b0_u_lin_atlas_2_5_merged.nii.gz
    │           ├── b0_u_lin_atlas_2_5.nii.gz
    │           ├── b0_u.nii.gz
    │           ├── epi_reg_d_ANTS.txt
    │           ├── epi_reg_d.mat
    │           ├── T1_mask.nii.gz
    │           ├── T1_norm_lin_atlas_2_5.nii.gz
    │           ├── T1_norm.nii.gz
    │           ├── T1_norm_nonlin_atlas_2_5.nii.gz
    │           ├── topup_fieldcoef.nii.gz
    │           └── topup_movpar.txt
    ├── sub-<subject>
    │   └── ses-<session>
    │       ├── dwi
    │       │   ├─sub-<subject>_ses-<session>_run-001_dwi.nii.gz
    │       │   ├─sub-<subject>_ses-<session>_run-001_dwi.json
    │       │   ├─sub-<subject>_ses-<session>_run-001_dwi.bvec
>   │       │   └─sub-<subject>_ses-<session>_run-001_dwi.bval
    │       ├── anat
    │       │   ├─sub-<subject>_ses-<session>_run-001_T1w.nii.gz
    │       │   └─sub-<subject>_ses-<session>_run-001_T1w.json
    │       └── sub-<subject>_ses-<session>_scans.tsv

    ~ Not currently updated with workflow progress ~
    ├── CHANGES
    ├── dataset_description.json
    ├── README
    ├── scans.json

    ~ Not currently created with workflow progress ~
    ├── participants.json
    └── participants.tsv
```

- `<resultsdir>` is the results directory configured in `config/config.yaml`
- `<subject>` is a subject,
- `<session>` is a subject's session,
- `<modality>` is a modality (e.g. `anat` or `dwi`),
- `<entities>` are BIDs entities (`task`, `run`, etc.),
- `<suffix>` is a BIDs suffix, either `T1w`, `T2w`, `dwi` or `bold`.

**TODO**
- Explain the QC files (copy from tidySnake?)
- Detail the key output files
- Add a note that eddy squad is only run if more than one recording
- Update CHANGES file when the workflow runs and files are changed.
- Update the scans.json file to include a list of all scans for all subjects
- Create the participants.json and .tsv file. Make this process as streamlined as possible.


## Workflow

The complete workflow consists of multiple steps depicted in the following graph.

![Workflow graph](rulegraph.png)

You will need to delete or comment out all print statements from your workflow before running

- Workflow graph generated with `srun --account=uoa03264 --qos=debug profiles/nesi/snakemake.sl --rulegraph | dot -Tpng > rulegraph.png`

The role of each step is the following:

- **convert_mask**
    - Converts a brain mask image (warped to DWI image from T1) from NIfTI format (.nii.gz) to MRtrix format (.mif).
- **convert_dwi**
    - Converts the eddy corrected DWI image (diffusion-weighted image) from NIfTI format to MRtrix format, also including the updated gradient directions (b-vectors) post eddy correction and the gradient strengths (b-values) for the diffusion MRI data.
- **response_function**
    - Estimates response functions for white matter, gray matter, and CSF from the DWI data using the dwi2response dhollander command in MRtrix. The response function is designed to estimate the expected signal from a voxel of homogenous and coherent tissue (csf, wm, gm).
- **estimate_fod**
    - This rule estimates Fiber Orientation Distributions (FODs) for white matter (WM), gray matter (GM), and cerebrospinal fluid (CSF) using multi-shell multi-tissue constrained spherical deconvolution (msmt_csd). It uses the tissue-specific response functions (calculated in the previous step) and the DWI data.
- **normalize_intensity**
    - The intensity of FOD images (white matter, gray matter, and CSF) are normalized. This step ensures that FOD images are comparable across different subjects and sessions by adjusting for any intensity variability.
- **rigit_registration**
    - The mean B0 image in DWI space is registered to the T1-weighted image to compute the transformation required to warp images in T1 space to DWI space. The registration matrix is computed and saved for use in the subsequent steps.
- **freesurfer_cross_sectional**
    - The input T1 image is ran through the full FreeSurfer segmentation pipeline, producing segmentations, and cortical surfaces. 
- **generate_5tt**
    - A 5-tissue-type (5TT) image is produced from from the FreeSurfer output. This image is then warped from T1 space into DWI space.
- **generate_streamlines**
    - Streamlines are seeded throughout the brain using Anatomically Constrained Tractography. The normalised white matter FODs are used to guide the streamlines, and the 5tt image is used to mask where streamlines can and cannot travel.
- **filter_streamlines**
    - The SIFT2 (Spherical-deconvolution Informed Filtering of Tractograms) algorithm weights the streamlines based on a measure of estimated white matter density. Streamlines with higher weights reflect a stronger biologically representative connection.
- **desikan_to_mrtrix**
    - The parcellation image generated my FreeSurfer in the form of the Desikan-Killiany (DK) atlas, is converted into a MRtrix compatible format. 
- **sift2_dk_connectome**
    - The sum of streamline strengths between brain regions in the DK atlas is computed between every combination of regions, producing an adjacency matrix (.csv). 


## Useful Snakemake options

View steps within workflow using rulegraph:

```
srun --account=uoa03264 --qos=debug profiles/nesi/snakemake.sl --forceall --rulegraph | dot -Tpdf > rulegraph.pdf
snakemake --forceall --rulegraph | dot -Tpdf > rulegraph.pdf
```

Use the [*local* profile](profiles/local/config.yaml), presetting many options to run the workflow locally:

```
snakemake --profile profiles/local
```

Inform `snakemake` of the maximum amount of memory available on the workstation:

```
snakemake --resources mem=48GB
```

Keep incomplete files (useful for debugging) from fail jobs, instead of wiping them:

```
snakemake --keep-incomplete
```

Run the pipeline until a certain file or rule, e.g. the `bias_correction` rule:

```
snakemake --until bias_correction
```

All these options can be combined and used with a profile, for example:

```
snakemake --profile profiles/local --keep-incomplete --until bias_correction
```

Unlock the folder, in case `snakemake` had to be interrupted abruptly previously:

```
snakemake --unlock
```

*Note: This last hint will be mentioned to you by `snakemake` itself.
Use it only when recommended to to so ;-).*


## Maintenance

TODO replace with currently available environments

The conda environment file [workflow/envs/mri.yaml](workflow/envs/mri.yaml) with pinned versions is generated from a version without versions [workflow/envs/mri_base.yaml](workflow/envs/mri_base.yaml).

You can update it using:

```
conda env create -f workflow/envs/mri_base.yaml -p ./mri_env
conda env export -p ./mri_env --no-builds | grep -v '^prefix:' > workflow/envs/mri.yaml
conda env remove -p ./mri_env
```
