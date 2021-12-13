# hdcoldbox

Sofware to analyzing data from the 2021 horizontal-drift cold box test.

Available commands:

* Generate dsiplays and metric vs. channel plots for one event:
<pre>
./doOneEvent hdproc 12242 1
</pre>

Note: The above instructions assume you are using duneproc (https://github.com/dladams/duneproc).
If not (and no one is saying you should), you will have to compose your own lar command lines and
the first argument in the above commands is the base of the fcl file (i.e. hdproc means hdproc.fcl)
you can take from this package.
