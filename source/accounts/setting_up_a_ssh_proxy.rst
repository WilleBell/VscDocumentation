.. _ssh_proxy:

Setting up an SSH proxy
=======================

.. warning::

   If you simply want to use OpenSSH to connect to login nodes
   of the VSC clusters, this is not the page you are looking for.
   Please check out :ref:`how to use the ssh command <OpenSSH access>`.


Rationale
---------

ssh provides a safe way of connecting to a computer, encrypting traffic
and avoiding passing passwords across public networks where your traffic
might be intercepted by someone else. Yet making a server accessible
from all over the world makes that server very vulnerable. Therefore
servers are often put behind a *firewall*, another computer or device
that filters traffic coming from the internet.

In the VSC, all clusters are behind a firewall, but for the tier-1
cluster muk this firewall is a bit more restrictive than for other
clusters. Muk can only be approached from certain other computers in the
VSC network, and only via the internal VSC network and not from the
public network. To avoid having to log on twice, first to another login
node in the VSC network and then from there on to Muk, one can set up a
so-called *ssh proxy*. You then connect through another computer (the
*proxy server*) to the computer that you really want to connect to.

This all sounds quite complicated, but once things are configure
properly it is really simple to log on to the host.

Setting up a proxy in OpenSSH
-----------------------------

Setting up a proxy is done by adding a few lines to the file
``$HOME/.ssh/config`` on the machine from which you want to log on to
another machine.

The basic structure is as follows:

::

   Host <my_connectionname>
       ProxyCommand ssh -q %r@<proxy server> 'exec nc <target host> %p'
       User <userid>

where:

-  ``<my_connectionname>``: the name you want to use for this proxy
   connection. You can then log on to the ``<target host>`` using this
   proxy configuration using ssh ``<my_connectionname>``
-  ``<proxy server>``: The name of the proxy server for the connection
-  ``<target host>``: The host to which you want to log on.
-  ``<userid>``: Your userid on ``<target host>``.

**Caveat:**\ *Access via the proxy will only work if you have logged in
to the proxy server itself at least once from the client you're using.*

Some examples
-------------

A regular proxy without X forwarding
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In Linux or macOS, SSH proxies are configured as follows:

In your ``$HOME/.ssh/config`` file, add the following lines:

::

   Host tier1
       ProxyCommand ssh -q %r@vsc.login.node 'exec nc login.muk.gent.vsc %p'
       User vscXXXXX

where you replace *vsc.login.node* with the name of the login node of
your home tier-2 cluster (see also the :ref:`overview of available
hardware <hardware>`).

Replace ``vscXXXXX`` your own VSC account name (e.g., ``vsc40000``).

The name 'tier1' in the 'Host' field is arbitrary. Any name will do, and
this is the name you need to use when logging in:

::

   $ ssh tier1

A proxy with X forwarding
~~~~~~~~~~~~~~~~~~~~~~~~~

This requires a minor modification to the lines above that need to be
added to ``$HOME/.ssh/config``:

::

   Host tier1X
       ProxyCommand ssh -X -q %r@vsc.login.node 'exec nc login.muk.gent.vsc %p'
       ForwardX11 yes
       User vscXXXXX

I.e., you need to add the -X option to the ssh command to enable X
forwarding and need to add the line '``ForwardX11 yes``'.

::

   $ ssh tier1X

will then log you on to login.muk.gent.vsc with X forwarding enabled
provided that the ``$DISPLAY`` variable was correctly set on the client on
which you executed the ssh command. Note that simply executing

::

   $ ssh -X tier1

has the same effect. It is not necessary to specify the X forwarding in
the config file, it can be done just when running ssh.

The proxy for testing/debugging on muk
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For testing/debugging, you can login to the UGent login node
gengar1.gengar.gent.vsc over the VSC network. The following
``$HOME/.ssh/config`` can be used:

::

   Host tier1debuglogin
       ProxyCommand ssh -q %r@vsc.login.node 'exec nc gengar1.gengar.gent.vsc %p'
       User vscXXXXX

Change ``vscXXXXX`` to your VSC username and connect with

::

   $ ssh tier1debuglogin

For advanced users
------------------

You can define many more properties for a ssh connection in the config
file, e.g., setting up ssh tunneling. On most Linux machines, you can
get more information about all the possibilities by issuing

::

   $ man 5 ssh_config

Alternatively, you can also google on this line and find `copies of the
manual page on the internet <http://www.manpagez.com/man/5/ssh_config/>`__.
