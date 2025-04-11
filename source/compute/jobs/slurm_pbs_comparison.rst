.. _Slurm_PBS_differences:

Main differences between Slurm and Torque
=========================================

- **Environment at job start:**

  - Torque does by default start with the login environment of a user.

  - Slurm starts by default with the environment from which the job was
    submitted (essentially the effect of ``qsub -V`` in Torque).
    This can have unexpected results, e.g., if you resubmit the job from a different
    environment or if some things are in different directories on the login and cluster
    nodes which does sometimes happen when we do a silent upgrade of the cluster.

    - ``--get-user-env`` will give an environment pretty equivalent
      to what you would get on Torque

    - ``--export=NONE`` will start the job with a very empty environment

- **Working directory at job start:** This is in fact a logical consequence of the previous
  bullet.

  - In Torque, the job start in your home directory. You can go to the directory from which
    the job was submitted with ``cd $PBS_O_WORKDIR``.

  - In Slurm, a job starts in the directory from which the job was submitted.

- **Redirection of stdout and stderr:**

  - In Torque, stdout and stderr go to different files by default. Both streams can be merged
    in a single file as in Slurm by specifying ``-j oe`` in the job script or at the qsub command line.

  - In Slurm, stdout and stderr are merged into a single file by default. You can change the behaviour
    by specifying a filename for stderr using ``--output-err``.

.. role:: raw-html(raw)
    :format: html

.. _Slurm_convert_from_PBS:

Converting PBS/Torque options to Slurm
--------------------------------------

**This is preliminary documentation for the pilot users. It will be further refined as the pilot progresses.**

See also the `Slurm Rosetta Stone of Workload Managers (PDF document) <https://slurm.schedmd.com/rosetta.pdf>`_.

Common tasks in Torque/Moab and Slurm
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

==========================================  ==================  =======================
Task                                        Torque/Moab         Slurm equivalent
==========================================  ==================  =======================
Submit a job                                qsub                sbatch
Cancel a job                                qdel                scancel
List jobs in the queue                      qstat, showq        squeue
==========================================  ==================  =======================


#PBS / qsub command line options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When specified in a Torque job script, the line specifying the option should start with ``#PBS``.
In Slurm, such lines start with ``#SBATCH``.

===================================  =====================
PBS/Torque                           Slurm equivalent
===================================  =====================
-L tasks=\ **<X>**:lprocs=\ **<Y>**  --ntasks=\ **<X>** --cpus-per-task=\ **<Y>**
-l nodes=\ **<N>**:ppn=\ **<P>**     | --nodes=\ **<N>** --ntasks-per-node=\ **<P>**
                                     |
                                     | or in the case of e.g. hybrid MPI-OpenMP runs:
                                     |
                                     | --nodes=\ **<N>** --ntasks-per-node=\ **<Q>** --cpus-per-task=\ **<P/Q>**
-l walltime=\ **<time>**             -t **<time>**\ , --time=\ **<time>**
-N **<jobname>**                     -J **<jobname>**\, --job-name=\ **<jobname>**
-o **<file>**                        -o **<file template>**\ , --output=\ **<file template>**
-e **<file>**                        -e **<file template>**\ , --error=\ **<file template>**\ , default is sending stderr to stdout
-m abe                               --mail-type=FAIL,BEGIN,END
-M **<mailaddress>**                 --mail-user=\ **<mailaddress>**
-v **<variable list>**               --export=\ **<variable list>**
===================================  =====================


Environment variables
~~~~~~~~~~~~~~~~~~~~~

========================  ================================
PBS variable              Slurm variable
========================  ================================
PBS_JOBID                 SLURM_JOB_ID :raw-html:`<br />`
                          SLURM_JOBID (for backward compatibility)
PBS_JOBNAME               SLURM_JOB_NAME :raw-html:`<br />`
                          %j in filename templates
PBS_NODENUM
PBS_NODEFILE              Replaced by a variable specifying the nodes rather than a node file: SLURM_JOB_NODELIST, SLURM_NODELIST (for backward compatibility)
PBS_NUM_NODES             SLURM_JOB_NUM_NODES :raw-html:`<br />`
                          SLURM_NNODES (for backward compatibility)
PBS_NUM_PPN
PBS_NP
PBS_O_WORKDIR             SLURM_SUBMIT_DIR :raw-html:`<br />`
                          Slurm executes the job in the directory from which the job was submitted (unless otherwise specified) rather than the home dir.
PBS_WALLTIME              No equivalent.
========================  ================================

