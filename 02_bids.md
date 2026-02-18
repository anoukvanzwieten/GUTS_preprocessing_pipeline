### Overview
This workflow describes how to:
1. Restructure raw YODA data
2. Run bidsmapper (define mapping)
3. Run bidscoiner (convert to BIDS)
4. Validate the dataset
5. Deface the data

Link to [Leonardo's github](https://github.com/leonardocerliani/GUTS_fmri_preproc/tree/main/TUT/05_full_pipeline)

## Transforming the data
We will transform the data to be compliant with bids using bidscoiner, a program that also transforms the data into
nifti format which is a nice benefit. However, it expects the structure to be as follows:

```
PARREC/
├── sub-gutsaumc0001
│   ├── ses-01
│   │   ├── [all the PARREC files here]
│   ├── ses-02
│   │   ├── [all the PARREC files here]
```

Unfortunately this is not the structure that we have. For now, the data from yoda is formatted like this:
```
├── sub-gutsaumc0001
│   ├── session-1
│   │   ├── anat
│   │   │    ├── [some PARREC files here]
│   │   ├── beh
│   │   ├── func
│   │   │    ├── [some PARREC files here]
```

This is a bit annoying so we need to make a plan of how to adjust this in the future. For now, we need 
to get the PARREC files from anat and func, and move them into our PARREC folder. Additionally, we want 
to take the behavior files and put them outside of the subject folder, so we place them in BEHAV.

The code to change these is here:
```
cd /dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/yoda

for sub in sub-*; do
  # create correct destination folder
  mkdir -p ../PARREC/$sub/ses-01
  mkdir -p ../BEHAV/$sub/ses-01

  # move PARREC files
  mv "$sub/session-1/anat/"* ../PARREC/$sub/ses-01/
  mv "$sub/session-1/func/"* ../PARREC/$sub/ses-01/

  # move behavior files
  mv "$sub/session-1/beh/"* ../BEHAV/$sub/ses-01/
done
```
With this code we move all files into a BIDS-compatible structure (`ses-01`) inside the PARREC folder.

Now (after checking if the move went as planned) we can remove the (empty) subject folder from the yoda folder
```
cd /dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/yoda
rm -r [SUBJECT]
```

## Bidsmapper
Bidsmapper is the program we use to create the data structure. It needs an interface, so we need to login through `x2goclient`. When you want to use bidsmapper, first activate your virtual preprocessing environment:
```
source venv_fmri_preproc/bin/activate
```
If you did not create the virtual environment yet, do so first with `python -m venv venv_fmri_preproc`. Also, especially 
if this is the first time you use bidsmapper, double check if you are in the docker group:
```
getent group docker
```
You should see your last name in the list. You need to be in this group to be able to use bidsmapper, so if you are not in there, contact me to be added. If you are in the group, we can install bidscoin (to check if it is already installed, use `which bidscoin`):  
```
pip install bidscoin
```

**Using bidsmapper**
In the terminal, go to the folder with the bids, PARREC and BEHAV folders. In our case this is 
```
cd /dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk
bidsmapper PARREC/ bids/
```
Bidsmapper will open a screen. 
<img width="797" height="717" alt="bidsmapper_image1" src="https://github.com/user-attachments/assets/a766e3c0-fbd7-4120-8366-b410328506bb" />

In this example, we have a couple of red lines. We can press edit to adjust the settings:
<img width="432" height="452" alt="bidsmapper_2" src="https://github.com/user-attachments/assets/518df320-02f8-43a4-906e-6ea5e7a13f22" />

Important:
- Check if data type is correct
- For func: The task name should be defined (sddt, trust, empmovie, or rest). **Also add this in the metadata field!**
- Run should be defined for sddt and trust (with a zero: 01)
- acq needs to be empty

Now you can save the setting, which will generate a `bidsmap.yaml` file that can be used to create the bids structure with correct naming. If you want to, [this link](https://bidscoin.readthedocs.io/en/latest/workflow.html) has more information about how to use the file for upcoming batches (with the same structure/settings).

## Bidscoiner
After creating the structure file with bidsmapper, bidscoiner is used to actually transform the data. To do so, make sure that you are still in your correct working directory and use:
```
bidscoiner PARREC/ bids/
```
The structure should now look like this:
```
/dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/bids$ tree -L 4
.
├── code
│   └── bidscoin
│       ├── bidscoiner.errors
│       ├── bidscoiner.log
│       ├── bidscoiner.tsv
│       ├── bidsmapper.errors
│       ├── bidsmapper.log
│       └── bidsmap.yaml
├── dataset_description.json
├── participants.json
├── participants.tsv
├── README
├── sub-gutsaumc0001
│   └── ses-01
│       ├── anat
│       │   ├── sub-gutsaumc0001_ses-01_T1w.json
│       │   └── sub-gutsaumc0001_ses-01_T1w.nii.gz
│       ├── func
│       │   ├── sub-gutsaumc0001_ses-01_task-empmovie_bold.json
│       │   ├── sub-gutsaumc0001_ses-01_task-empmovie_bold.nii.gz
│       │   ├── sub-gutsaumc0001_ses-01_task-rest_bold.json
│       │   ├── sub-gutsaumc0001_ses-01_task-rest_bold.nii.gz
│       │   ├── sub-gutsaumc0001_ses-01_task-sddt_run-01_bold.json
│       │   ├── sub-gutsaumc0001_ses-01_task-sddt_run-01_bold.nii.gz
│       │   ├── sub-gutsaumc0001_ses-01_task-sddt_run-02_bold.json
│       │   ├── sub-gutsaumc0001_ses-01_task-sddt_run-02_bold.nii.gz
│       │   ├── sub-gutsaumc0001_ses-01_task-sddt_run-03_bold.json
│       │   ├── sub-gutsaumc0001_ses-01_task-sddt_run-03_bold.nii.gz
│       │   ├── sub-gutsaumc0001_ses-01_task-trust_run-01_bold.json
│       │   ├── sub-gutsaumc0001_ses-01_task-trust_run-01_bold.nii.gz
│       │   ├── sub-gutsaumc0001_ses-01_task-trust_run-02_bold.json
│       │   └── sub-gutsaumc0001_ses-01_task-trust_run-02_bold.nii.gz
│       └── sub-gutsaumc0001_ses-01_scans.tsv
├── sub-gutsaumc0002
│   └── ses-01
│       ├── [etc]
```

## Bidsvalidator
Now, you can open a browser and search for the [bidsvalidator](https://bids-standard.github.io/bids-validator/) webpage. Upload your bids folder, and check whether there are any errors. Note that you will probably see many Warnings, this is not a problem for now, as long as there are no errors. If there are errors this will likely create issues with fmriprep etc, so fix them before continuing.

## Defacing the data
Bidscoiner also has a deface [plugin](https://bidscoin.readthedocs.io/en/latest/installation.html). If you have not used it yet, you can install it using:
```
pip install bidscoin[pydeface]
```

Now you can deface a subject of your choice by going to the `anat` folder within the subject folder and defacing the T1 file:
```
pydeface subjectid_ses-01_T1w.nii.gz
```

You can also process multiple subjects in parallel with
```
find bids -type f -name "*T1w*nii.gz" | xargs -n 1 -P 3 pydeface
```
It is set to 3 subjects in this example. If there are many subjects you can adjust the number of participants you want to process in parallel by increasing the `-P 3` argument, but be mindfull of your storm usage.

After, you can overwrite the original T1w with the defaced version as we don't need the regular ones anymore:
```
find bids -name "*_defaced.nii.gz" | while IFS= read -r defaced; do
  original="${defaced/_defaced/}"
  mv "$defaced" "$original"
done
```
**Note to self: figure out if defacer also defaces the functional data**
