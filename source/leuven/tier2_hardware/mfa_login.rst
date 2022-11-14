.. _mfa_leuven:

Multi Factor Authentication (MFA)
=================================

About
-----

Multi Factor Authentication (MFA) is an augmented level of security which, as
the name suggests, requires multiple steps when authenticating. The following
two factors are necessary to connect to the KU Leuven clusters:

- A valid private key
- Access to a VSC-associated university/institution account

This approach ensures that if your private key is compromised, the person who
has unauthorized access to it will still not be able to abuse your VSC account.
This document provides two methods to connect to the KU Leuven clusters with
MFA. The first method, without an agent, is easier to get started with, but requires
some repetitive steps with each connection. The second method, *with* an agent,
requires some more effort to set up initially but will be easier when you need
multiple connections or you are using some specific GUI applications to connect. 

MFA without an agent
--------------------

The following steps are applicable when you do not have an ssh agent running
and you want to log in to the cluster with an ssh client like PuTTY or
MobaXTerm. It also applies when using the ssh command in a terminal on Linux,
Mac, or WSL.

#. [Optional] Visit the `VSC firewall page`_ in a browser and login with your
   institution credentials. This step is optional when connecting from a
   white-listed IP address (among which are the KU Leuven address range, Flemish
   universities, most Belgian Internet providers,...). It is **mandatory** for
   other IP addresses (for instance when you are connecting from abroad).
#. Launch your ssh client and choose your private VSC key for authentication as
   usual.
#. If the private key is accepted, your ssh client will now prompt you with an
   URL. Visit this URL in a browser and authenticate with your institution
   credentials. Note that this link is dynamically generated, you cannot use it
   more than once.

The last step is the second factor in the authentication process. When following
that link, you will be asked to choose your university/association and log in.
If you already did this in the same browser session (for instance when accessing
another university-related web sites or during step 1), the login will proceed
automatically. If the login was successful, you will be redirected to a page
with the message that your VSC identity was confirmed.

Your ssh connection is now completed!

.. note::

    For `login[-tier1].hpc.kuleuven.be` only, a successful connection will
    white list your IP address for 90 days. Within that time frame, the first
    step mentioned above becomes optional.
    
.. note::

    The above method works fine to create the connection through MobaXTerm.
    The included file explorer will show you the files, but opening, downloading
    and uploading files from here does not work without the agent. If you would 
    like to use the file explorer, have a look at :ref:`Authentication with an ssh agent<mfa_agent>` . 


GUI applications with SSH connection in the background
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some applications such as FileZilla provide a Graphical User Interface
(GUI) that will setup an ssh connection under the hood. This leads to a problem
with step 3 from the last section, as those applications will typically not
prompt you the firewall URL (some applications like WinSCP will do this however).
To work around this, you should first login to the KU Leuven cluster with an
ssh-client on your machine as explained in the previous section. As long as you
keep this connection open, you can connect with the other apps as well. This
extra step can however also be avoided by using an ssh agent, which will be
explained in the next part.

.. note::

   This method will not always work for NX. It is highly recommended to use
   the method with an :ref:`ssh agent<mfa_agent>` when using MFA with NX.

.. note::
   It is currently not possible to connect to NX when using a ED25519 keytype.
   The RSA4096 keytype does allow you to connect. As this is the recommended
   keytype for connections to the HPC clusters, this should not be an issue for
   most users.

.. _mfa_agent:

Authentication with an ssh agent
--------------------------------

.. note :: 

   In the past we have provided our own in-house SSH agent, vscagent, but PuTTY
   0.78< allows you to store a certificate as well. For this reason, we have
   changed our official recommendation for an agent to the PuTTY agent.

In order to circumvent the annoyance of multiple MFA prompts or connecting to
the cluster with an ssh client before being able to use certain apps like FileZilla,
you can use an agent. This agent will store a certificate that contains the
identity verification you did when following the firewall link. This way, you
will only be asked to verify your identity once. Of course this certificate
does not live forever. The certificate will only exist for as long as your agent lives.

For all agents, you first need to connect to the cluster from a terminal, or an appropriate
SSH client, in the way as described above, after which the certificate will automatically
be stored in the agent.

To adopt any of these methods, read the following parts. The method you can use varies based on your OS.

.. _agent_windows:   
Authentication with an agent on Windows
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For MS-Windows systems, the PuTTY agent (Pageant) is the recommended agent to use. You should download 
`the latest PuTTY release`_ or at least version 0.78 if you plan on using an older version. For users
with a KU Leuven laptop, this version should be available in your software center. 

There are two methods to store your **key** in Pageant. In both cases the key will only be stored for as long as the agent is running.

#. Open PuTTY and start your saved session, with the path to your key stored under 'Connection-SSH-Auth-Credentials'. Once you connect using the standard method, your key will be stored in Pageant.
#. Be sure Pageant is started, go to your system tray, right-click the Pageant symbol and select 'Add Key'. Add the key you want to use. 

To effectively store your certificate, you need to connect to the cluster first. You can do this by connecting
through PuTTY, but other SSH clients like MobaXTerm, if configured correctly, can do this too
(see :ref:`Configuration of ssh-clients and UI apps<mfa_client_config>` for the correct set-up). If you want to use
this method, you need to store the key in Pageant first though. 

Once the certificate is stored, you should be able to connect to the cluster using any
ssh-client or with GUI apps like NX and FileZilla.

.. note::
   It might be that you have to
   adapt some options in the configuration of these apps. Have a look at the
   :ref:`Configuration of ssh-clients and GUI apps<mfa_client_config>` below.

