## Slice timing correction
In order to apply slice timing correction, fmriprep needs to know at which times the slices are acquired. We need to manually put them in the
json files, so fmriprep can read them. This is created with many thanks to [Miguel](https://github.com/mapdasilva/PrataLab_SBL_Collab/tree/main/Preprocessing_Notes_and_Fixes).
Before we begin, make sure you are in your preprocessing environment with `source venv_fmri_preproc/bin/activate`.

### Data00
Again, 'data00' is where our code lives and from where we run the analysis. In your perferred folder on data00, create a file that holds the slice
timings with `nano slicetiming.json`. In this file, enter our slice timings (these are asending, no multiband):
```
{"SliceTiming": [
    0.000,
    0.055,
    0.110,
    0.165,
    0.220,
    0.275,
    0.330,
    0.385,
    0.440,
    0.495,
    0.550,
    0.605,
    0.660,
    0.715,
    0.770,
    0.825,
    0.880,
    0.935,
    0.990,
    1.045,
    1.100,
    1.155,
    1.210,
    1.265,
    1.320,
    1.375,
    1.430,
    1.485,
    1.540,
    1.595,
    1.650,
    1.705,
    1.760,
    1.815,
    1.870,
    1.925,
    1.980,
    2.035,
    2.090,
    2.145
  ]
}
```

Next, create a fix_jsons_slicetimings.py in the same directory using `nano`. Enter the following in the file:
```
#!/usr/bin/env python3

import json
import os
import glob

# Base directory: adjust if necessary
base_dir = "/dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/bids/"

# Find all bold JSONs
jsons = glob.glob(os.path.join(base_dir, 'sub*', '**', 'func', '*_task-*_bold.json'), recursive=True)

# Load slice timing
slicetiming_json = os.path.join(os.getcwd(), 'slicetiming.json')
with open(slicetiming_json, 'r') as f:
    data = json.load(f)
    slice_timing = data.get("SliceTiming")
    if slice_timing is None:
        raise KeyError(f"'SliceTiming' not found in {slicetiming_json}")

# Update each JSON
for json_file in jsons:
    try:
        with open(json_file, 'r') as f:
            data = json.load(f)
        data["SliceTiming"] = slice_timing
        with open(json_file, 'w') as f:
            json.dump(data, f, indent=4)
        # Print subject ID dynamically
        subject_id = os.path.basename(json_file).split('_')[0]
        print(f"Updated SliceTiming for {subject_id}: {json_file}")
    except Exception as e:
        print(f"Failed to update {json_file}: {e}")
```

Make sure to **adjust the base_dir** to where your bids data lives. Also, this code expects the following data structure:
```
base_dir
├── sub[]
│   └── [] (in our case this is ses01, optional depending on your dataset)
│       ├── func
│       │   ├── [this is where the .json files live, in *_task-*_bold.json format]
```
Check if this is correct. You can adjust the `jsons = glob.glob(os.path.join(base_dir, 'sub*', '**', 'func', '*_task-*_bold.json'), recursive=True)` 
line to your data structure if necessary.

To run the script, run this command from the directory in data00 where both slicetiming files live:
```
python fix_jsons_slicetimings.py
```
