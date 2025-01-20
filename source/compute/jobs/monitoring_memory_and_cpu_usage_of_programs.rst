.. _monitoring memory and cpu:

Monitoring memory and CPU usage of programs
===========================================

Purpose
-------

Estimating the amount of memory an application will use during execution
is often non trivial, especially when one uses third-party software.
However, this information is valuable, since it helps to determine the
characteristics of the compute nodes a job using this application should
run on.

Although the tool presented here can also be used to support the
software development process, better tools are almost certainly
available.

Note that currently only single node jobs are supported, MPI support may
be added in a future release.

Prerequisites
-------------

The user should be familiar with the Linux bash shell.

Monitoring a program
--------------------

To start using monitor, first load the appropriate module:

::

   $ module load monitor

Starting a program, e.g., simulation, to monitor is very straightforward

::

   $ monitor simulation

monitor will write the CPU usage and memory consumption of simulation to
standard error. Values will be displayed every 5 seconds. This is the
rate at which monitor samples the program's metrics.

Log file
~~~~~~~~

Since monitor's output may interfere with that of the program to
monitor, it is often convenient to use a log file. The latter can be
specified as follows:

::

   $ monitor -l simulation.log simulation

For long running programs, it may be convenient to limit the output to,
e.g., the last minute of the programs execution. Since monitor provides
metrics every 5 seconds, this implies we want to limit the output to the
last 12 values to cover a minute:

::

   $ monitor -l simulation.log -n 12 simulation

Note that this option is only available when monitor writes its metrics
to a log file, not when standard error is used.

Modifying the sample resolution
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The interval at which monitor will show the metrics can be modified by
specifying delta, the sample rate:

::

   $ monitor -d 60 simulation

monitor will now print the program's metrics every 60 seconds. Note that
the minimum delta value is 1 second.

File sizes
~~~~~~~~~~

Some programs use temporary files, the size of which may also be a
useful metric. monitor provides an option to display the size of one or
more files:

::

   $ monitor -f tmp/simulation.tmp,cache simulation

Here, the size of the file simulation.tmp in directory tmp, as well as
the size of the file cache will be monitored. Files can be specified by
absolute as well as relative path, and multiple files are separated by
','.

Programs with command line options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Many programs, e.g., MATLAB, take command line options. To make sure
these do not interfere with those of monitor and vice versa, the program
can for instance be started in the following way:

::

   $ monitor -delta 60 -- matlab -nojvm -nodisplay computation.m

The use of ``--`` will ensure that monitor does not get confused by
MATLAB's ``-nojvm`` and ``-nodisplay`` options.

Subprocesses and multicore programs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some processes spawn one or more subprocesses. In that case, the metrics
shown by monitor are aggregated over the process and all of its
subprocesses (recursively). The reported CPU usage is the sum of all
these processes, and can thus exceed 100 %.

Some (well, since this is a HPC cluster, we hope most) programs use more
than one core to perform their computations. Hence, it should not come
as a surprise that the CPU usage is reported as larger than 100 %.

When programs of this type are running on a computer with *n* cores, the
CPU usage can go up to *n* x 100 %.

Exit codes
~~~~~~~~~~

monitor will propagate the exit code of the program it is watching.
Suppose the latter ends normally, then monitor's exit code will be 0. On
the other hand, when the program terminates abnormally with a non-zero
exit code, e.g., 3, then this will be monitor's exit code as well.

When monitor has to terminate in an abnormal state, for instance if it
can't create the log file, its exit code will be 65. If this interferes
with an exit code of the program to be monitored, it can be modified by
setting the environment variable MONITOR_EXIT_ERROR to a more suitable
value.

Monitoring a running process
----------------------------

It is also possible to \\"attach\" monitor to a program or process that
is already running. One simply determines the relevant process ID using
the ps command, e.g., 18749, and starts monitor:

::

   $ monitor -p 18749

Note that this feature can be (ab)used to monitor specific subprocesses.

More information
----------------

Help is available for monitor by issuing:

::

   $ monitor -h
