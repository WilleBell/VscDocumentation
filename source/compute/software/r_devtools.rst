.. _r_devtools:

Installing R packages with devtools
===================================

Introduction
~~~~~~~~~~~~

The installation of some R packages may require the use of devtools.
Devtools is an R package that facilitates the installation of other
R packages from github, gitlab, bitbucket or other repositories.
In what follows github will be used as an example. Please consult the
devtools_ documentation for examples of other repositories.

Depending on how your R library is managed, you will need a slightly different
approach to use and install devtools.

.. note::

  When consulting the devtools documentation, make sure that it is the correct version!
  It should match the devtools version included in the R module. To check which devtools version is installed:

  .. code-block:: r
    
    library(devtools)
    sessioninfo::session_info()

Installing in a local R library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you manage your R packages in a :ref:`local R library<r_package_management_standard_lib>` under ``$VSC_DATA/Rlibs``
while using a centrally installed R module, you can use the devtools package included in the module.
You will need to execute the following commands in the R console:

.. code-block:: r

   > # First check that the R library path points to your local R library:
   > .libPaths()
   > # Set the R library path if this is not the case. e.g.
   > .libPaths("/data/leuven/XXX/vscXXXXX/Rlibs/rocky8/icelake/R-4.2.2")
   > # Load devtools and e.g. install your package from github:
   > library(devtools)
   > install_github("Developer/Package")

.. note::

  The devtools package is **not** included in "-bare" R modules, e.g. R/4.0.2-foss-2018a-bare.

Installing in a conda environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are using conda to manage your R packages, you should first install
devtools in your conda environment. The following steps assume that you 
already have a conda environment named "science". If you do not yet have
a conda environment, First create a :ref:`conda environment<r_package_management_conda>`. 
In the following example, it is assumed that your miniconda environment is installed in ``$VSC_DATA/miniconda3``.

.. code-block:: bash

   $ # Activate your conda environment and install devtools
   $ source activate science
   $ conda install -c conda-forge r-devtools
   $ # Launch R
   $ R

.. code-block:: r

   > # Check that the R library path points to your conda R library
   > .libPaths()
   > # Set the R library path if this was not the case.
   > .libPaths("/data/leuven/XXX/vscXXXXX/miniconda3/envs/science/lib/R/library")
   > # Load devtools and e.g. install your package from github:
   > library(devtools)
   > devtools::install_github("Developer/Package")

.. _devtools: https://www.rdocumentation.org/packages/devtools
