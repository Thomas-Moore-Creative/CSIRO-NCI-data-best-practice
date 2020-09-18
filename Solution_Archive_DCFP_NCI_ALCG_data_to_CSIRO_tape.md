# A solution using rsync and parallel - "1TB per hour"
### Credit for the idea of this approach and assistance getting it to work goes to `Ondrej Hlinka` at CSIRO's Scientific Computing.  

## Description

Approach utilises: 

- newer version of rsync
- with specific cipher flag
- extra memory allocation setting
- NOT run from io queue our the DM node (NFS problem accessing `/datastore`) but from a screen on Ruby at the command line. 

###### NB: our goal here is to get data onto `/datastore` tape, hence the `ruby` approach.  If you are looking to reach other CSIRO storage the io queue on the DM node is possibly preferable.

#### example command
`cat /datastore/d/dcfp/cut_fake_filelist.txt | parallel -j 8 'rsync -ailPW -e "ssh  -T -c aes128-ctr"  USERNAME@gadi-dm.nci.org.au:/scratch/v14/USERNAME/better_practice_testing/test_data/{} /datastore/d/dcfp/fake_f6'`

#### setup steps to take
- open a `screen` on `ruby.hpc.csiro.au`
- `module load jemalloc`
- `module load rsync` 
- `module load parallel`


#### NB:
This gives you:

- the jemalloc memory allocation [http://jemalloc.net/](http://jemalloc.net/)
- an updated version of `rsync` => `rsync  version 3.2.3  protocol version 31`
- acccess to the very handy `GNU parallel`[https://www.gnu.org/software/parallel/](https://www.gnu.org/software/parallel/)

> **Reference:** O. Tange (2018): GNU Parallel 2018, Mar 2018, ISBN 9781387509881,
  DOI [https://doi.org/10.5281/zenodo.1146014](https://doi.org/10.5281/zenodo.1146014)


### my current approach is manual and at the command line
##### example steps

- Make a list of file to move over on Gadi <br>
`find ./*.tar -type f > ../f6_2012_filelist.txt`
- Move the filelist to CSIRO (using `rsync`) and format <br>
`cut -c 3- f6_2012_filelist.txt > cut_f6_2012_filelist.txt`<br>
- Run the 10 parallel rsyncs until the task is done <br>
```time cat /datastore/d/dcfp/NCI_file_lists/cut_f6_2012_filelist.txt | parallel -j 10 --results /datastore/d/dcfp/logs/ 'rsync -ailPW --log-file="/datastore/d/dcfp/logs/f6_2012_rsync.log.$(date +%Y%m%d%H%m%S)" -e "ssh  -T -c aes128-ctr"  USER@gadi-dm.nci.org.au:/scratch/v14/USER/tar_tmp/f6.WIP.c5-d60-pX-f6-20121101.20200831_153624/{} /datastore/d/dcfp/CAFE/forecasts/f6/'```

The above command will take the formatted filelist as input to a `parallel` job with 10 streams, saving `parallel` results (`stderr`, `stdout`) to `/logs`, using the `-ailPW` flags (NB: `-W` is required for `Ruby`), logging `rsync` output to `/logs`, employing the `aes128-ctr` cipher, and executing across all filenames in `cut_f6_2012_filelist.txt` in the `/scratch/v14/USER/tar_tmp/f6.WIP.c5-d60-pX-f6-20121101.20200831_153624/` directory.  `GNU Parallel` will execute 10 streams at a time until the full job is done.

Some testing has been done to arrive at the choice of 10 streams.  However performance is almost certainly heavily depenent on network load at both ends.

# Results

### Range of performance on a real-world 1TB mini-forecast = `370 - 694MB/sec`<br> Performance on a real-world 11TB full forecast = `250MB/s`
NB: The Ruby "front-end" spinning disk is only 15TB (for the entire organisation) and it seems **annecdotally** that speeds on 11TB transfers are high (300-700MB/s) until we get close to filling that 15TB and then speeds crash?

# Overall this is at least one order of magnitude faster than previous serial `rsync` attempts.  An 11TB transfer to tape can occur in under 12 hours.  Approximately 1TB per hour.

NB: to be kind to users `dmput` is currently run manually as often as possible while the 11TB transfers are underway, but it appears that actual moves to tape only occur at night.


### Issues remain dealing with:
1. flagging failures
2. recovering from failures
3. more robust checks to enable confidence to delete files at NCI
