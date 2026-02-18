## Skull stripping with Synthstrip

Because the skull stripping software that halfpipe and fmriprep use (e.g., ANTS) do not work well on our data, we use [Synthstrip](https://surfer.nmr.mgh.harvard.edu/docs/synthstrip/) instead. For this step, we are working in 2 locations:

### Location 1: /dataGUTS2/path/to/your/data/folder
`dataGUTS2` is our privacy-protected storage mount, and the location where our data is BIDS dataset is stored. It is important that we have a subjects_list_tobestripped.txt file containing the subject IDs of the subjects we want to skullstrip. If it does not yet exist, create one with `touch subjects_list_tobestripped.txt` and adjust it with `nano subjects_list_tobestripped.txt`. After entering the subjects, write them out with `^O`, press enter if you are happy with the file name, and exit with `^X`. Each subject should have its own row:
```
sub-gutsaumc0001
sub-gutsaumc0002
sub-gutsaumc0003
sub-gutsaumc0004
etc.
```

### Location 2: /data00/path/to/your/code/folder
`data00` is our faster, Linux-based environment where we run analyses. 
We use three files: `do_skullstrip.sh`, `launch_skullstrip.sh` and `synthstrip-docker`. Additionally, we also need to have `N4BiasFieldCorrection`. For us, they are located within `data00/GUTS/carmen_anouk/code/synthstrip`. If you want to have them in your own repository, you can copy them towards your desired location:
```
cp -r /data00/GUTS/anouk/code/synthstrip/ path/to/your/destination/folder
```
The reason you might want to do this is because the paths in our files are specified for our data (e.g., `Data_collection/carmen_anouk`. If you are working in your own path, you need to adjust a few settings:

**in `do_skullstrip.sh`, you should adjust:**
- `orig_root`
- `dest_root`
- `ANTSPATH` → *(path to N4BiasFieldCorrection location)*

**in `launch_skullstrip.sh`, adjust**
- `subject_list`
- `root`

After the paths are correct, can run the program (from wherever you have the synthstrip files in data00):
```
./launch_skullstrip.sh
```

### Renaming the skull stripped images for fmriprep

fMRIPrep expects input directories that comply with the BIDS structure. This creates a problem, because we want it to use our already skullstripped images instead of letting it do the stripping itself. Therefore, we need to adjust the naming to the format fmriprep expects. This is not ideal and could create confusion in the future, so we should create readme files for when others want to use our data.

To use the skull-stripped T1 images as input, we rename the **skull-stripped images** to the original BIDS T1w filenames. This allows fMRIPrep to read the skull-stripped images as if they were the originals. The original T1w images are still there with the `_RAW` suffix for backup.

```bash
BIDS_DIR=/dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/bids

while read -r sub; do

    anat_dir="$BIDS_DIR/$sub/ses-01/anat"
    [ -d "$anat_dir" ] || continue

    orig="$anat_dir/${sub}_ses-01_T1w.nii.gz"
    stripped="$anat_dir/${sub}_ses-01_T1w_N4_brain.nii.gz"

    # Only proceed if stripped image exists
    [ -f "$stripped" ] || continue

    # Overwrite T1w with skull-stripped version
    mv "$stripped" "$orig"

done < subjects_list_tobestripped.txt
```

**Before / After:**

| Before | After |
|--------|-------|
| `sub-gutsaumc0002_ses-01_N4_brain.nii.gz` | `sub-gutsaumc0002_ses-01_T1w.nii.gz` |


