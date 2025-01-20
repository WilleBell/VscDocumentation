.. _Subversion:

Subversion
==========

Preparation
-----------

The Subversion software is installed on the cluster. On most systems it
is default software and does not need a module (try ``which svn`` and
<code>which svnadmin to check if the system can find the subversion
commands). On some systems you may have to load the appropriate module,
i.e.,

::

   $ module load subversion

When you are frequently using Subversion, it may be convenient to load
this module from your '.bashrc' file. (Note that in general we strongly
caution against loading modules from '.bashrc', so this is an
exception.)

Since some Subversion operations require editing, it may be convenient
to define a default editor in your '.bashrc' file. This can be done by
setting the 'EDITOR' variable to the path of your favorite editor, e.g.,
emacs. When this line is added to your '.bashrc' file, Subversion will
automatically launch this editor whenever it is required.

::

   export EDITOR=/usr/bin/emacs

Of course, any editor you are familiar with will do.

Creating a repository
---------------------

To create a Subversion repository on a VSC cluster, the user first has
to decide on its location. We suggest to use the data directory since

#. its default quota are quite sufficient;
#. if the repository is to be shared, the permissions on the user's home
   directory need not to be modified, hence decreasing potential
   security risks; and
#. only for users of the KU Leuven cluster, the data directory is
   backed up (so is the user's home directory, incidentally).

Actually creating a repository is very simple:

::

   $ cd $VSC_DATA
   $ svnadmin create svn-repo

#. Log in on the login node.
#. Change to the data directory using
#. Create the repository using

Note that a directory with the name 'svn-repo' will be created in your
'$VSC_DATA' directory. You can choose any name you want for this
directory. Do not modify the contents of this directory since this will
corrupt your repository unless you know quite well what you are doing.

At this point, it may be a good idea to read the section in the
Subversion book on the `repository
layout <http://svnbook.red-bean.com/en/1.5/svn.reposadmin.planning.html#svn.reposadmin.projects.chooselayout>`__.
In this How-To, we will assume that each project has its own directory
at the root level of the repository, and that each project will have a
'trunk', 'branches' and 'tags' directory. This is recommended practice,
but you may wish to take a different approach.

To make life easier, it is convenient to define an environment variable
that contains the URI to the repository you just created. If you work
with a single repository, you may consider adding this to your '.bashrc'
file.

::

   export SVN=\"svn+ssh://vsc98765@vsc.login.node DATA/svn-repo\"

Here you would replace 'vsc98765' by your own VSC user ID,
'vsc.login.node' by the login node of your VSC cluster, and finally,
'DATA' by the value of your '$VSC_DATA' variable.

Putting a project under Subversion control
------------------------------------------

Here, we assume that you already have a directory that contains an
initial version of the source code for your project. If not, create one,
and populate it with some relevant files. For the purpose of this
How-To, the directory currently containing the source code will be
called '$VSC_DATA/simulation', and it will contain two source files,
'simulation.c' and 'simulation.h', as well as a make file 'Makefile'.

Preparing the repository
~~~~~~~~~~~~~~~~~~~~~~~~

Since we follow the Subversion community's recommended practice, we
start by creating the appropriate directories in the repository to house
our project.

::

   $ svn mkdir -m 'simulation: creating dirs' --parents   \\
               $SVN/simulation/trunk    \\
               $SVN/simulation/branches \\
               $SVN/simulation/tags

The repository is now prepared so that the actual code can be imported.

Importing your source code
~~~~~~~~~~~~~~~~~~~~~~~~~~

As mentioned, the source code for your project exists in the directory
'$VSC_DATA/simulation'. Since the semantics of the 'trunk' directory of
a project is that this is the location where the bulk of the development
work is done, we will import the project into the trunk.

::

   $ svn import -m 'simulation: import' \\
                $VSC_DATA/simulation   \\
                $SVN/simulation/trunk

#. First, prepare the source directory '$VSC_DATA/simulation' by
   deleting all files that you don't want to place under version
   control. Remove artifacts such as, e.g., object files or executables,
   as well as text files not to be imported into the repository.
#. Now the directory can be imported by simply typing:

The project is now almost ready for development under version control.

Checking out
~~~~~~~~~~~~

Although the source directory has been imported into the subversion
repository, this directory is *not* under version control. We first have
to check out a working copy of the directory.

Since you are not yet familiar with subversion and may have made a
mistake along the way, it may be a good idea at this point to make a
backup of the original directory first, by, e.g.,

::

   $ tar czf $VSC_DATA/simulation.tar.gz $VSC_DATA/simulation

Now it is safe to checkout the project from the repository using:

::

   $ svn checkout $SVN/simulation/trunk $VSC_DATA/simulation

Note that the existing files in the'$VSC_DATA/simulation' directory have
been replaced by those downloaded from the repository, and that a new
directory '$VSC_DATA/simulation/.svn' has been created. It is the latter
that contains the information needed for version control operations.

Subversion work cycle
---------------------

The basic work cycle for development on your project is fairly
straightforward.

::

   $ cd $VSC_DATA/simulation
   $ svn update
   $ svn add utils.c utils.h
   $ svn status
   $ svn commit -m 'simulation: implemented a very interesting feature'

#. Change to the directory containing your project's working copy, e.g.,
#. Update your working copy to the latest version, see 
   :ref:`svn_updating_working_copy` 
   below for a brief introduction to the topic.
#. Edit the project's files to your heart's content, or add new files to
   the repository after you created them, e.g., 'utils.c' and 'utils.h'.
   Note that the new files will only be stored in the repository upon
   the next commit operation, see below.
#. Examine your changes, this will be elaborated upon in 
   :ref:`svn_status`.
#. Commit your changes, i.e., all changes you made to the working copy
   are now transfered to the repository as a new revision.
#. Repeat steps 2 to 5 until you are done.

If you are the sole developer working on this project and exclusively on
the VSC cluster, you need not update since your working copy will be the
latest anyway. However, an update is vital when others can commit
changes, or when you work in various locations such as your desktop or
laptop.

Other subversion features
-------------------------

It would be beyond the scope of this How-To to attempt to stray too far
from the mechanics of the basic work cycle. However, a few features will
be highlighted since they may prove useful.

A central concept to almost all version control systems is that of a
version number. In Subversion, all operations that modify the current
version in the repository will result in an automatic increment of the
revision number. In the example above, the 'mkdir' would result in
revision 1, the 'import' in revision 2, and each consecutive 'commit'
will further increment the version number.

Reverting to a previous version
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The most important point of any version control system is that it is
possible to revert to some revision if necessary. Suppose you want to
revert to the state of the original import, than this can be
accomplished as follows:

::

   $ svn checkout -r 2 $SVN/simulation/trunk simulation-old

Finding changes between revisions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Finding changes between revisions, or between a certain revision and the
current state of the working copy is also fairly easy:

::

   $ svn diff -r HEAD simulation.c

Examining history
~~~~~~~~~~~~~~~~~

To many Subversion operations, e.g., 'mkdir' and 'commit', with a
message can be added (the '-m <string>' in the commands of the previous
section), and they will be associated with the resulting revision
number. When used consistently, these comments can be very useful since
they can be reviewed later whenever one has to examine changes made to
the project. If a repository hosts multiple projects, it is wise to have
some sort of convention, e.g., to start the comments on a project by its
name as a tag. Note that this convention was followed in the examples
above. One can for instance show all messages associated with changes to
the file 'simulation.c' using:

::

   $ svn log simulation.c

Deleting and renaming
~~~~~~~~~~~~~~~~~~~~~

When a file is no longer needed, it can be removed from the current
version in the repository, as well as from the working copy.

::

   $ svn rm Makefile

The previous command would remove the file 'Makefile' from the working
directory, and tag it for deletion from the current revision upon the
next commit operation. Note that the file is not removed from the
repository, it is still part of older revisions.

Similarly, a file may have to be renamed, an operation that is also
directly supported by Subversion.

::

   $ svn mv utils.c util.c

Again, the change will only be propagated to the repository upon the
next commit operation.

.. _svn_status:

Examining status
~~~~~~~~~~~~~~~~

While development progresses, the working copy differs more and more
from the latest revision in the repository, i.e., HEAD. To get an
overview of files that were modified, added, deleted, etc., one can
examine the status.

::

   $ svn status

This results in a list of files and directories, each preceded by a
character:

-  M: file is modified
-  A: file has been added
-  D: file has been deleted
-  ?: file is not (yet) under version control (it should be added if it
   needs to be)

When nothing has been modified since the last commit, this command shows
no output.

.. _svn_updating_working_copy:

Updating the working copy
~~~~~~~~~~~~~~~~~~~~~~~~~

When the latest revision in the repository has changed with respect to
the working copy, an update of the latter should be done before
continuing the development.

::

   $ svn update

This may be painless, or require some work. Subversion will try to
reconciliate the revision in the repository with your working copy.
When changes can safely be applied, subversion does so automatically.
The output of the 'update' command is a list of files, preceded by
characters denoting status information:

-  A: file was not in the working copy, and has now been checked out.
-  U: file was modified in the repository, but not in the working copy,
   the latter has been modified to reflect the changes.
-  G: file was modified in both the working copy and the repository, the
   changes have been merged automatically.

In case of conflict, e.g., the same line of a file was changed in both
the repository and the working copy, Subversion will offer a number of
options to resolve the conflict.

::

   Conflict discovered in 'simulation.c'.
   Select: (p) postpone, (df) diff-full, (e) edit,
    (mc) mine-conflict, (tc) theirs-conflict,
    (s) show all options:

The safest option is to choose to edit the file, i.e., type 'e'. The
file will be opened in an editor with the conflicts clearly marked. An
example is shown below:

::

   <<<<<<< .mine
    printf(\"bye world simulation!\\n\");
   =======
    printf(\"hello nice world simulation\\n\");
   >>>>>>> .r7

Here '.mine' indicates the state in your working copy, '.r7' that of
revision 7 (i.e., HEAD) in the repository. You can now resolve them
manually by editing the file. Upon saving the changes and quitting the
editor, the option 'resolved' will be added to the list above. Enter 'r'
to indicate that the conflict has indeed been resolved successfully.

Tagging
~~~~~~~

Some revisions are more important than others. For example, the version
that was used to generate the data you used in the article that was
submitted to Nature is fairly important. You will probably continue to
work on the code, adding several revisions while the referees do their
job. In their report, they may require some additional data, and you
will have to run the program as it was at the time of submission, so you
want to retrieve that version from the repository. Unfortunately,
revision numbers have no semantics, so it will be fairly hard to find
exactly the right version.

Important revisions may be tagged explicitly in Subversion, so choosing
an appropriate tag name adds semantics to a revision. Tagging is
essentially copying to the tags directory that was created upon setting
up the repository for the project.

::

   $ svn copy --parents -m 'simulation: tagging Nature submission' \\
              $SVN/simulation/trunk           \\
              $SVN/simulation/tags/nature-submission

It is now trivial to check out the version that was used to compute the
relevant data:

::

   $ svn checkout $SVN/simulation/tags/nature-submission \\
                  simulation-nature

Desktop access
--------------

It is also possible to access VSC subversion repositories from your
desktop. See the pages in the :ref:`Windows client <windows_client>`,
:ref:`macOS client <macos_client>` and :ref:`Linux client <linux_client>`
sections.

Further information on Subversion
---------------------------------

Subversion is a rather sophisticated version control system, and in this
mini-tutorial for the impatient we have barely scratched the surface.
Further information is available in an `online book on
Subversion <http://svnbook.red-bean.com/>`__, a must read for
everyone involved in a non-trivial software development project that
used subversion.

Subversion can also provide help on commands:

::

   $ svn help
   $ svn help commit

The former lists all available subversion commands, the latter form
displays help specific to the command, 'commit' in this example.
