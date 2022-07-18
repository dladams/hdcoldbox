# hdcoldbox
David Adams  
July 18, 2022

Software to analyzing data from the 2021 and 2022 horizontal-drift cold box tests.

## Installation

First install *dunerun*, set it up and install *duneproc* following the instructions in the
[*duneproc* README](https://github.com/dladams/duneproc/blob/master/README.md).
Then clone this package in the aread where you want run and setup to use duneproc as follows:

<pre>
cd &lt;some-dir>
git clone https://github.com/dladams/hdcoldbox.git
cd hdcoldbox
./start-shell
</pre>

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

The *duneproc* help tells what commands are available for HD codbox data:
<pre>
duneproc> duneprocHelp hdcb

2021 CERN HD coldbox commands:
  hdcbFindFiles - Find event data files for the run

duneproc> 
</pre>

The electronics and channel maps have changed between coldbox runs and the readout configuration must change accordingly.
Issue [#1](../../issues/1) proposes to automate this but, until that is implemented, the top-level config files are appropriate for onely
one run period and users may need to adjust these to look at data from other periods.
See comments in the top-level config for help with that adjustment.
Right now (July 18, 2022), the configs support the current test of APA2.
The following example is for an older run and won't work out of the box.
Substitute a current run number to use the SW as it stands.

List the files for a run:
<pre>
duneproc> hdcbFindFiles -h
Usage: /home/dladams/proc/install/v09_46_00_00/duneproc/bin/hdcbFindFiles RUNPAT DIR
  RUNPAT is run number or RUN_FFFF where FFFF is the file index.
  DIR is the directory to write the file list.
  If DIR="-", /home/dladams/data/dune/datasets/hdcb is used.
duneproc> hdcbFindFiles 14201
np04_coldbox_run014201_0000_dataflow0_20220713T083046.hdf5
np04_coldbox_run014201_0001_dataflow0_20220713T084916.hdf5
np04_coldbox_run014201_0002_dataflow0_20220713T090746.hdf5
np04_coldbox_run014201_0003_dataflow0_20220713T092616.hdf5
np04_coldbox_run014201_0004_dataflow0_20220713T094446.hdf5
np04_coldbox_run014201_0005_dataflow0_20220713T100316.hdf5
np04_coldbox_run014201_0006_dataflow0_20220713T102146.hdf5
np04_coldbox_run014201_0007_dataflow0_20220713T104016.hdf5
np04_coldbox_run014201_0008_dataflow0_20220713T105846.hdf5
np04_coldbox_run014201_0009_dataflow0_20220713T111716.hdf5
np04_coldbox_run014201_0010_dataflow0_20220713T113546.hdf5
np04_coldbox_run014201_0011_dataflow0_20220713T115416.hdf5
np04_coldbox_run014201_0012_dataflow0_20220713T121246.hdf5
duneproc> 
</pre>

The script *doOneEvent* may be used to process a single event.
The first argment is the base name of the top-level config file.
To make raw data noise plots for furn 14201 event 1:
<pre>
duneproc> ./doOneEvent hdproc 14201 1
np04_coldbox_run014201_0000_dataflow0_20220713T083046.hdf5
np04_coldbox_run014201_0001_dataflow0_20220713T084916.hdf5
np04_coldbox_run014201_0002_dataflow0_20220713T090746.hdf5
np04_coldbox_run014201_0003_dataflow0_20220713T092616.hdf5
np04_coldbox_run014201_0004_dataflow0_20220713T094446.hdf5
np04_coldbox_run014201_0005_dataflow0_20220713T100316.hdf5
np04_coldbox_run014201_0006_dataflow0_20220713T102146.hdf5
np04_coldbox_run014201_0007_dataflow0_20220713T104016.hdf5
np04_coldbox_run014201_0008_dataflow0_20220713T105846.hdf5
np04_coldbox_run014201_0009_dataflow0_20220713T111716.hdf5
np04_coldbox_run014201_0010_dataflow0_20220713T113546.hdf5
np04_coldbox_run014201_0011_dataflow0_20220713T115416.hdf5
np04_coldbox_run014201_0012_dataflow0_20220713T121246.hdf5
Dataset written to /home/dladams/data/dune/datasets/hdcb/hdcb014201.txt
duneproc hdproc hdcb014201/event001 1 0
FOUND input file list: /home/dladams/data/dune/datasets/hdcb/hdcb014201.txt
RUNDIR = hdproc/hdcb014201/event001
Run directory: hdproc/hdcb014201/event001
Taking input files from /home/dladams/data/dune/datasets/hdcb/hdcb014201.txt
.
.
.
Run directory: hdproc/hdcb014201/event001
ARGS: -c run.fcl -s --nskip 0
Command: lar -c run.fcl -S infiles.txt --nskip 0 --no-output
Creating output directory /home/dladams/xfer/2022/0718/hdproc/run014201/event001
Output directory: /home/dladams/xfer/2022/0718/hdproc/run014201/event001
total 11537
1120 -rw-r--r--. 1 dladams fnalgrid 1146790 Jul 18 14:52 adcprp_tpp0c_run014201_evt000001.png
1743 -rw-r--r--. 1 dladams fnalgrid 1784218 Jul 18 14:52 adcprp_tpp0u_run014201_evt000001.png
1727 -rw-r--r--. 1 dladams fnalgrid 1768130 Jul 18 14:52 adcprp_tpp0v_run014201_evt000001.png
1126 -rw-r--r--. 1 dladams fnalgrid 1152929 Jul 18 14:52 adcprp_tpp0z_run014201_evt000001.png
1125 -rw-r--r--. 1 dladams fnalgrid 1151588 Jul 18 14:52 adcraw_tpp0c_run014201_evt000001.png
1747 -rw-r--r--. 1 dladams fnalgrid 1788799 Jul 18 14:52 adcraw_tpp0u_run014201_evt000001.png
1730 -rw-r--r--. 1 dladams fnalgrid 1771477 Jul 18 14:52 adcraw_tpp0v_run014201_evt000001.png
1131 -rw-r--r--. 1 dladams fnalgrid 1157738 Jul 18 14:52 adcraw_tpp0z_run014201_evt000001.png
  13 -rw-r--r--. 1 dladams fnalgrid   12387 Jul 18 14:52 chmet_pednoise_tps0_run014201_evt000001.png
  28 -rw-r--r--. 1 dladams fnalgrid   28493 Jul 18 14:52 chmet_pedrawrms_tps0_run014201_evt000001.png
  20 -rw-r--r--. 1 dladams fnalgrid   19694 Jul 18 14:52 chmet_ped_tps0_run014201_evt000001.png
  29 -rw-r--r--. 1 dladams fnalgrid   29090 Jul 18 14:52 chmet_samrms_tps0_run014201_evt000001.png
</pre>
Note the script copies the gnerated png files to a subdirectory of $HOME/xfer/YYYY where YYYY is the current year
if that directory exists.
The adcraw* plots are the noise vs channel vs tick event displays.
The adcprp apply a calibration that is not yet correctly set and should be ignored.
TRhe chmet pedrawrms has raw RMS vs channel.

Replace hdproc with hdproc-cnr to generate noise plots with CNR (correlated noise removal).
Now the adcprp plots have CNR noise vs channel-tick and samrms has noise vs channel.
Note, that the plots are generated with a gain of one and the noise units are realls ADC counts
rathr than the indicated ke/tick.
