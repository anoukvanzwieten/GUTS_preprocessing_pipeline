## Merge MNI templates

### Merging templates with ANTs
We would like to assess if a pediatric template is a better fit for our population. Because the templates are either from 10 - 14, or from 13 - 18.5, we merge and average them.

1. `conda activate antsenv` 

2. Registration:
```
antsRegistrationSyN.sh -d 3 \
  -f template1.nii.gz \
  -m template2.nii.gz \
  -o t2_to_t1_
```

3. Average:
```
fslmaths template1.nii.gz \
  -add t2_to_t1_Warped.nii.gz \
  -div 2 merged_template.nii.gz
  ```