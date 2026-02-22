## README

This repository is made to support the workflow of the GUTS preprocessing pipeline. While reading this, be aware that it is still a work in progress and there are many things to be finetuned. Additionally, much of the information in this page is based on [Leonardo's GUTS repository](https://github.com/leonardocerliani/GUTS_fmri_preproc/tree/main/TUT). While this repository is tailored specifically to the WP3 workflow, his repository contains more detailed technical explanations, additional resources, and troubleshooting guidance. We strongly encourage consulting his repository alongside this one for a more complete understanding of the pipeline.


### Repository structure
The information is ordered chronologically:
| Step | Description |
|------|------------|
| 00 | Basic requirements: setting up a storm account, checking access *(to be created)*  |
| 01 | [Data transfer yoda](01_data_transfer_yoda.md): creating a data password and using the ibridges GUI to import the data from yoda into storm. Specifies which format to use.   |
| 02 | [Bids](02_bids.md): using bidsmapper, coiner and validator to create bids compliant data structure. Includes deface plugin. |
| 03 | [Skullstrip](03_skullstrip_synthstrip.md): skullstripping with synthstrip |
| 04 | [Fixing of slicetimings in jsons](04_slicetimings_in_jsons.md) | Adding the slice aquisition times in the jsons for later slice timing correction.
| 05 | Fmriprep: preprocessing data with fmriprep *(to be created)* |
| 06 | GLM with FSL *(to be created?)* |

