.. role:: raw-html(raw)
    :format: html

.. _Vaughan hardware:

################
Vaughan hardware
################

The Vaughan compute nodes should be used for sufficiently large parallel jobs,
or when you can otherwise fill all cores of a compute node.
The Vaughan cluster also contains 2 node types (NVIDIA and AMD) for GPU computing.

For smaller jobs, consider using the :ref:`Leibniz<Leibniz hardware>` nodes.
For longer jobs or batches of single-core jobs, consider using the :ref:`Breniac<Breniac hardware UAntwerp>` nodes.

****************
Compute nodes
****************

When submitting a job with ``sbatch`` or using ``srun``, you can choose to specify
the partition your job is submitted to.
When the option is omitted, your job is submitted to the default partition (**zen2**).

CPU compute nodes
=================

The maximum execution wall time for jobs is **3 days** (72 hours).

===============  ======  ======================================  ======  ==========  =========
Slurm partition  nodes   processors |nbsp| per |nbsp| node       memory  local disk  network
===============  ======  ======================================  ======  ==========  =========
**zen2**         152     2x 32-core `AMD Epyc 7452`_ \@2.35 GHz  256 GB  240 GB SSD  HDR100-IB
zen3             24      2x 32-core `AMD Epyc 7543`_ \@2.80 GHz  256 GB  500 GB SSD  HDR100-IB
zen3_512         16      2x 32-core `AMD Epyc 7543`_ \@2.80 GHz  512 GB  500 GB SSD  HDR100-IB
===============  ======  ======================================  ======  ==========  =========

GPU compute nodes
=================

The maximum execution wall time for GPU jobs is **1 day** (24 hours).

===============  ======  ===================================  ==========  ======================================  ======  =================  =========
Slurm partition  nodes   GPUs |nbsp| per |nbsp| node          GPU memory  processors |nbsp| per |nbsp| node       memory  local |nbsp| disk  network
===============  ======  ===================================  ==========  ======================================  ======  =================  =========
ampere_gpu       1       4x `NVIDIA A100`_ (Ampere)           40 GB SXM4  2x 32-core `AMD Epyc 7452`_ \@2.35 GHz  256 GB  480 GB SSD         HDR100-IB
arcturus_gpu     2       2x `AMD Instinct MI100`_ (Arcturus)  32 GB HBM2  2x 32-core `AMD Epyc 7452`_ \@2.35 GHz  256 GB  480 GB SSD         HDR100-IB
===============  ======  ===================================  ==========  ======================================  ======  =================  =========

.. seealso:: See :ref:`GPU computing UAntwerp` for more information on using the GPU nodes.

.. _Vaughan login:

********************
Login infrastructure
********************

You can log in to the Vaughan cluster using SSH via ``login-vaughan.hpc.uantwerpen.be``.

Alternatively, you can also log in directly to the login nodes using one of the following hostnames.
From inside the VSC network (e.g., when connecting from another VSC cluster), use the internal interface names.

+--------------+-------------------------------------+--------------------------------+
| Login node   | External interface                  | Internal interface             |
+==============+=====================================+================================+
| generic name | login\-vaughan.hpc.uantwerpen.be    | login.vaughan.antwerpen.vsc    |
+--------------+-------------------------------------+--------------------------------+
| per node     | | login1\-vaughan.hpc.uantwerpen.be | | login1.vaughan.antwerpen.vsc |
|              | | login2\-vaughan.hpc.uantwerpen.be | | login2.vaughan.antwerpen.vsc |
+--------------+-------------------------------------+--------------------------------+

.. note:: Direct login is possible to all login nodes *from within Belgium only*.
  From outside of Belgium, a :ref:`VPN connection <vpn>` to the UAntwerp network is required.


- 2 login nodes

  - 2x 16-core `AMD EPYC 7282`_ CPUs\@2.8 GHz (zen2)
  - 256 GB RAM
  - 2x 480 GB HDD local disk (raid 1)

*********************
Compiling for Vaughan
*********************

To compile code for Vaughan, all ``intel``,
``foss`` and ``GCC`` modules can be used (the
latter being equivalent to ``foss`` but without MPI and the math libraries).

.. seealso::
  For general information about the compiler toolchains, please see the shared
  :ref:`Intel toolchain<Intel toolchain>` and
  :ref:`FOSS toolchain<FOSS toolchain>` documentation.

Optimization options for the Intel compilers
============================================

As the processors in Vaughan are made by AMD, there is no explicit support
in the Intel compilers. However, by choosing the appropriate compiler
options, the Intel compilers still produce very good code for Vaughan that
will often beat code produced by GCC (certainly for Fortran codes as gfortran
is a rather weak compiler).

To optimize for Vaughan, compile on the Vaughan login
or compute nodes and combine the option ``-march=core-avx2`` with either optimization
level ``-O2`` or ``-O3``. For some codes, the additional optimizations at
level ``-O3`` actually produce slower code (often the case if the code
contains many short loops).

|Warning| If you forget these options, the default for the Intel compilers
is to generate code using optimization level ``-O2`` for architecture ``-march=pentium4``.
While ``-O2`` gives pretty good results, compiling for the Pentium 4 architecture uses 
none of the new instructions nor the vector instructions introduced since 2005.

|Warning| The ``-x`` and ``-ax``-based options don't function properly on AMD processors.
These options add CPU detection to the code, and whenever detecting AMD
processors, binaries refuse to work or switch to code for the ancient
Pentium 4 architecture. In particular, ``-xCORE-AVX2`` is known to produce
non-working code.

Optimization options for the GNU compilers
==========================================

To optimize for Vaughan, compile on the Vaughan login
or compute nodes and combine either the option ``-march=native``, or
``-march=znver2`` or ``-march=znver3`` for the zen2 and zen3 nodes respectively.
You can combine this with either optimization
level ``-O2`` or ``-O3``. In most cases, and especially for
floating point intensive code, ``-O3`` will be the preferred optimization level
with the GNU compilers as it only activates vectorization at this level
(whereas the Intel compilers already offer vectorization at level ``-O2``).

|Warning| If you forget these options, the default for the GNU compilers is
to generate unoptimized (level ``-O0``) code for a very generic CPU
(``-march=x86-64``) which doesn't exploit the performance potential of
the Vaughan CPUs at all.

*******
History
*******

The Vaughan cluster was installed in the summer of 2020. It is a NEC system consisting of
152 compute nodes with dual 32-core `AMD EPYC 7452`_  Rome generation CPUs with
256 GB RAM, connected through an HDR100 InfiniBand network.
It also has 1 node with four `NVIDIA A100`_ (Ampere) GPU compute cards and
2 nodes equipped with two `AMD Instinct MI100`_ (Arcturus) GPU compute cards.

In the summer of 2023, the Vaughan cluster was extended with
40 compute nodes with dual 32-core `AMD EPYC 7543`_ Milan generation CPUs, 24
nodes with 256 GB RAM and 16 nodes 512 GB RAM. All Milan nodes are connected
through an HDR200 InfiniBand network.

Origin of the name
==================

Vaughan is named after `Dorothy Vaughan <https://en.wikipedia.org/wiki/Dorothy_Vaughan>`_,
an Afro-American mathematician who worked for NACA and NASA.
During her 28-year career, Vaughan prepared for the introduction of machine computers in
the early 1960s by teaching herself and her staff the programming language of Fortran.
She later headed the programming section of the Analysis and Computation Division (ACD)
at Langley.
