# hdcoldbox
David Adams  
April 2022

Software to analyzing data from the 2021 horizontal-drift cold box test.

## Installation

First install *dunerun* and *duneproc* following the instructions in the
[*duneproc* README](https://github.com/dladams/duneproc/blob/master/README.md)
and then clone this package in the aread where you want run, e.g.

<pre>
cd &lt;some-dir>
git clone https://github.com/dladams/hdcoldbox.git
cd hdcoldbox
</pre>
Here \<pkgdir> is a source installation dir which can be removed after installation and the set up defines the env needed for the build command.

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

List the files for a run:
<pre>
duneproc> hdcbFindFiles -h
Usage: /home/dladams/proc/install/v09_46_00_00/duneproc/bin/hdcbFindFiles RUNPAT DIR
  RUNPAT is run number or RUN_FFFF where FFFF is the file index.
  DIR is the directory to write the file list.
  If DIR="-", /home/dladams/data/dune/datasets/hdcb is used.
duneproc> hdcbFindFiles 12242
np04_coldbox_run012242_0000_20211208T153442.hdf5
np04_coldbox_run012242_0001_20211208T153634.hdf5
np04_coldbox_run012242_0002_20211208T153826.hdf5
np04_coldbox_run012242_0003_20211208T154018.hdf5
np04_coldbox_run012242_0004_20211208T154210.hdf5
np04_coldbox_run012242_0005_20211208T154402.hdf5
np04_coldbox_run012242_0006_20211208T154554.hdf5
np04_coldbox_run012242_0007_20211208T154746.hdf5
duneproc> 
</pre>

Having found files for the run, we define a dataset that includes these files adding a second argument that writes this definition to a standard location:
<pre>
duneproc> hdcbFindFiles 12242 -
Output directory not found: /home/dladams/data/dune/datasets/hdcb
duneproc> mkdir -p /home/dladams/data/dune/datasets/hdcb
duneproc> hdcbFindFiles 12242 -
np04_coldbox_run012242_0000_20211208T153442.hdf5
np04_coldbox_run012242_0001_20211208T153634.hdf5
np04_coldbox_run012242_0002_20211208T153826.hdf5
np04_coldbox_run012242_0003_20211208T154018.hdf5
np04_coldbox_run012242_0004_20211208T154210.hdf5
np04_coldbox_run012242_0005_20211208T154402.hdf5
np04_coldbox_run012242_0006_20211208T154554.hdf5
np04_coldbox_run012242_0007_20211208T154746.hdf5
Dataset written to /home/dladams/data/dune/datasets/hdcb/hdcb012242.txt
duneproc> 
</pre>

Generate displays and metric vs. channel plots for one event using hdproc.fcl from the *hdcoldbox* package:
<pre>
duneproc> duneproc hdproc hdcb012242/event0001 noxrootd
FOUND input file list: /nashome/d/dladams/data/dune/datasets/hdcb/hdcb012242.txt
RUNDIR = hdproc/hdcb012242/event0001
Run directory: hdproc/hdcb012242/event0001
Taking input files from /nashome/d/dladams/data/dune/datasets/hdcb/hdcb012242.txt
Copying hdproc.fcl from submission directory.
makeFcl: Created event0001.fcl
Not found: /nashome/d/dladams/proc/v09_46_00_00/hdcoldbox/local.fcl
Not found: /nashome/d/dladams/proc/v09_46_00_00/hdcoldbox/dbg.fcl
.
.
.
</pre>
Note the noxrootd option is required because this data is in hdf5 format.
This means we read from pnfs which implies we cannot access the data from FNAL jupyter server so no notebooks :(.

There is also helper script to run this command:

<pre>
./doOneEvent hdproc 12242 1
</pre>

Note: The above instructions assume you are using duneproc (https://github.com/dladams/duneproc).
If not (and no one is saying you should), you will have to compose your own lar command lines and
the first argument in the above commands is the base of the fcl file (i.e. hdproc means hdproc.fcl)
you can take from this package.
