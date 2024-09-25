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

## 2 
