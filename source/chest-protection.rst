================
Chest Protection
================

WorldGuard provides rudimentary self-serve chest protection that a player may utilize by placing a sign underneath their chest with specially formatted text. We generally advise new setups to **not** use this chest protection as it is not an actively updated part of WorldGuard. In addition, :doc:`regions <regions/index>` are a preferred method of defining ownership because it does not have problems involving blocks like hoppers.

.. tip::
    If you are interested in single-block chest protection, we recommend utilizing third-party chest protection plugins like `Lockette <http://dev.bukkit.org/bukkit-plugins/lockette/>`_ or `LWC <http://dev.bukkit.org/bukkit-plugins/lwc/>`_.

Getting Started
===============

Chest protection must first be enabled in the :doc:`config`. With it disabled, chest protection will not be active but it will still not be possible to make "lock signs" (however, this can also be disabled in the configuration).

A sign is protected as long as a *special sign is placed under a chest*. This sign:

* Must be a sign post
* Must be under a chest or inventory (for double chests, it only needs to be under one side)
* Have the text ``[Lock]`` on the first line
* Have the player's username on the second line
* Have up to two more other players' names on the next two lines

It is not possible to place someone else's name on the second line.

.. note::
    Chest protection in WorldGuard does not support UUIDs.

.. warning::
    Due to changes in Minecraft 1.8, updating from an older version of Minecraft may destroy all lock signs. In fact, it will destroy all signs that have square brackets (``[`` and ``]``) around text.