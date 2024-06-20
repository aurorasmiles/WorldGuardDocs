================
Chest Protection
================

WorldGuard provides rudimentary self-serve chest protection that a player may utilize by placing a sign underneath their chest with specially formatted text. We generally advise new setups to **not** use this chest protection as it is not an actively updated part of WorldGuard. In addition, :doc:`regions <regions/index>` are a preferred method of defining ownership because it does not have problems involving blocks like hoppers.

.. warning::
    Chest protection in WorldGuard does not support UUIDs, so users will not be able to access chests if they change names.
    It also operates independently of regions and other protection.

    **It is highly recommended to not use this feature in WorldGuard. This feature will be removed from WorldGuard in a future version.**

    Use a dedicated plugin such as `LWC Extended <https://github.com/pop4959/LWCX>`_ if you need single-block protection.

Getting Started
===============

Chest protection must first be enabled in the :doc:`config`. With it disabled, chest protection will not be active but it will still not be possible to make "lock signs" (however, this is disabled in the configuration in modern WorldGuard versions).

A sign is protected as long as a *special sign is placed under a chest*. This sign:

* Must be a sign post
* Must be under a chest or inventory (for double chests, it only needs to be under one side)
* Have the text ``[Lock]`` on the first line
* Have the player's username on the second line
* Have up to two more other players' names on the next two lines

It is not possible to place someone else's name on the second line.
