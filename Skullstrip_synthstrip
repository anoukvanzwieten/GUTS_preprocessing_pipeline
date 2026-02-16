## Skull stripping with Synthstrip

Because the skull stripping software that halfpipe and fmriprep use (e.g., ANTS) do not work well on our data, we use Synthstrip (https://surfer.nmr.mgh.harvard.edu/docs/synthstrip/). This software is already installed in storm, so we just need to run the right command:

```
docker run --rm \
  -v /path/to/data:/data \
  freesurfer/synthstrip \
  -i /data/input.nii.gz \
  -o /data/output_brain.nii.gz
```
- `docker run`: initiates a container to run the program in
- `--rm`: removes the container after use (would accumulate over time otherwise)
- `-v` = volume mount. It connects a folder on the server to a folder inside the container.
- `-i` = input image (.nii format)
- `-o` = name of the output image

### Example for specific subjects
If we want to run this for specific subjects (2, 5 and 10) in our /dataGUTS2/ folder, we would use this command:
```
for sub in sub-gutsaumc0002 sub-gutsaumc0005 sub-gutsaumc0010; do
  infile="${sub}/ses-01/anat/${sub}_ses-01_T1w.nii.gz"
  if [ -f "$infile" ]; then
    docker run --rm \
      -v /dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/bids:/data \
      freesurfer/synthstrip \
      -i /data/$infile \
      -o /data/${sub}/ses-01/anat/${sub}_ses-01_stripped_brain_T1w.nii.gz
  fi
done
```
- The `-v` argument mounts the full BIDS dataset folder to `/data` inside the container.  
- Output images are written inside each subject’s `anat/` folder, following BIDS derivative naming: `_stripped-brain_T1w.nii.gz`.  
- The `if [ -f "$infile" ]` check prevents crashes if a file is missing.

If everything went well, you should now see the skull stripped file in the /anat directory of the subject you processed.

### Renaming the skull stripped images for fmriprep

fMRIPrep expects input directories that comply with the BIDS structure. To use the skull-stripped T1 images as input, we rename:

- The **original T1w images** → `_original` suffix (kept as backup)  
- The **skull-stripped images** → original BIDS T1w filenames

This allows fMRIPrep to read the skull-stripped images as if they were the originals.

```bash
# Base BIDS directory
BIDS_DIR=/dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/bids

# List of subjects to process
SUBS=("sub-gutsaumc0002" "sub-gutsaumc0005" "sub-gutsaumc0010")

for sub in "${SUBS[@]}"; do
    anat_dir="$BIDS_DIR/$sub/ses-01/anat"
    
    # Skip if folder doesn't exist
    [ -d "$anat_dir" ] || continue
    
    # Loop over T1w images
    for t1 in "$anat_dir/${sub}_ses-01_T1w.nii.gz"; do
        [ -f "$t1" ] || continue
        
        # Rename original T1 to _original
        mv "$t1" "${anat_dir}/${sub}_ses-01_T1w_original.nii.gz"
        
        # Rename skull-stripped image to normal BIDS T1w name
        stripped="${anat_dir}/${sub}_ses-01_stripped-brain_T1w.nii.gz"
        if [ -f "$stripped" ]; then
            mv "$stripped" "$t1"
        fi
    done
done
```

**Before / After:**

| Before | After |
|--------|-------|
| `sub-gutsaumc0002_ses-01_T1w.nii.gz` | `sub-gutsaumc0002_ses-01_T1w_original.nii.gz` |
| `sub-gutsaumc0002_ses-01_stripped-brain_T1w.nii.gz` | `sub-gutsaumc0002_ses-01_T1w.nii.gz` |


