=========
Host Keys
=========

Frequently in the past, Minecraft had failures in its login code where players could login to a server as any player, including administrators and moderators. Between 2010 and 2013, exploits of this nature were made public five times, frequently leading to thousands of servers being hacked.

The host keys feature was added to WorldGuard as an extra barrier to impersonation. It works because an extra piece of information, not known by Mojang, has to be sent from the client during login to a server. Even if an attacker were able to break Minecraft's login system and join as a moderator, because the attacker's game would lack this piece of information, the server could detect impersonation.

.. note::
    Security breaches of this nature are less common these days.

How It Works
============

When a player connects to a server with an address, say ``play.example.com``, Minecraft will tell the server that the player connected with that address. A moderator could connect to a special, secret ``secretmod.play.example.com`` address, and the server could easily check whether the address used by the moderator started with ``secretmod``.

The host keys feature allows you to configure an an accepted address for certain players. If a player on the list connects with an incorrect address, they are kicked immediately.

Configuration
=============

Setup is done using the :doc:`config`::

   host-keys:
       your_username: bagels.play.example.com
       moderator1_name: manoverboard.play.example.com

.. tip::
    Host keys support uuids, and it's recommended you assign players' uuids to host keys instead of usernames in case they change their username.

DNS Configuration
~~~~~~~~~~~~~~~~~

To make this work, you have to make ``bagels.play.example.com`` and ``manoverboard.play.example.com`` point to your server. However, you should **not** add specific records for the domains that you use, because this allows attackers to easily figure out the secret domains.

Rather, it is recommended that you setup "wildcard addresses." An example of a wildcard address may be ``*.play.example.com``, which would mean that *any* prefix would work (``aa.play.example.com``, ``ab.play.example.com``, ``ac.play.example.com``, etc.).

.. tip:: If you don't have a domain name or can't set a wildcard address, you can use `sslip.io <https://sslip.io/>`_.

Alternatives
============

An alternative to host keys, although not provided by WorldGuard, is to use some sort of login command that takes a password.
