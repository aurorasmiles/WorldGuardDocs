=======
Regions
=======

Every region object is a subclass of the ``ProtectedRegion`` class, and there are several subclasses:

* ``ProtectedCuboidRegion``
* ``ProtectedPolygonalRegion``
* ``GlobalProtectedRegion``

Each region object stores:

* An ID (immutable)
* Its priority
* Its (optional) parent
* A list of members
* A list of owners
* A list of region flags
* A boolean that automatically tracks whether the region has been modified

Vectors are used for referring to locations --- these vector objects are from WorldEdit (see :doc:`../native-objects` for converting from Bukkit locations).

.. topic:: Example: Changing the priority of a region

    .. code-block:: java

        region.setPriority(100);

.. topic:: Example: Setting the parent of a region

    .. code-block:: java

        mall.setParent(null); // No parent
        plot.setParent(mall);

    If you attempt to create a scenario where there is circular inheritance, an exception will be thrown.

.. topic:: Example: Getting polygon vertices out of a region

    .. code-block:: java

        if (region instanceof ProtectedPolygonalRegion) {
            ProtectedPolygonalRegion polygon = (ProtectedPolygonalRegion) region;
            List<BlockVector2D> points = polygon.getPoints();
        }

Domains
=======

Owners and members (``region.getOwners()`` and ``region.getMembers()``) are separate instances of ``DefaultDomain``, which holds player names, player UUIDs, and permission groups.

.. topic:: Example: Adding members to a region

    .. code-block:: java

        DefaultDomain members = region.getMembers();
        members.addPlayer("sk89q");
        members.addPlayer(UUID.fromString("0ea8eca3-dbf6-47cc-9d1a-c64551ca975c"));
        members.addGroup("admins");

.. topic:: Example: Converting names to UUIDs in the background

    If you need to convert player names into UUIDs, you should try to do it in the background if possible so that you do not pause the server or game.

    You can use WorldGuard's ``DomainInputResolver`` class to help you do that. It implements ``Callable<DefaultDomain>`` and will return a ``DefaultDomain`` object that can be added to an existing domain. It takes input in the format of the member management commands. This is illustrated below.

    .. code-block:: java

        // Google's Guava library provides useful concurrency classes.
        // The following executor would be re-used in your plugin.
        ListeningExecutorService executor =
                MoreExecutors.listeningDecorator(Executors.newCachedThreadPool());

        String[] input = new String[] { "sk89q", "g:admins" };
        ProfileService profiles = getWorldGuard().getProfileService();
        DomainInputResolver resolver = new DomainInputResolver(profiles, input);
        resolver.setLocatorPolicy(UserLocatorPolicy.UUID_AND_NAME);
        ListenableFuture<DefaultDomain> future = executor.submit(resolver);

        // Add a callback using Guava
        Futures.addCallback(future, new FutureCallback<DefaultDomain>() {
            @Override
            public void onSuccess(DefaultDomain result) {
                region.getOwners().addAll(result);
            }

            @Override
            public void onFailure(Throwable throwable) {
                // Do something about the error
            }
        });

    It is highly recommended that you inform the user if the UUID lookup does not complete instantly.

Flags
=====

Flags can be read by calling ``getFlag(Flag flag)``. You can find static ``Flag`` objects on ``DefaultFlag``:

.. code-block:: java

    DefaultFlag.BUILD
    DefaultFlag.PVP
    DefaultFlag.LEAF_DECAY
    DefaultFlag.LIGHTNING

The returned value will be of the data type of the flag. For example, if you were to use ``DefaultFlag.GREET_MESSAGE``, which is a ``StringFlag``, a ``String`` will be returned.

.. topic:: Example: Getting the greeting message

    .. code-block:: java

        String message = region.getFlag(DefaultFlag.GREET_MESSAGE);
        player.sendMessage(message);

If the given flag is not set, ``null`` will be returned.

Setting Flags
~~~~~~~~~~~~~

Flags can be set using ``setFlag(Flag flag, ? value)``. The value that you use must be of the type that the flag allows. For example, if the flag is a ``StringFlag``, you can only set a ``String``:

