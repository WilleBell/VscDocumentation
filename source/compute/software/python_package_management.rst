.. _Python packages:

Python package management
=========================

Introduction
------------

Python comes with an extensive standard library, and you are strongly
encouraged to use those packages as much as possible, since this will
ensure that your code can be run on any platform that supports Python.

However, many useful extensions to and libraries for Python come in the
form of packages that can be installed separately. Some of those are part
of the default installation on VSC infrastructure, others have been made
available through the module system and must be loaded explicitly.

Given the astounding number of packages, it is not sustainable to
install each and everyone system wide. Since it is very easy for a user
to install them just for himself, or for his research group, that is not
a problem though. Do not hesitate to contact support whenever you
encounter trouble doing so.

Checking for installed packages
-------------------------------

To check which Python packages are installed, the ``pip`` utility is
useful. It will list all packages that are installed for the Python
distribution you are using, including those installed by you, i.e.,
those in your ``PYTHONPATH`` environment variable.

#. Load the module for the Python version you wish to use, e.g.,::

      $ module load Python/3.7.0-foss-2018b

#. Run ``pip``::
   
      $ pip freeze

Note that some packages, e.g., ``mpi4py``, ``pyh5``, ``pytables``,...,
are available through the module system, and have to be loaded
separately. These packages will not be listed by ``pip`` unless you
loaded the corresponding module.  In recent toolchains, many of the
packages you need for scientific computing have been bundled into the
``SciPy-bundle`` module.


.. _conda for Python:

Install Python packages using conda
-----------------------------------

.. note::

    Conda packages are incompatible with the software modules.
    Usage of conda is discouraged in the clusters at UAntwerpen, UGent,
    and VUB.

The easiest way to install and manage your own Python environment is
conda.  Using conda has some major advantages.

-  You can create project-specific environments that can be shared with
   others and (up to a point) across platforms.  This makes it easier to
   ensure that your experiments are reproducible.
-  conda takes care of the dependencies, up to the level of system libraries.
   This makes it very easy to install packages.

However, this last advantage is also a potential drawback: you have to
review the libraries that conda installs because they may not have
been optimized for the hardware you are using.  For linear algebra, conda
will typically use Intel MKL runtime libraries, giving you performance that
is on par with the Python modules for `numpy` and `scipy`.

However, care has to be taken in a number of situations.  When you require
``mpi4py``, conda will typically use a library that is not configured and
optimized for the networks used in our clusters, and the performance impact
is quite severe.  Another example is TensorFlow when running on CPUs, the
default package is not optimized for the CPUs in our infrastructure, and will
run sub-optimally.  (Note that this is not the case when you run TensorFlow on
GPUs, since conda will install the appropriate CUDA libraries.)

These issues can be avoided by using Intel's Python distribution that contains
Intel MPI and optimized versions of packages such as scikit-learn and TensorFlow.
You will find `installation instructions <https://software.intel.com/content/www/us/en/develop/articles/using-intel-distribution-for-python-with-anaconda.html>`_
provided by Intel.

.. _install_miniconda_python:

Install Miniconda
~~~~~~~~~~~~~~~~~

If you have Miniconda already installed, you can skip ahead to the next
section, if Miniconda is not installed, we start with that. Download the
Bash script that will install it from `conda.io <https://conda.io/>`_
using, e.g., ``wget``::

   $ wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh

Once downloaded, run the installation script::

   $ bash Miniconda3-latest-Linux-x86_64.sh -b -p $VSC_DATA/miniconda3

.. warning::

   It is important to use ``$VSC_DATA`` to store your conda installation
   since environments tend to be large, and your quota in ``$VSC_HOME``
   would be exceeded soon.

Optionally, you can add the path to the Miniconda installation to the
``PATH`` environment variable in your ``.bashrc`` file. This is convenient, but
may lead to conflicts when working with the module system, so make sure
that you know what you are doing in either case. The line to add to your
``.bashrc`` file would be::

   export PATH="${VSC_DATA}/miniconda3/bin:${PATH}"

.. _create_python_conda_env:

Create an environment
~~~~~~~~~~~~~~~~~~~~~

First, ensure that the Miniconda installation is in your PATH
environment variable. The following command should return the full path
to the conda command::

   $ which conda

If the result is blank, or reports that conda can not be found, modify
the ``PATH`` environment variable appropriately by adding Miniconda's ``bin``
directory to ``PATH``.

You can create an environment based on the default conda channels, but
it is recommended to at least consider the Intel Python distribution.

Intel provides instructions on `how to install the Intel Python distribution
<https://software.intel.com/content/www/us/en/develop/articles/using-intel-distribution-for-python-with-anaconda.html>`_.

Alternatively, to creating a new conda environment based on the default channels:

   $ conda create  -n science  numpy scipy matplotlib

This command creates a new conda environment called science, and
installs a number of Python packages that you will probably want to have
handy in any case to preprocess, visualize, or postprocess your data.
You can of course install more, depending on your requirements and
personal taste.

This will default to the latest Python 3 version, if you need a specific
version, e.g., Python 2.7.x, this can be specified as follows::

   $ conda create -n science  python=2.7  numpy scipy matplotlib


Work with the environment
~~~~~~~~~~~~~~~~~~~~~~~~~

To work with an environment, you have to activate it. This is done with,
e.g.,

::

   $ source activate science

