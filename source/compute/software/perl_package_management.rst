.. _Perl packages:

Perl package management
=======================

Introduction
------------

(**Note:** *the Perl community uses the term 'modules' rather than
'packages', however, in the documentation, we use the term 'packages' to
try and avoid confusion with the module system for loading software.*)

Perl comes with an extensive standard library, and you are strongly
encouraged to use those packages as much as possible, since this will
ensure that your code can be run on any platform that supports Perl.

However, many useful extensions to and libraries for Perl come in the
form of packages that can be installed separately. Some of those are part
of the default installation on VSC infrastructure.

Given the astounding number of packages, it is not sustainable to
install each and everyone system wide. Since it is very easy for a user
to install them just for himself, or for his research group, that is not
a problem though. Do not hesitate to contact support whenever you
encounter trouble doing so.

Checking for installed Perl packages
------------------------------------

To check which Perl packages are installed, the ``cpan`` utility is
useful. It will list all packages that are installed for the Perl
distribution you are using, including those installed by you, i.e.,
those in your ``PERL5LIB`` environment variable.

#. Load the module for the Perl version you wish to use, e.g., ::

      $ module load Perl/5.28.2-foss-2018a

#. Run cpan: ::

      $ cpan  -l

Installing your own Perl packages
---------------------------------

Setting up your own package repository for Perl is straightforward. For
this purpose, the ``cpan`` utility first needs to be configured. Replace
the paths ``/user/leuven/301/vsc30140`` and ``/data/leuven/301/vsc30140``
by the ones to your own home and data directories.

#. Load the appropriate Perl module, e.g., ::

      $ module load Perl/5.28.2-foss-2018a

#. Create a directory to install in, i.e., ::

      $ mkdir $VSC_DATA/perl5

#. Run cpan: ::

      $ cpan

#. Configure internet access and mirror sites: ::
   
      cpan[1]> o conf init connect_to_internet_ok urllist

#. Set the install base, i.e., directory created above: ::

      cpan[2]> o conf makepl_arg INSTALL_BASE=/data/leuven/301/vsc30140/perl5

   Note that you can not use an environment variable for the path.

#. Set the preference directory path: ::

      cpan[3]> o conf prefs_dir /user/leuven/301/vsc30140/.cpan/prefs

#. Commit changes so that they are stored in ``~/.cpan/CPAN/MyConfig.pm``, i.e., ::

      cpan[4]> o conf commit

#. Quit cpan: ::

      cpan[5]> q

Now Perl packages can be installed easily, e.g., ::

   $ cpan IO::Scalar

Note that this will install all dependencies as needed, though you may
be prompted.

To effortlessly use locally installed packages, install the local::lib
package first, and use the following code fragment in Perl scripts that
depend on locally installed packages.  ::

   use local::lib;
