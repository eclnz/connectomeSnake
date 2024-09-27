# Requested Memory For Each Step
- sacct -j <job_id>
- I've been grabbing the job_id from the log outputs.
    - <job_id>-<rule_name>.out

## convert_mask **REDUCE**
    - 108K / 512M -> 256M
    - 48K  / 512M
    - 116K / 512M
    
## response_function **REDUCE**
    - 240K / 4,000M  -> 256M
    - 228M / 4,000M 
    - 264K / 4,000M

## estimate_fod
    - 585M / 4,000M
    - 585M / 4,000M
    - 588K / 4,000M
    
## normalize_intensity **REDUCE**
    - 0K / 4,000M ? -> 256M
    - 0K / 4,000M ?
    
## freesurfer_cross_sectional
    - 2,234M / 4,000M
    - 2,221M / 4,000M
    - 2,123M / 4,000M
    
## rigid_registration
    - 1,336M / 4,000M
    - 1,325M / 4,000M
    - 1,326M / 4,000M
    
## generate_5tt
    - 1,412M / 4,000M
    - 1,286M / 4,000M
    - 2,006M / 4,000M
    
## generate_streamlines
    - 17.72M / 4,000M (100,000) streamlines
    - 17.78M / 4,000M (100,000) streamlines

## filter_streamlines   **INCREASE**
    - 3,408M / 4,000M (100,000) streamlines -> 6000M
    - 2,949M / 4,000M (100,000) streamlines

Will delete streamlines and increase to 10 million (a good target) to see how this affects memory utilisation.
    
## desikan_to_mrtrix    **REDUCE**
    - 84K  / 4,000M -> 256M
    - 164K / 4,000M
    - 76K  / 4,000M
    
## sift2_dk_connectome  **REDUCE**
    - 176K / 4,000M -> 256M
    - 100K / 4,000M