.. code-block:: java

    region.setFlag(DefaultFlag.GREET_MESSAGE, "Hi there!");

Flags can be removed by using ``null`` for the value.

Region groups can be set by calling ``getRegionGroupFlag()`` on a flag to get its region group flag:

.. code-block:: java

    RegionGroupFlag flag = DefaultFlag.PVP.getRegionGroupFlag();

.. topic:: Example: Setting the region group of the ``use`` flag:

    .. code-block:: java

        region.setFlag(DefaultFlag.USE, StateFlag.State.ALLOW);
        region.setFlag(DefaultFlag.USE.getRegionGroupFlag(), RegionGroup.MEMBERS);

Custom Flags
~~~~~~~~~~~~

At this time, custom flags are not officially supported. There are, however, unofficial, third-party implementations of custom flags for WorldGuard.

Creating Regions
================

``ProtectedRegion`` is an abstract class, so you must use one of the subclasses. For example, you might use a ``ProtectedCuboidRegion``.

In every case, a region ID must be passed for the region. Valid regions must match the regular expression ``^[A-Za-z0-9_,'\-\+/]{1,}`` --- that is, region IDs are only valid if they contain A-Z, a-z, 0-9, underscores, commas, single quotation marks, dashes, pluses, or forward slashes. IDs are case in-sensitive. The validity of an ID can be verified using ``ProtectedRegion.isValidId(String)``.

To save a created region, see :doc:`managers`.

Cuboids
~~~~~~~

To create a new cuboid region, two opposite corners are required. Any two opposite two corners are acceptable.

.. code-block:: java

    BlockVector min = new BlockVector(-10, 5, -4);
    BlockVector max = new BlockVector(5, -8, 10);
    ProtectedRegion region = new ProtectedCuboidRegion("spawn", min, max);

2D Polygon
~~~~~~~~~~

Only 2D polygons are supported. These are polygons that have been extended vertically, which means that a minimum Y and a maximum Y are needed to create a 2D polygon region. A minimum of three points is required to create a valid 2D polygonal region.

.. code-block:: java

    List<BlockVector2D> points = Lists.newArrayList(); // Call from Guava
    points.add(new BlockVector2D(3, 4, 5));
    points.add(new BlockVector2D(0, 0, 0));
    points.add(new BlockVector2D(19, 3, 4));
    int minY = 0;
    int maxY = 54;
    ProtectedRegion region = new ProtectedPolygonalRegion("spawn", points, minY, maxY);

The points can be in any order.

Global Regions
~~~~~~~~~~~~~~

Not to be confused with :doc:`../../regions/global-region`, global regions have no physical area. They do not contain any points. The global region *does* use the ``GlobalProtectedRegion``, but other regions can also utilize this class (users can create them by using the ``-g`` switch on ``/rg define``).

.. code-block:: java

    ProtectedRegion region = new GlobalProtectedRegion("template");

Spatial Queries
===============

There are a few methods to perform spatial queries on a specific region.

.. hint::
    If you are interested in performing spatial queries on all regions, see :doc:`spatial-queries`.

Testing Point Containment
~~~~~~~~~~~~~~~~~~~~~~~~~

``boolean contains(Vector)`` can be used to test whether a region contains a particular point.

.. topic:: Example: Seeing whether a region contains (20, 0, 30)

    .. code-block:: java

        region.contains(new Vector(20, 0, 30));

Finding Intersecting Regions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``getIntersectingRegions(Collection<ProtectedRegion>)`` method call can be used to return a list of intersecting regions. These regions do **not** have to be fully contained.

.. topic:: Example: Seeing which regions overlap with spawn

    .. code-block:: java

        List<ProtectedRegion> candidates = Lists.newArrayList();
        candidates.add(mall);
        candidates.add(hospital);

        List<ProtectedRegion> overlapping = spawn.getIntersectingRegions(candidates);

Dirty Flag
==========

Whenever changes are made to a region object, a "dirty" flag (not to be confused with region flags) is set on the region. This can be tested with ``isDirty()``, and it is used by region managers to know which regions need to be saved.