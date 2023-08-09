=========
iCommands
=========


iCommands is a command-line interface for iRODS, the open-source
software behind Tier-1 Data. For those who are familiar with Unix command-line
programs, it is a powerful and easy to use tool.

Installation
============

iCommands are already installed on the following HPC clusters:

- Genius
- wICE
- Hydra

You can of course also install iCommands on your local system. 
However, iCommands is only available for Linux environments. 
To get one, Windows users might consider installing Windows Subsystem for Linux (WSL).

You can install iCommands on different distributions as follows:

Centos 7:
---------

.. code:: sh

   # Installing prerequisites
   yum update
   yum install wget sudo

   # Add the iRODS repository to your package manager (if you haven't done so already)
   sudo rpm --import https://packages.irods.org/irods-signing-key.asc
   wget -qO - https://packages.irods.org/renci-irods.yum.repo | sudo tee /etc/yum.repos.d/renci-irods.yum.repo

   # Installing iCommands  
   yum install irods-icommands

Almalinux 8/Rocky Linux 8:
--------------------------

.. code:: sh

   # Installing prerequisites
   yum update 
   yum install wget sudo

   # Add the iRODS repository to your package manager (if you haven't done so already)
   sudo rpm --import https://packages.irods.org/irods-signing-key.asc
   wget -qO - https://packages.irods.org/renci-irods.yum.repo | sudo tee /etc/yum.repos.d/renci-irods.yum.repo

   # irods runtime needs to be installed manually because of https://github.com/k3s-io/k3s/issues/5588
   yum install irods-runtime 

   # Installing iCommands  
   yum install irods-icommands

Debian 11:
----------

.. code:: sh

   # Installing prerequisites
   apt-get update
   apt-get install wget lsb-release sudo gnupg

   # Add the iRODS repository to your package manager (if you haven't done so already)
   wget -qO - https://packages.irods.org/irods-signing-key.asc | sudo apt-key add -
   echo "deb [arch=amd64] https://packages.irods.org/apt/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/renci-irods.list
   sudo apt-get update

   # Installing iCommands  
   apt-get install irods-icommands

Ubuntu 18/20:
-------------

.. code:: sh

   # Installing prerequisites
   apt-get update
   apt-get install wget lsb-core sudo

   # Add the iRODS repository to your package manager (if you haven't done so already)
   wget -qO - https://packages.irods.org/irods-signing-key.asc | sudo apt-key add -
   echo "deb [arch=amd64] https://packages.irods.org/apt/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/renci-irods.list
   sudo apt-get update

   # Installing iCommands 
   apt-get install irods-icommands

Ubuntu 22:
----------

.. code:: sh

   # Installing prerequisites
   apt-get update
   apt-get install gnupg wget sudo
   wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2_amd64.deb
   sudo dpkg -i libssl1.1_1.1.1f-1ubuntu2_amd64.deb

   # Add the iRODS repository to your package manager (if you haven't done so already)
   wget -qO - https://packages.irods.org/irods-signing-key.asc | sudo apt-key add -
   echo "deb [arch=amd64] https://packages.irods.org/apt/ focal main" | sudo tee /etc/apt/sources.list.d/renci-irods.list
   sudo apt-get update

   # Installing iCommands 
   apt-get install irods-icommands

Authenticating
==============

To authenticate, go to the `ManGO portal <https://mango.vscentrum.be>`__
and log in. Click on ‘How to connect’ next to your zone, copy the code
under ‘iCommands for Linux’ and paste it into your terminal. This should
authenticate your for 168 hours.

Getting help
============

iCommands has a built-in documentation, which you can access from the
command line. The command ``ihelp`` gives an overview of all commands,
with a brief description.

To get the documentation for a specific command, you can either type
``ihelp <command>`` or ``command -h``.

Similarities with UNIX commands
===============================

To people who are used to working on a Linux command line, iCommands
will instantly feel familiar. Many unix commands have a direct Unix
counterpart. While the Unix commands work on the local workspace, the
iCommands work on the data in Tier-1 Data.

+-------------------------------+-----------------------+-------------+
| Unix command                  | iCommand              | use         |
+===============================+=======================+=============+
| cd                            | icd                   | change      |
|                               |                       | current     |
|                               |                       | working     |
|                               |                       | directory   |
|                               |                       | /collection |
+-------------------------------+-----------------------+-------------+
| pwd                           | ipwd                  | show the    |
|                               |                       | current     |
|                               |                       | working     |
|                               |                       | directory   |
|                               |                       | /collection |
+-------------------------------+-----------------------+-------------+
| ls                            | ils                   | list the    |
|                               |                       | current     |
|                               |                       | working     |
|                               |                       | directory   |
|                               |                       | /collection |
+-------------------------------+-----------------------+-------------+
| mkdir                         | imkdir                | create      |
|                               |                       | directory   |
|                               |                       | /collection |
+-------------------------------+-----------------------+-------------+
| cp                            | icp                   | copy a      |
|                               |                       | file/data   |
|                               |                       | object or   |
|                               |                       | collectio   |
|                               |                       | n/directory |
+-------------------------------+-----------------------+-------------+
| mv                            | imv                   | move a      |
|                               |                       | file/data   |
|                               |                       | object or   |
|                               |                       | collectio   |
|                               |                       | n/directory |
+-------------------------------+-----------------------+-------------+
| chmod                         | ichmod                | changing    |
|                               |                       | permissions |
+-------------------------------+-----------------------+-------------+
| …                             | …                     | …           |
+-------------------------------+-----------------------+-------------+

Just like Unix commands, iCommands work with both absolute and relative
paths. For example, to go from ``/<zone>/home/<project>`` to
``/<zone>/home/<project>/raw_data`` you can use both of the following
options:

.. code:: sh

   icd raw_data

   icd /<zone>/home/<project>/raw_data

Like with Unix commands, you can use ``.`` to refer to the current
working collection, and ``..`` to refer to one level above the current
collection.

An important difference is that iCommands have no shell expansion. If
you try to use autocompletion with iCommands, or use wildcards (*), it
will be filled in based on the data in your local directory. This can
yield unexpected results.

Uploading and downloading data
==============================

To upload data from your local directory to Tier-1 Data, you can use the
command ``iput``. You can upload individual files but also whole
directories, by using the ``-r`` option, which stands for ‘recursive’.

.. code:: sh

   iput my_file.txt 
   iput -r  my_directory

You can optionally specify a destination as second argument. If you
leave the destination blank, iput will take the current working
collection as destination by default.

To download data objects or whole collections from Tier-1 Data to your local
directory, you can use the command ``iget``:

.. code:: sh

   iget my_data_object.txt
   iget -r my_directory

``iget`` downloads data to your current working directory, unless you
specify another destination as second argument.

It is also possible with iCommands to sync a local directory and a
collection in Tier-1 Data with the command ``irsync``. This command makes a
comparison between the data on both sides. Any data from the source
which is missing in the destination, is transferred. If files are
present in both the source and destination, ``irsync`` will calculate
checksums to see whether the version in the destination is still up to
date.

.. code:: sh

   # syncronizing data from a local directory to a Tier-1 Data collection
   irsync -r local_directory i:collection

   # syncronizing data from a Tier-1 Data collection to a local directory
   irsync -r i:collection local_director
