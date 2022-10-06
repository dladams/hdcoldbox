# hdcoldbox
David Adams  
October 2022

Software to analyze data from the 2021 and 2022 horizontal-drift cold box tests.


## Installation

First install *dunerun*, set it up and install *duneproc* following the instructions in the
[*duneproc* README](https://github.com/dladams/duneproc/blob/master/README.md).
Then clone this package ([hdcoldbox](https://github.com/dladams/hdcoldbox)
in the area where you want to run, and set up to use duneproc as follows:

<pre>
cd &lt;some-dir>
git clone https://github.com/dladams/hdcoldbox.git
cd hdcoldbox
./start-shell
</pre>
The last command starts a shell with the environment (exe, library, fcl path and more) specified by the dunesw release and the local build of duneproc.
It also replaces the xroot libraries replaced with a version supports streaming of hdf5 data.
One side effect I see of this replacement is that git commands no longer functions properly.
I handle this by running them after exiting the shell (exit command) or running them from a different terminal.

## Available commands

First make sure we have the VOMS proxy needed to access event data files:
<pre>
duneproc> dunerun-check-proxy
subject   : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams/CN=1184421617
issuer    : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams
identity  : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams
type      : RFC compliant proxy
strength  : 1024 bits
path      : /tmp/x509up_u6585
timeleft  : 10:00:05
</pre>

The *duneproc* hdcb help tells what commands are available for HD codbox data:
<pre>
duneproc> duneprocHelp hdcb

2021, 2022 CERN HD coldbox commands:
  hdcbFindFiles - Find event data files for a run
  hdcbRunTime - Find the date and time for a run

duneproc> 
</pre>
Some of the general duneproc commands may also be of interest:
<pre>
duneproc> duneprocHelp general

getGridProxy - Fetches a kerberos-based grid certificate.
findFileXroot - Find an event data file in xrootd.
findFilePnfs - Find an event data file in dcache.
findFileCache - Find a file in local file cache DUNEPROC_CACHE_PATH.
stageDuneDataset - Stage a dataset (copy files to dcache disk).
cacheDuneDataset - Cache a dataset (copy files to local disk).

duneproc> 
</pre>

As an example, We generate data quality plots for one event in a recent coldbox run, run 16178 with 
2 us data in the final APA1 test.
To list the files for that run:
<pre>
duneproc> hdcbFindFiles -h
Usage: /home/dladams/proc/install/v09_56_00d00/duneproc/bin/hdcbFindFiles RUNPAT DIR
  RUNPAT is run number or RUN_FFFF where FFFF is the file index.
  DIR is the directory to write the file list.
  If DIR="-", /home/dladams/data/dune/datasets/hdcb is used.
duneproc> hdcbFindFiles 16178 -
np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5
np04_coldbox_run016178_0001_dataflow0_20220927T101342.hdf5
np04_coldbox_run016178_0002_dataflow0_20220927T101404.hdf5
Dataset written to /home/dladams/data/dune/datasets/hdcb/hdcb016178.txt
duneproc> 
</pre>
From the file name, we see the dataset name is hdcb016178 and can stage the files like this:
<pre>
duneproc> stageDuneDataset hdcb016178
subject   : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams/CN=2674166866
issuer    : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams
identity  : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams
type      : RFC compliant proxy
strength  : 2048 bits
path      : /tmp/x509up_u6585
timeleft  : 10:59:54

np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5
np04_coldbox_run016178_0001_dataflow0_20220927T101342.hdf5
np04_coldbox_run016178_0002_dataflow0_20220927T101404.hdf5
QUERY: file_name in (np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5, np04_coldbox_run016178_0001_dataflow0_20220927T101342.hdf5, np04_coldbox_run016178_0002_dataflow0_20220927T101404.hdf5)
Dataset definition 'dladams_hdcb016178' has been created with id 762182
Staging command: samweb prestage-dataset --node=jupyter-dladams.fnal.gov --defname=dladams_hdcb016178

Log is /home/dladams/data/dune/datasets/logs/stage_hdcb016178.log

Displaying log at /home/dladams/data/dune/datasets/logs/stage_hdcb016178.log.
Interrupt at any time with ^C.
Staging dataset dladams_hdcb016178
Fri Sep 30 13:33:02 UTC 2022

Started project prestage_jupyter_dladams_hdcb016178_20220930133303
Started consumer processs ID 20919585
File np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5 is staged
File np04_coldbox_run016178_0002_dataflow0_20220927T101404.hdf5 is staged
File np04_coldbox_run016178_0001_dataflow0_20220927T101342.hdf5 is staged
Stopped project prestage_jupyter_dladams_hdcb016178_20220930133303

Fri Sep 30 13:33:14 UTC 2022
Done
^C
duneproc> 
</pre>
After the comments about about displaying the log, the command is just following that log and
can be interrupted with control-C at any time without affecting the progress of that staging.
Look at the log or the [staging monitor](https://samweb.fnal.gov:8483/station_monitor/dune/stations/dune/projects)
to check the progress of the job.
Files listed in the log are on disk and ready for processing.

If you want to process a particular event, say event 5 in run 16178, the you can use the implicit dataset name.
To see the file name and event offset:
<pre>
duneproc> findRunFiles hdcb 16178 5
np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5 4
duneproc> 
</pre>

To stage that file, use the implicit dataset file name:
<pre>
duneproc> stageDuneDataset hdcb-16178-5
subject   : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams/CN=362266381
issuer    : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams
identity  : /DC=org/DC=cilogon/C=US/O=Fermi National Accelerator Laboratory/OU=People/CN=David Adams/CN=UID:dladams
type      : RFC compliant proxy
strength  : 2048 bits
path      : /tmp/x509up_u6585
timeleft  : 10:39:36

QUERY: file_name = np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5
Dataset definition 'dladams_hdcb-16178-5' has been created with id 762422
Staging command: samweb prestage-dataset --node=jupyter-dladams.fnal.gov --defname=dladams_hdcb-16178-5

Log is /home/dladams/data/dune/datasets/logs/stage_hdcb-16178-5.log

Displaying log at /home/dladams/data/dune/datasets/logs/stage_hdcb-16178-5.log.
Interrupt at any time with ^C.
Staging dataset dladams_hdcb-16178-5
Tue Oct  4 14:36:59 UTC 2022

Started project prestage_jupyter_dladams_hdcb-16178-5_20221004143700
Started consumer processs ID 20930965
File np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5 is staged
Stopped project prestage_jupyter_dladams_hdcb-16178-5_20221004143700

Tue Oct  4 14:37:11 UTC 2022
Done
duneproc> 
</pre>

The script *doOneEvent* may be used to process a single event.
The first argument is the *action path*,  slash-separated base names of fcl config file that specify the processing.

The first action here configures the raw data decoding and channel map which can change between run periods.
For some run periods, there are also separate maps based on soldered ends and wire positions.
This action  can be the base name of one of the *hdread_\*.fcl* here or the script create-fcl can be used to copy
one of these based on run value.
As demonstrated here, the special name *hdread* (or *hdreadwi* or *hdreadse*) can be used with the *doOneEvent*
to select the readout fcl using the run number.

The second action can be any of the following:
* hdproc -- Generate raw event displays and noise plots
* hdproc-cnr -- Event displays and noise vs. channel before and after CNR (correlated noise removal).
* wfraw -- raw waveforms
* distraw -- ADC distributions for each channel
* dump -- Text dump of the raw ADC codes for the first few samples

To make raw data noise plots for run 16178 event 5:
<pre>
duneproc> ./doOneEvent hdread/hdproc 16178 5 retry
Creating fcl.
Created hdread.fcl
Skipping hdproc
Created fcl: hdread.fcl
duneproc hdread/hdproc hdcb-016178-005/event005 retry
Processing hdcb event 005 in file np04_coldbox_run016178_0000_dataflow0_20220927T101320.hdf5 using offset 4
RUNDIR = hdread/hdproc/hdcb-016178-005/event005
Removing hdread/hdproc/hdcb-016178-005/event005
Run directory: hdread/hdproc/hdcb-016178-005/event005
Taking input files from rawinfiles.txt
Copying hdread.fcl from submission directory.
Copying hdproc.fcl from submission directory.
makeFcl: Created event005.fcl
Not found: /home/dladams/proc/run01/hdcoldbox/local.fcl
Not found: /home/dladams/proc/run01/hdcoldbox/dbg.fcl
.
.
.
Run directory: hdread/hdproc/hdcb-016178-005/event005
ARGS: -c run.fcl -s -n 1 --nskip 4
Command: lar -c run.fcl -S infiles.txt -n 1 --nskip 4 --no-output
Copying view.ipynb to hdread/hdproc/hdcb-016178-005/event005
Creating output directory /home/dladams/xfer/2022/1004/hdread/hdproc/run016178/event005
Output directory: /home/dladams/xfer/2022/1004/hdread/hdproc/run016178/event005
total 12051
1133 -rw-r--r--. 1 dladams fnalgrid 1159412 Oct  4 14:22 adcprp_tpp0c_run016178_evt000005.png
1861 -rw-r--r--. 1 dladams fnalgrid 1904832 Oct  4 14:22 adcprp_tpp0u_run016178_evt000005.png
1863 -rw-r--r--. 1 dladams fnalgrid 1907051 Oct  4 14:22 adcprp_tpp0v_run016178_evt000005.png
1127 -rw-r--r--. 1 dladams fnalgrid 1153084 Oct  4 14:22 adcprp_tpp0z_run016178_evt000005.png
1137 -rw-r--r--. 1 dladams fnalgrid 1163986 Oct  4 14:22 adcraw_tpp0c_run016178_evt000005.png
1864 -rw-r--r--. 1 dladams fnalgrid 1908732 Oct  4 14:22 adcraw_tpp0u_run016178_evt000005.png
1867 -rw-r--r--. 1 dladams fnalgrid 1910868 Oct  4 14:22 adcraw_tpp0v_run016178_evt000005.png
1130 -rw-r--r--. 1 dladams fnalgrid 1156790 Oct  4 14:22 adcraw_tpp0z_run016178_evt000005.png
  27 -rw-r--r--. 1 dladams fnalgrid   26960 Oct  4 14:22 chmet_pedrawrms_tps0_run016178_evt000005.png
  18 -rw-r--r--. 1 dladams fnalgrid   18351 Oct  4 14:22 chmet_ped_tps0_run016178_evt000005.png
  27 -rw-r--r--. 1 dladams fnalgrid   27437 Oct  4 14:22 chmet_samrms_tps0_run016178_evt000005.png
</pre>
The script copies the generated png files to a subdirectory of $HOME/xfer/YYYY where YYYY is the current year
if that directory exists.
Those files are listed at the end of the above log.
The adcraw* plots are the raw noise vs channel vs tick event displays.
The adcprp apply a very rough calibration and probably should be ignored.
The chmet ped and pedrawrms files respectively hold the pedestal and pedestal RMS (noise) vs channel.
The samrms file has the sample RMS with the same questionable calibration.

Replace hdproc with hdproc-cnr to generate noise plots with CNR (correlated noise removal).
Now the adcprp plots have CNR noise vs channel-tick and samrms has noise vs channel.
These plots are generated with a calibration gain of one and so the units are ADC counts.

The above job loops over all events in the file and skips the one of interest.
This is because event skipping and restricted event counts are not supported in lar for the HD coldbox data.
See [duneprototypes issue 8](https://github.com/DUNE/duneprototypes/issues/8).
## Viewing output
The above and most other jobs produce image files. In Jupyter, these can be viewed by navigating to the run directory in the file browser
and opening the file(s) of interest.
To view all images on a single page, open and run the view notebood (view.ipynb) in that directory.

## Notes

October 4, 2022: The above are confirmed in a update of v09_58_02d00 and presumed to work in later versions.

Run lists for the 2022 APA tests can be found [here](https://docs.google.com/spreadsheets/d/1wh8qZTL6iZGQxaHKIjkvGPs81b2g6ReBHMYqCpT59yo/edit#gid=419431128)

Run summary for 2022 is [here](https://docs.google.com/spreadsheets/d/1wh8qZTL6iZGQxaHKIjkvGPs81b2g6ReBHMYqCpT59yo/edit#gid=1659477561).
