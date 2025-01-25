#########################
:fas:`key` Authentication
#########################

Connections to VSC clusters and web services are always encrypted to secure
your data. We currently support two types of authentication for connections to
VSC clusters:

* :ref:`auth_key_pair`

* :ref:`auth_mfa`

The difference between cryptographic key-based authentication and multi-factor
authentication (MFA) lies on the risk that somebody else can impersonate you and use
your credential to log in to VSC clusters and services. MFA requires that you
validate the authentication with another device apart from the computer being
used to connect to the cluster, making it much harder for an attacker to use
your log in credentials.

It is important to note that the security of your data with both methods is the
same once the connection has been established. The type of encryption of the
resulting connection does not depend on the authentication method.

.. _auth_key_pair:

Cryptographic Key Pair
======================

Connections with key-based authentication are only possible to the
:ref:`terminal interface` of the following VSC clusters:

.. include:: clusters_ssh_key.rst

Connections to VSC web-base services, such as the `VSC account page`_ or the
:ref:`compute portal` to VSC clusters, are always handled with MFA following
the security policies of your home institution.

The following sections explain how to create and manage your cryptographic keys
to connect to supported clusters.

.. toctree::
   :maxdepth: 3

   generating_keys

.. _auth_mfa:

Multi-factor Authentication
===========================

Multi Factor Authentication (MFA) is an augmented level of security
which, as the name suggests, requires multiple steps to successfully
authenticate.

Connections with MFA are currently supported on all VSC web-based services,
such as the `VSC account page`_ or the :ref:`compute portal` to VSC clusters,
and also on the :ref:`terminal interface` of the following VSC clusters:

.. include:: clusters_mfa.rst

The following sections explain how to set up MFA to connect to supported
clusters.

.. toctree::
   :maxdepth: 3

   mfa_login   

Connections from Abroad
=======================

All VSC clusters are behind a firewall, which is configured by default to block
all traffic from abroad. If you want to access any VSC cluster from
abroad, it is necessary that you first authorize your own connection on the
`VSC Firewall`_. Once your connection is authorized, you can proceed as usual.

.. note::

   Keep the `VSC Firewall`_ page open for the duration of your session on the
   VSC cluster.

