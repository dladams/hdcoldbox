# hdcoldbox
David Adams  
September 2022

Software to analyze data from the 2021 and 2022 horizontal-drift cold box tests.

Run lists for the 2022 APA tests can be found [here](https://docs.google.com/spreadsheets/d/1wh8qZTL6iZGQxaHKIjkvGPs81b2g6ReBHMYqCpT59yo/edit#gid=419431128)

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

As an example, We generate data quality plots for one event ina recent coldbox run.
Comments on July 14, 2022 in the npo4-shifterassistant slack channel indicate that run 14206 has APA 2 cold data.
To list the files for this run:
<pre>
duneproc> hdcbFindFiles -h
Usage: /home/dladams/proc/install/v09_56_00d00/duneproc/bin/hdcbFindFiles RUNPAT DIR
  RUNPAT is run number or RUN_FFFF where FFFF is the file index.
  DIR is the directory to write the file list.
  If DIR="-", /home/dladams/data/dune/datasets/hdcb is used.
duneproc> hdcbFindFiles 14206
np04_coldbox_run014206_0000_dataflow0_20220714T072327.hdf5
duneproc> hdcbFindFiles 14206 -
Output file already exists: /home/dladams/data/dune/datasets/hdcb/hdcb014206.txt
duneproc> 
</pre>
The last command generates the dataset description file or reports if it already exists, as is the case here.
Note that you are required to have the directory $HOME/data/dune/datasets.

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

To make raw data noise plots for run 14206 event 1:
<pre>
duneproc> ./doOneEvent hdread/hdproc 14206 1
Creating fcl.
Created hdread.fcl
Skipping hdproc
Created fcl: hdread.fcl
Output file already exists: /home/dladams/data/dune/datasets/hdcb/hdcb014206.txt
duneproc hdread/hdproc hdcb014206/event001 1 0
FOUND input file list: /home/dladams/data/dune/datasets/hdcb/hdcb014206.txt
RUNDIR = hdread/hdproc/hdcb014206/event001
Run directory: hdread/hdproc/hdcb014206/event001
Taking input files from /home/dladams/data/dune/datasets/hdcb/hdcb014206.txt
Copying hdread.fcl from submission directory.
Copying hdproc.fcl from submission directory.
makeFcl: Created event001.fcl
.
.
.
Run directory: hdread/hdproc/hdcb014206/event001
ARGS: -c run.fcl -s --nskip 0
Command: lar -c run.fcl -S infiles.txt --nskip 0 --no-output
Output directory: /home/dladams/xfer/2022/0802/hdread/hdproc/run014206/event001
total 14427
1335 -rw-r--r--. 1 dladams fnalgrid 1366647 Aug  2 16:03 adcprp_tpp0c_run014206_evt000001.png
2245 -rw-r--r--. 1 dladams fnalgrid 2297893 Aug  2 16:03 adcprp_tpp0u_run014206_evt000001.png
2255 -rw-r--r--. 1 dladams fnalgrid 2309037 Aug  2 16:03 adcprp_tpp0v_run014206_evt000001.png
1333 -rw-r--r--. 1 dladams fnalgrid 1364375 Aug  2 16:03 adcprp_tpp0z_run014206_evt000001.png
1340 -rw-r--r--. 1 dladams fnalgrid 1371341 Aug  2 16:03 adcraw_tpp0c_run014206_evt000001.png
2248 -rw-r--r--. 1 dladams fnalgrid 2301560 Aug  2 16:03 adcraw_tpp0u_run014206_evt000001.png
2259 -rw-r--r--. 1 dladams fnalgrid 2313025 Aug  2 16:03 adcraw_tpp0v_run014206_evt000001.png
1338 -rw-r--r--. 1 dladams fnalgrid 1369556 Aug  2 16:03 adcraw_tpp0z_run014206_evt000001.png
  13 -rw-r--r--. 1 dladams fnalgrid   12428 Aug  2 15:43 chmet_pednoise_tps0_run014206_evt000001.png
  23 -rw-r--r--. 1 dladams fnalgrid   22831 Aug  2 16:03 chmet_pedrawrms_tps0_run014206_evt000001.png
  18 -rw-r--r--. 1 dladams fnalgrid   18236 Aug  2 16:03 chmet_ped_tps0_run014206_evt000001.png
  23 -rw-r--r--. 1 dladams fnalgrid   23248 Aug  2 16:03 chmet_samrms_tps0_run014206_evt000001.png
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

## Viewing output
The above and most other jobs produce image files. In Jupyter, these can be viewed by navigating to the run directory in the file browser
and opening the file(s) of interest.
To view all images on a single page, open and run the view notebood (view.ipynb) in that directory.

## Notes

Aogust 2, 2022: The above are confirmed to work with dunesw v09_56_00d00.

Run summary for 2022 is [here](https://docs.google.com/spreadsheets/d/1wh8qZTL6iZGQxaHKIjkvGPs81b2g6ReBHMYqCpT59yo/edit#gid=1659477561).
