## fmriprep
------------- THIS PIPELINE IS STILL IN CONSTRUCTION -------------

Note that we are still working out the details of our pipeline. The current setup is created to run without skull stripping, as we are inputing our already skullstripped T1 files. If you want to use a raw T1 file, remove the `--skull-strip-t1w skip \` line from the code. 

```
#!/bin/bash

root="/dataGUTS2/GUTS/WP3/Data_collection/carmen_anouk/tmp"
LOGDIR="/data00/GUTS/anouk/logs/fmriprep"
mkdir -p "$LOGDIR"

fmriprep-docker ${root}/bids ${root}/derivative_original participant \
    --nprocs 30 \
    --omp-nthreads 8 \
    --slice-time-ref 0.5 \
    --output-spaces MNI152NLin2009cAsym:res-3 \
    --fs-license-file /usr/local/freesurfer/license.txt \
    --skull-strip-t1w skip \
    2>&1 | tee "$LOGDIR/fmriprep_$(date +%Y%m%d_%H%M).log"
```

If you are creating it yourself, make it excecutable with `chmod +x` and check if it worked through `ls -la`. 
To start fmriprep, run the following command:
```
tmux new -s fmriprep
source ~/venv_fmri_preproc/bin/activate
./run_fmriprep.sh
```

`tmux` is a terminal session manager that allows us to keep the process alive, even when we are not connected to the server.
You can detach from it using `Ctrl + b`, and then type `d`. Because it starts a new terminal session, we need to enter our
venv again when we are inside it. 

After you are finished, kill the terminal session using
```
tmux kill-session -t fmriprep
```
