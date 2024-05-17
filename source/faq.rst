================
Common Questions
================

.. contents::
    :local:
    :backlinks: none

.. tip::
    Be sure to also to check out the :doc:`regions/common-scenarios` page for solutions to common issues regarding region protection.

General
=======

Why don't any commands work?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If no commands work, it may because WorldGuard failed to start:

* Make sure that you are running Bukkit (or a server software that supports Bukkit). Use the ``version`` command in console or in-game and make sure that the response has "Bukkit" or "CraftBukkit" in it.
* Make sure that you have `WorldEdit <https://www.enginehub.org/worldedit>`_ installed.
* Make sure that you have the proper version of WorldGuard for your version of Minecraft.

If those solutions do not help you, you will need to look through your startup log:

* If you use a game server host, use its log viewer.
* You can also open up "latest.log" in the logs folder of your server directory. (On older versions of Minecraft, the log file was "server.log" in the root directory.)

If you are unable to discover the problem from reading the server log, you can :doc:`ask for help or submit a bug report <../support>`.

How old is WorldGuard?
~~~~~~~~~~~~~~~~~~~~~~

WorldGuard began in November 2010 for the "hMod" modding platform by `sk89q <http://www.sk89q.com>`_. Later on, WorldGuard was ported to Bukkit.

Who works on WorldGuard?
~~~~~~~~~~~~~~~~~~~~~~~~

WorldGuard has been developed by many people, and large portions of WorldGuard include contributed code. The list of top contributors can be `found on GitHub <https://github.com/EngineHub/WorldGuard/graphs/contributors>`_.

Can't Build
===========

.. _cannot-build:

Why can't players break or place blocks?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::
    A new WorldGuard installation has most features disabled, so it is unlikely to be caused by WorldGuard in such scenarios.

One of the easiest ways to identify the reason is to identify the message that you get when you are unable to build. WorldGuard usually uses dark red or crimson-colored messages, or a message like "Hey! Sorry, but you can't _____ here." If you get no message, it is probably not WorldGuard.

If it does not immediately appear to be WorldGuard,

* Make sure that Minecraft's "spawn protection" is not on. Spawn protection prevents anyone from making any changes within a certain radius of a world's spawn point. To adjust spawn protection, change the ``spawn-protection`` setting in the server.properties file to ``0``.
* Make sure that you are not testing in adventure mode.
* Update your version of CraftBukkit/Spigot/Paper.

If those steps do not help, there's a simple command in WorldGuard that can simulate an action on behalf of a player and then report the plugin that blocked the action. Use the "testbreak" and "testplace" commands described on the :doc:`commands` page to identify the plugin.

If the cause is WorldGuard:

* Use the :doc:`regions/wand` to see if any regions protect the blocks in question. If some do, but you do not know why, see :ref:`troubleshooting-region-protection`
* Check to see whether :doc:`build-perms` is enabled.
* Check to see whether any other :doc:`config` options are enabled that would prevent the action.
* If you cannot figure out the cause, :doc:`look into getting help <support>`.

If the command lists a different plugin:

* See if you need to give extra permissions or change any configuration for the other plugin.

If the test command lists no plugins, make sure that you first followed the steps above (check spawn protection, etc.), then :doc:`look into getting help <support>`.

.. _troubleshooting-region-protection:

After setting up some regions, why can no one build?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use the :doc:`regions/wand` and right click an affected block to list the available regions. Use the ``/rg info`` to lookup information about each listed region.

* Make sure to verify membership of all the regions.
* Make sure that the ``build`` flag is not set to ``deny``.

Is there only a global region?

* Make sure that the :doc:`regions/global-region` does not have ``build`` set to deny.
* Make sure that the :doc:`regions/global-region` does not have ``passthrough`` set to deny.
* Make sure that the :doc:`regions/global-region` does not have members or owners.

Building Not Blocked
====================

Why is protection not working? Players receive NO message.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* You have op or full permissions, so you override protection.
* You have set the ``build`` flag on a region to "allow."
* You have set the ``passthrough`` flag on a region to "allow."
* You have set some other relevant flag (``pvp``, ``ride``, etc.) on the region to "allow."
* The item in question is from a mod or a third-party plugin (see :doc:`regions/scope`).
* WorldGuard does not yet protect that particular thing that you are trying to do. This is not the case for simple block place or break. Please make sure that you are using the latest version of WorldGuard, and if it's still a problem, :doc:`file a bug report <support>`.
* There is a bug in your version of CraftBukkit/Spigot/Paper.

.. _can-build-with-message:

If you still cannot figure out the cause, :doc:`look into getting help <support>`.

Why is protection not working? Players DO receive a message.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If WorldGuard is blocking an action, it's still possible for a different plugin to *unblock* the action. However, because WorldGuard has already sent the "you can't build" message, the player still receives it.

You can use the "testbreak" and "testplace" commands described in :doc:`commands` to identify the causing plugin. If you see any plugin above WorldGuard on the list with "ALLOW" next to its line, then that plugin is the cause.

Another possibility is that your version of CraftBukkit/Spigot/Paper has a bug. Be sure to use the latest available version. If you still cannot figure out the cause, :doc:`look into getting help <support>`.

Region Protection
=================

Why do pistons not work?
~~~~~~~~~~~~~~~~~~~~~~~~

You probably set the ``build`` flag to ``deny`` when you probably should not have. Check out the :doc:`regions/common-scenarios` page for more information.

How do I do __________ with region protection?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Check out the :doc:`regions/common-scenarios` page.