Authentication with an agent on Linux/Mac/WSL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For Linux, Mac, and WSL you can use the built-in ssh agent. If you would prefer
a user interface, you can also use the the previously mentioned vscagent.
Instead of downloading `vscagent.exe` download `vscagent` and run
`vscagent gui` and follow the above procedure to configure it.
**Be aware that the vscagent does not work for Macs with an M1 processor!**

If you prefer using the built-in ssh-agent, use the following instructions to configure it
correctly.

.. note::
   Before starting out with the built-in SSH agent, you should know that using an SSH agent
   poses a possible security threat when you are not using this with care. When using an
   SSH agent for a connection to a remote server, all certificates that are stored in your agent
   are visible for root users on the remote server. So be sure to **only** use the agent
   for trusted servers. If you would prefer more secure set-ups, have a look at 
   :ref:`Secure your SSH agent set-up<secure_ssh_agent>` .

First of all, verify that it is running. You can do this by executing:: 

    ssh-add -l

If the agent is not running, you will get a
'Could not open a connection to your authentication agent.' message. In this
case you can start the agent with::

    eval $(ssh-agent)

(to kill the agent use ``eval "$(ssh-agent -k)"``)

If your agent is running, the ``ssh-add -l`` will show the identities that were
added to the ssh-agent. If none are added, the output will state
'The agent has no identities.'. You can add your key with
``ssh-add </path/to/your/private/key>``. Now ``ssh-add -l`` should show your key.

Now, depending on how ssh is configured, it might be that your key will not be
stored by default. It is probably best to verify the following steps before
continuing:

#. Adapt or create a profile for your cluster connection in the config file in
   your .ssh folder. If you do not have a config file there, create one first.
   From your home dir::

      touch ~/.ssh/config
      chmod 600 ~/.ssh/config

#. In this file you can create a profile for each of your connections and add
   options specifically for that connection. For Tier-2::

      Host login.hpc.kuleuven.be
        ForwardAgent yes
        PubkeyAuthentication yes
        ChallengeResponseAuthentication yes
        PreferredAuthentications publickey,keyboard-interactive

The indentation is not strictly necessary, but is recommended for readability.

If you now connect to the cluster using your standard `ssh` command, the
certificate will automatically be stored for as long as your agent lives. If
you want to use apps that use ssh in the background (NX, FileZilla), you will
have to do this connection to the cluster as well. You are free to log out of
that session afterwards. 

.. note::

    You might have to adapt some options in the configuration of your
    connection profiles in some apps. Have a look at
    :ref:`Configuration of ssh-clients and UI apps<mfa_client_config>` below.
   
   
.. _secure_ssh_agent:   

Secure your SSH agent set-up
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are ways to protect yourself from possible malicious attempts
to use certificates stored in your SSH agent on your local machine. A root user
on any remote server can access those certificates, and can use those to connect
to the remote servers for which you also have a certificate stored in your agent, 
and this connection will happen in your name. Luckily, as the agent only lives
for a limited time this threat also only exists for a limited time.
You also don't expose your private key.

Before diving into the technical settings, the first step to ensure your security,
is vigilance. Think about what connections could potentially pose a risk. Avoid
adding those to your agent. When connecting to our cluster you set up the agent
to avoid that you need to follow the firewall link every time. If you see no special
need for using an agent, it is probably better to avoid it.

If you do wish to use multiple certificates in your agent, you can also add
your key to your agent using::

    ssh-add -c /path/to/your/private/key
    
The '-c' will ask for a confirmation every time you (or in the worst case someone
else in your name) tries to use the agent to connect to a remote server. You will
manually have to click 'ok' before. You might have to install the package 'ssh-askpass'
on your local machine first.


.. _mfa_client_config:

Configuration of ssh-clients and GUI apps
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have not yet set up your ssh-client or other apps that use ssh to be
able to use an agent, you might have to make some changes in your connection
profiles. Different apps will need different changes, but here we shortly show
what to do for MobaXTerm, PuTTY and NX:

- MobaXTerm

  - right-click on the user session you have created to connect to the Tier-2 cluster and choose 'Edit Session'
  - Select the 'Advanced SSH settings' tab
  - Uncheck 'Use private key' if selected
  - click 'Ok'
  - If you want to be able to store the certificate directly by connecting through MobaXTerm, be sure that 'Use external Pageant' is checked. You of course still need to store the key in your Pageant first, as explained under :ref:`Authentication with an agent on Windows<agent_windows>`.
    
- PuTTY

  - Load your profile to connect to the Tier-2 cluster
  - Go to 'Auth' under 'Connection'
  - Be sure that 'Allow agent forwarding' is checked
  - If you have a private key file stored under 'Private key file for authentication', remove it
    
- NX

  - Right-click on the connection to the Tier-2 cluster
  - Click on 'Edit connection'
  - Select the 'Configuration' tab
  - Select 'Use key-based authentication with a SSH agent'
  - Click 'Modify' and verify that 'Forward authentication' is checked

Known issues - General remarks
------------------------------

- It has happened that users cannot properly load the MFA URL. If that would
  happen to you, it is worth trying to paste the URL in an incognito browser
  window. This has only been verified to work in Chrome and does not seem to
  work in Firefox.
- MobaXTerm: version 21.1 has known issues in combination with the vscagent.
  It does not always seem to find the certificate in your agent. Updating to
  the latest version should solve this.
- If you are using sshfs, no link will be prompted to you as when using ssh.
  This is intended to be this way. The recommended approach would be to use an
  ssh agent to store your certificate. This will avoid you having to connect
  with the MFA link every time when connecting to the cluster.
- Safari does not properly load the vscagent download page. 
- Some ssh-clients have their own built-in agents that can prompt you the
  firewall link. You are free to use these instead of agent as well.

.. _VSC firewall page: https://firewall.vscentrum.be
.. _vscagent: https://firewall.vscentrum.be/vscagent/latest/
.. _latest PuTTY release: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
