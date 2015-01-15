============
Build Checks
============

To facilitate simple build checks, WorldGuard has a few helper methods on :doc:`WorldGuardPlugin <dependency>`:

.. code-block:: java

    boolean canBuild(Player player, Location loc);
    boolean canBuild(Player player, Block block);

This simple check merely checks whether the player can build but does not currently check any flags.

.. topic:: Example: Checking if a player can build

    .. code-block:: java

        getWorldGuardPlugin().canBuild(player, block.getRelative(0, -1, 0));