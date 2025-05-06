.. _resource specification:

Specifying job resources
========================

Resources are specified using the ``-l`` option.  Typically, three resources will
be specified:

- :ref:`walltime <walltime>`
- :ref:`number of nodes and cores <nodes and ppn>`
- :ref:`memory <mem>`
- :ref:`number of GPUs <gpus>` (if applicable)

Additional specifications may be required to :ref:`accommodate hardware
details <specifying node properties>`.


.. _walltime:

Walltime
--------

Walltime is specified through the option ``-l walltime=HH:MM:SS`` with
``HH:MM:SS`` the walltime that you expect to need for the job. (The
format ``DD:HH:MM:SS`` can also be used when the walltime exceeds 1 day,
and ``MM:SS`` or simply ``SS`` are also viable options for very short
jobs).

To specify a run time of 30 hours, 25 minutes and 5 seconds, you'd use the
following ``qsub`` command::

   $ qsub -l walltime=30:25:05 myjob.pbs

Equivalently, you can specify the following PBS directive in your job script::

   #PBS -l walltime=30:25:05

If you omit this option, the resource manager use a default value (one hour
on most clusters).

Walltime estimation
~~~~~~~~~~~~~~~~~~~

.. warning::

   It is important that you try to estimate the walltime for your job properly.

If your job exceeds the specified walltime, it will be killed, so the walltime
should not be underestimated.

On the other hand, the walltime should not be wildly overestimated either since this
has several disadvantages.

- It will make your job harder to schedule, so it will spend more time in the queue.
- Short jobs may benefit from back fill, i.e., if the scheduler finds a gap
  (based on the estimated end time of the running jobs) that is long enough to run
  that job before it has enough resources to start a large higher-priority parallel job.
- To make sure that the cluster cannot be monopolized by one or
  a few users, many of our clusters have a stricter limit on the number of
  long-running jobs than on the number of jobs with a shorter walltime.

The maximum allowed walltime for a job can be different from cluster to
cluster, as it depends on their technical characteristics and policies.
You can find the maximum walltime of each cluster on their pages in the
:ref:`tier1 hardware` or :ref:`tier2 hardware` sections.

.. _nodes and ppn:

Number of nodes and cores
-------------------------

The following options can be used to specify the number of nodes and cores
needed for the job::

   -l nodes=<nodenum>:ppn=<cores per node>
   
This indicates that the job needs ``<nodenum>`` nodes with ``<cores per node>``
cores per node. Depending on the settings for the particular system, this will
be physical cores or hyperthreads on a physical core.

.. note::

   The job script will only run once on the first node of
   your allocation. To start processes on the other nodes, you'll need to
   use tools like ``pbsdsh`` or ``mpirun``/``mpiexec``.




.. _mem:

Memory
------

The following option specifies the RAM requirements of your job::

   -l mem=<memory>

The job needs ``<memory>`` RAM memory per node.  The units for the ``mem`` value are ``kb``, ``mb``, ``gb`` or
``tb``.

Example::

   -l nodes=2:ppn=8  -l mem=80gb

Each of the 16 processes can use 10 GB RAM for a total of 160 GB.

.. _vmem:

Virtual memory
~~~~~~~~~~~~~~

A second option is to specify virtual memory::

   -l vmem=<memory>

The job needs ``<memory>`` virtual memory per node. This determines the total amount of RAM memory + swap space that
can be used on any node.

.. note::

   On many clusters, there is not much swap space available.
   Moreover, swapping should be avoided as it causes a dramatic
   performance loss. Hence this option is not very useful in most cases.

Combining resource specifications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It is possible to combine multiple ``-l`` options in a single one by
separating the arguments with a colon (,). E.g., the block::

   #PBS -l walltime=2:30:00
   #PBS -l nodes=2:ppn=16
   #PBS -l mem=20gb

is equivalent with the line::

   #PBS -l walltime=2:30:00,nodes=2:ppn=16,mem=20gb

The same holds when using ``-l`` on the command line for ``qsub``.


.. _gpus_torque:

.. include:: gpus.rst

