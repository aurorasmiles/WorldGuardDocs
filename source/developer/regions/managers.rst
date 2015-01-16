========
Managers
========

Region Container
================

Region data can be accessed via the ``RegionContainer`` object:

.. code-block:: java

    RegionContainer container = getWorldGuard().getRegionContainer();

Every world has separate lists of regions. To access the regions for a particular world, the container has a ``getWorld(World)`` method:

.. code-block:: java

    RegionManager regions = container.get(world);

.. warning::
    The returned value **may be null** if region support is disabled or region data failed to load. WorldGuard may periodically attempt to load the data again.

Region Managers
===============

To get a particular region by ID:

.. code-block:: java

    ProtectedRegion region = regions.getRegion("spawn");

In addition:

* Get an immutable map of all regions: ``regions.getRegions()``
* Test whether a region exists given an identifier: ``regions.hasRegion(String)``
* Get the number of regions: ``regions.size()``

.. topic:: Example: Getting a region named "spawn"

    .. code-block:: java

        RegionContainer container = getWorldGuard().getRegionContainer();
        RegionManager regions = container.get(world);
        if (regions != null) {
            return regions.getRegion("spawn");
        } else {
            // The world has no region support or region data failed to load
        }

Creating Regions
~~~~~~~~~~~~~~~~

Once you've created an instance of a :doc:`ProtectedRegion <protected-region>`, use ``addRegion(ProtectedRegion)`` on a manager:

.. code-block:: java

    RegionContainer container = getWorldGuard().getRegionContainer();
    RegionManager regions = container.get(world);
    regions.addRegion(region);

Parent regions are automatically added. If there are existing regions with equivalent IDs, then the new regions will replace the previous regions..

Removing Regions
~~~~~~~~~~~~~~~~

Regions can be removed by identifier using ``regions.removeRegion(String, RemovalStrategy)``. The removal strategy parameter determines what to do regarding regions that inherit from the removed region.

.. code-block:: java

    regions.removeRegion("mall", RemovalStrategy.UNSET_PARENT_IN_CHILDREN);

Saving Changes
~~~~~~~~~~~~~~

Region data is automatically saved after a short delay if changes are made, so there is **no need to explicitly save**.

If you wish to explicitly save, you can call either:

* ``save()``
* ``saveChanges()``

The calls can be used from any thread, but they will block until completion (or error).

Reloading Changes
~~~~~~~~~~~~~~~~~

To reload changes from disk, ``load()`` can be used. It can be called from any thread, but it will block until completion (or error).