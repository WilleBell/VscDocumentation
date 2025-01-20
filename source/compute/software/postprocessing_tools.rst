Postprocessing tools
====================

This section is still rather empty. It will be expanded over time.

Visualization software
----------------------

-  `ParaView <https://www.paraview.org/>`__ is a free
   visualization package. It can be used in three modes:

   -  Installed on your desktop: you have to transfer your data to your
      desktop system
   -  As an interactive process on the cluster: this option is available
      only for :ref:`NoMachine NX
      users <NX start guide>` (go to the
      Applications menu -> HPC -> Visualisation -> Paraview).
   -  In client-server mode: The interactive part of ParaView is running
      on your desktop, while the server part that reads the data and
      renders the images (no GPU required as ParaView also contains a
      software OpenGL renderer) and sends the rendered images to the
      client on the desktop. Setting up ParaView for this scenario is
      explained in the :ref:`page on ParaView remote
      visualization <Paraview>`.
