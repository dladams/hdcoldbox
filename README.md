# hdcoldbox

Sofware to analyzing data from the 2021 horizontal-drift cold box test.

## Installation

First install [*dunerun*](https://github.com/dladams/dunerun) specifying the dunesw release.
Here we denote the base installation directory \<install-dir> and assume package-specific installation.
Set up *dunerun* and the clone and use it to install *duneproc* and *dunehdcoldbox* as follows:
<pre>
cd &lt;pkgdir>
source &lt;install-dir>/dunerun/setup.sh
git clone https://github.com/dladams/duneproc.git
duneproc/build
git clone https://github.com/dladams/hdcoldbox.git
hdcoldbox/build
</pre>
Here \<pkgdir> is a source installation dir which can be removed after installation and the set up defines the env needed for the build command.

## Available commands

* Generate displays and metric vs. channel plots for one event:
<pre>
./doOneEvent hdproc 12242 1
</pre>

Note: The above instructions assume you are using duneproc (https://github.com/dladams/duneproc).
If not (and no one is saying you should), you will have to compose your own lar command lines and
the first argument in the above commands is the base of the fcl file (i.e. hdproc means hdproc.fcl)
you can take from this package.