Here, ``science`` is the name of the environment you want to work in.


Install an additional package
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To install an additional package, e.g., \`pandas`, first ensure that the
environment you want to work in is activated.

::

   $ source activate science

Next, install the package::

   $ conda install tensorflow-gpu

Note that conda will take care of all dependencies, including
non-Python libraries (e.g., cuDNN and CUDA for the example above). This
ensures that you work in a consistent environment.


Update/remove a package
~~~~~~~~~~~~~~~~~~~~~~~

Using conda, it is easy to keep your packages up-to-date. Updating a
single package (and its dependencies) can be done using::

   $ conda update pandas

Updating all packages in the environment is trivial::

   $ conda update --all

Removing an installed package::

   $ conda remove tensorflow-gpu


Deactivate an environment
~~~~~~~~~~~~~~~~~~~~~~~~~

To deactivate a conda environment, i.e., return the shell to its
original state, use the following command::

   $ source deactivate


More information
~~~~~~~~~~~~~~~~

Additional information about conda can be found on its `documentation
site <https://docs.conda.io/en/latest/>`_.


Alternatives to conda
---------------------

Setting up your own package repository for Python is straightforward. 
`PyPi, the Python Package Index <https://pypi.org/>`_ is a web repository of
Python packages and you can easily install packages from it using either
``easy_install`` or ``pip``. In both cases, you'll have to create a 
subdirectory for Python in your ``${VSC_DATA}`` directory, add this directory
to your ``PYTHONPATH`` after loading a suitable Python module, and then 
point ``easy_install`` or ``pip`` to that directory as the install target
rather then the default (which of course is write-protected on a multi-user
system). Both commands will take care of dependencies also.


Installing packages using easy_install
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you prefer to use ``easy_install``, you can follow these instructions:

#. Load the appropriate Python module, i.e., the one you want the python
   package to be available for::
   
      $ module load Python/3.7.0-foss-2018b
   
#. Create a directory to hold the packages you install, the last three
   directory names are mandatory::
   
      $ mkdir -p "${VSC_DATA}/python_lib/lib/python3.7/site-packages/"
   
#. Add that directory to the ``PYTHONPATH`` environment variable for the
   current shell to do the installation::
   
      $ export PYTHONPATH="${VSC_DATA}/python_lib/lib/python3.7/site-packages/:${PYTHONPATH}"
   
#. Add the following to your ``.bashrc`` so that Python knows where to
   look next time you use it::
   
      export PYTHONPATH="${VSC_DATA}/python_lib/lib/python3.7/site-packages/:${PYTHONPATH}"
   
#. Install the package, using the ``--prefix`` option to specify the
   install path (this would install the sphinx package)::
   
   $ easy_install --prefix="${VSC_DATA}/python_lib" sphinx


Installing packages using  pip
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you prefer using ``pip``, you can perform an install in your own
directories as well by providing an install option.

#. Load the appropriate Python module, i.e., the one you want the python
   package to be available for::
   
      $ module load Python/3.7.0-foss-2018b
   
#. Create a directory to hold the packages you install, the last three
   directory names are mandatory::
   
      $ mkdir -p "${VSC_DATA}/python_lib/lib/python3.7/site-packages/"
   
#. Add that directory to the ``PYTHONPATH`` environment variable for the
   current shell to do the installation::
   
      $ export PYTHONPATH="${VSC_DATA}/python_lib/lib/python3.7/site-packages/:${PYTHONPATH}"
   
#. Add the following to your ``.bashrc`` so that Python knows where to
   look next time you use it::
   
      export PYTHONPATH="${VSC_DATA}/python_lib/lib/python3.7/site-packages/:${PYTHONPATH}"
   
#. Install the package, using the ``--prefix`` install option to specify
   the install path (this would install the sphinx package)::
   
      $ pip install --install-option="--prefix=${VSC_DATA}/python_lib" sphinx

   For newer version of ``pip``, you would use::

      $ pip install  --prefix="${VSC_DATA}/python_lib" sphinx


Installing Anaconda on NX node (KU Leuven Genius)
-------------------------------------------------

#. Before installing make sure that you do not have a ``.local/lib``
   directory in your ``$VSC_HOME``. In case it exists, please move it to
   some other location or temporary archive. It creates conflicts with
   Anaconda.
#. Download appropriate (64-Bit (x86) Linux Installer) version of Anaconda
   from `https://www.anaconda.com/products/individual#Downloads <https://www.anaconda.com/products/individual#Downloads>`_
#. Change the permissions of the file (if necessary)::

      $ chmod u+x Anaconda3-2019.07-Linux-x86_64.sh

#. Execute the installer::
  
      $ ./Anaconda3-2019.07-Linux-x86_64.sh 

   You will be asked for to accept the license agreement, choose the location where
   it should be installed (please choose your ``$VSC_DATA``). After installation is
   done you can choose to installer to add the Anaconda path to your ``.bashrc``.
   We recommend not to do that as it will prevent creating NX desktops. Instead of
   that you can manually (or in another script) modify your path when you want to
   use Anaconda::

      export PATH="${VSC_DATA}/anaconda3/bin:$PATH"

#. Go to the directory where Anaconda is installed and check for updates, e.g.,::

      $ cd anaconda3/bin/
      $ conda update anaconda-navigator

#. You can start the navigator from that directory with::

      $ ./anaconda-navigator
