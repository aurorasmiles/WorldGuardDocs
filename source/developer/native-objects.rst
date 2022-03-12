===================
From Bukkit Objects
===================

Players
=======

For legacy reasons and future cross-platform support, WorldGuard uses its own internal player object called ``LocalPlayer``. A Bukkit ``Player`` can be converted to a ``LocalPlayer`` using ``wrapPlayer(Player)`` on ``WorldGuardPlugin``.

.. code-block:: java

    WorldGuardPlugin.inst().wrapPlayer(player);

Other Types
===========

Many operations need WorldEdit locations, worlds, and so on. Bukkit's ``Location``, ``World``, and other objects can be converted to WorldEdit objects using methods in ``com.sk89q.worldedit.bukkit.BukkitAdapter``.

.. code-block:: java

    BukkitAdapter.adapt(location);
    BukkitAdapter.adapt(world);
