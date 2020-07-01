WOS Storage Quick Start Guide
=============================

Overview of the storage infrastructure
--------------------------------------

Storage is an important part of a cluster. But not all the storage has
the same characteristics. HPC cluster storage at KU Leuven consists of 3
different storage Tiers, optimized for different usage

-  NAS storage, fully back-up with snapshots for /home and /data
-  Scratch storage, fast parallel filesystem
-  Archive storage, to store large amounts of data for long time

The picture below gives a quick overview of the different
components.

|WOS schematics|


Storage Types
-------------

As described on the :ref:`VSC storage guidelines <data location>`,
different types of data can be stored in different places. There is also
an extra storage space for Archive use.

Archive Storage
---------------

The archive tier is built with DDN WOS storage. It is intended to store
data for longer term. The storage is optimized for capacity, not for
speed. The storage by default is mirrored.

No deletion rules are executed on this storage. The data will be kept
until the user deletes it.

**Use for**: Storing data that will not be used for a longer period and
which should be kept. Compute nodes have no direct access to that
storage area and therefore it should not be used for jobs I/O
operations.

**How to request**: Please send a request from the `storage request
webpage <https://admin.kuleuven.be/icts/onderzoek/hpc/hpc-storage>`_.

| **How much does it cost**: For all the prices please refer to our
  `service catalog (login required) <https://icts.kuleuven.be/sc/english/HPC>`_.

Working with archive storage
----------------------------

The archive storage should not be used to perform I/O in a compute job.
Data should first be copied to the faster scratch filesystem. To
accommodate user groups that have a large archive space, a staging area
is foreseen. The staging area is a part of the same hardware platform as
the fast scratch filesystem, but other rules apply. Data is not deleted
automatically after 21 days. When the staging area is full it will be
the user’s responsibility to make sure that enough space is available.
Data created on scratch or in the staging location which needs to be
kept for longer time should be copied to the archive.

Location of Archive/Staging
---------------------------

The name of user's archive directory is in the format:
``/archive/leuven/arc_XXXXX``, where ``XXXXX`` is a number and this will be
given to the user by HPC admin once your archive requested is handled.

The name of your staging directory is in this format:
``/staging/leuven/stg_XXXXX``, where ``XXXXX`` is the same number as for the
archive directory.

Use case: Data is in archive, how can I use it in a compute job?
----------------------------------------------------------------

In this use case you want to start to compute on older data in your
archive.

If you want to compute with data in your archive stored in
‘archive_folder’. You can copy this data to your scratch using the
following command:

::

   rsync -a <PATH_to_archive/archive_folder> <PATH_to_scratch>

Afterwards you may want to archive the new produced results back to
archive therefore you should follow the steps in the following use case.

Use case: Data produced on cluster, stored for longer time?
-----------------------------------------------------------

This procedure applies to the case when you have jobs producing output
results on the scratch area and you want to archive those results in
your archive area.

In that case you have a folder on scratch called ‘archive_folder’ in
which you are working. And the same folder already exists in your
archive space. Now you want to update your archive space with the new
results produced on scratch

You could run the command:

::

   rsync -i -u -r --dry-run <PATH_to_scratch/archive_folder> <PATH_to_archive/archive_folder>

This command will not perform the copy yet but it will give an overview
of all data changed since last copy from archive. Therefore not all data
needs to be copied back. If you agree with this overview you can run
this command without the --dry-run’ option. If you are synching a large
amount files, please contact HPC support for follow-up.

Use case : How to get local data on archive?
--------------------------------------------

Data that is stored at the user's local facilities can be copied to the
archive through scp/bbcp/sftp methods. For this please refer to the
appropriate VSC documentation:

-  for Linux: :ref:`openssh <scp and sftp>`

-  for windows: :ref:`FileZilla <FileZilla>` or :ref:`winscp <WinScp>`

-  for macOS: :ref:`Cyberduck data transfer` or :ref:`scp and sftp <scp and sftp>`

Use case : How to check the disk usage?
---------------------------------------

To check the occupied disk space additional option is necessary with du
command:

::

   du --apparent-size folder-name


.. On our clusters, Torque is currently no longer configured with support
.. for data staging. If this gets (re-)enabled in the future, the paragraph
.. below can be included again.
.. comments:

    How to stage in or stage out using torque?
    ------------------------------------------

    Torque gives also the possibility to specify data staging as a job
    requirement. This way Torque will copy your data to scratch while your
    job is in the queue and will not start the job before all data is
    copied. The same mechanism is possible for stageout requirements. In the
    example below Torque will copy back your data from scratch when your job
    is finished to the archive storage tier:

    ::

       qsub -W stagein=/scratch/leuven/3XX/vsc3XXXX@login1:/archive/leuven/arc_000XX/foldertostagein
       -W stageout=/scratch/leuven/3XX/vsc3XXXX/foldertostageout@login1:/archive/leuven/arc_000XX/

    Hostname is always one of the login nodes, because these are the only
    nodes where ‘archive’ is available on the cluster.

    For stagein the copy goes from ``/archive/leuven/arc_000XX/foldertostagein``
    to ``/scratch/leuven/3XX/vsc3XXXX``

    For stageout the copy goes from
    ``/scratch/leuven/3XX/vsc3XXXX/foldertostageout`` to
    ``/archive/leuven/arc_000XX/``

Attached documents
------------------

-  :download:`WOS storage quick start guide <wos_storage_quick_start_guide/wos_quickstart.pdf>`
-  download:`slides from storage info-session <wos_storage_quick_start_guide/wos_slides.pdf>`

.. |WOS schematics| image:: wos_storage_quick_start_guide/wos_schematics.png
  :width: 700
  :alt: WOS schematics
