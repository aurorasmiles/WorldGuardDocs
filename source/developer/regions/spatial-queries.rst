===============
Spatial Queries
===============

WorldGuard optimizes for two types of spatial queries:

* Finding all regions containing a point
* Finding regions overlapping another region

Spatial queries can be performed using an instance of a :doc:`RegionManager <managers>`, but they can also be performed through a special query cache. In either case, a returned ``ApplicableRegionSet`` object will be returned that contains a list of regions, as well as additional methods to perform :doc:`flag-calculation`.

It's also possible to, given a list of regions, to create your own ``ApplicableRegionSet``. This is useful if you want to :doc:`test the value of flags <flag-calculation>` and you already have a list of regions to test with (and the regions do not even need to overlap).

Getting an ApplicableRegionSet
==============================

Through the Query Cache
~~~~~~~~~~~~~~~~~~~~~~~

The query cache stores the last query result for at most one or two seconds, which speeds up repeated lookups for the same a block, a common scenario during event handling. However, the query cache only supports the first type of query, point location. To utilize the cache, a new ``RegionQuery`` instance can be obtained from a ``RegionContainer`` (see :doc:`../native-objects` for converting from Bukkit locations):

.. code-block:: java

    RegionQuery query = container.createQuery();
    ApplicableRegionSet set = query.getApplicableRegions(loc);

.. topic:: Example: Getting regions at (10, 64, 100)

    .. code-block:: java

        Location loc = new com.sk89q.worldedit.util.Location(world, 10, 64, 100); // can also be adapted from Bukkit, as mentioned above
        RegionContainer container = WorldGuard.getInstance().getPlatform().getRegionContainer();
        RegionQuery query = container.createQuery();
        ApplicableRegionSet set = query.getApplicableRegions(loc);

One particular feature of the cache is that it will **return virtual results** if region protection is disabled or region data failed to load:

* If region protection is disabled, an instance of ``PermissiveRegionSet`` will be returned, which will contain no regions and permit any action.
* If region data failed to load, an instance of ``FailedLoadRegionSet`` will be returned, which will contain no regions, deny every action, and also implicitly provide values for some flags (such as ``deny-message`` to inform players of the error).

In either case, ``set.isVirtual()`` will return ``true`` if the result is virtual.

Through a RegionManager
~~~~~~~~~~~~~~~~~~~~~~~

Given an `RegionManager`, ``getApplicableRegions(Vector)`` can be used to perform a point location query (see :doc:`../native-objects` for converting from Bukkit locations).

.. code-block:: java

    BlockVector3 position = BlockVector3.at(20, 10, 4);
    ApplicableRegionSet set = regions.getApplicableRegions(position);

.. topic:: Example: Getting regions at (10, 64, 100)

    .. code-block:: java

        Location loc = new com.sk89q.worldedit.util.Location(world, 10, 64, 100); // can also be adapted from Bukkit, as mentioned above
        RegionContainer container = WorldGuard.getInstance().getPlatform().getRegionContainer();
        RegionManager regions = container.get(world);
        // Check to make sure that "regions" is not null
        ApplicableRegionSet set = regions.getApplicableRegions(loc.toVector().toBlockPoint());

If the goal is to find a list of regions that overlap another, use ``getApplicableRegions(ProtectedRegion)`` on the manager. Because shape and the region are currently the same objects (coupled together), you have to use a dummy ID:

.. code-block:: java

    BlockVector3 min = BlockVector3.at(0, 0, 0);
    BlockVector3 max = BlockVector3.at(10, 10, 10);
    ProtectedRegion test = new ProtectedCuboidRegion("dummy", min, max);
    ApplicableRegionSet set = regions.getApplicableRegions(test);

Constructing Manually
~~~~~~~~~~~~~~~~~~~~~

``RegionResultSet`` takes a ``List<ProtectedRegion>`` and an optional global region.

The provided regions do not need to overlap.

.. code-block:: java

    List<ProtectedRegion> regions = Lists.newArrayList();
    regions.add(spawn);
    regions.add(mall);
    regions.add(pub);

    ApplicableRegionSet set = new RegionResultSet(regions, null); // No global region

.. warning::
    Your list of regions may be re-ordered in-place. After you have given a list of regions to the instance, it should no longer be used.

Using a ApplicableRegionSet
===========================

If your interest is in getting the list of regions, ``ApplicableRegionSet`` implements ``Iterable<ProtectedRegion>`` so you can loop over it. However, looping over regions to do explicit checks may not account for things such as priorities, flag defaults, inheritance, and the global region. It is always preferable to do protection or flag checks via the dedicated query methods, as covered by :doc:`protection-query` and :doc:`flag-calculation`.

.. code-block:: java

    for (ProtectedRegion region : set) {
        // Do something with each region
    }

.. topic:: Example: Getting a list of regions

    .. code-block:: java

        List<ProtectedRegion> region = new ArrayList<>();
        set.forEach(region::add);
