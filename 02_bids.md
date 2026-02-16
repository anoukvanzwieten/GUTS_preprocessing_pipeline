## Transforming the data
We will transform the data to be compliant with bids using bidscoiner, a program that also transforms the data into
nifti format which is a nice benefit. However, it expects the structure to be as follows:

```
PARREC/
├── sub-gutsaumc_0020
│   ├── ses-01
│   │   ├── [all the PARREC files here]
│   ├── ses-02
│   │   ├── [all the PARREC files here]
```

Unfortunately this is not the structure that we have. For now, the data from yoda is formatted like this:
```
├── sub-gutsaumc_0020
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
With this code we also rename session-1 to ses-01 to be bids compliant. 

Now (after checking if the move went as planned) we can remove the (empty) subject folder from the yoda folder
```
cd /dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/yoda
rm -r [SUBJECT]
```

### Bidsmapper



