===================
From Bukkit Objects
===================

Players
=======

Due to legacy reasons, WorldGuard uses its own internal player object called ``LocalPlayer``. A Bukkit ``Player`` can be converted to a ``LocalPlayer`` using ``wrapPlayer(Player)`` on ``WorldGuardPlugin``.

.. code-block:: java

    getWorldGuard().wrapPlayer(player);

Vectors
=======

Many operations need WorldEdit vectors. Bukkit ``Locations`` can be converted to ``Vectors`` using methods on ``BukkitUtil``.

.. code-block:: java

    BukkitUtil.toVector(location);