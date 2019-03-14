===================
Querying Protection
===================

To query protection, the ``Flags.BUILD`` flag can be tested using the methods explained in :doc:`flag-calculation`.

.. warning::
    Region queries do not check if a player has bypass permissions. Depending on your use case, you may want to check that separately.

    .. code-block:: java

        boolean canBypass = WorldGuard.getInstance().getPlatform().getSessionManager().hasBypass(player, player.getWorld());

.. topic:: Example: Querying build permission using the query cache

    .. code-block:: java

        LocalPlayer localPlayer = WorldGuardPlugin.inst().wrapPlayer(player);
        Location loc = new Location(world, 10, 64, 100);
        RegionContainer container = WorldGuard.getInstance().getPlatform().getRegionContainer();
        RegionQuery query = container.createQuery();

        if (!query.testState(loc, localPlayer, Flags.BUILD)) {
            // Can't build
        }