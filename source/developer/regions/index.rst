====================
Working with Regions
====================

Region data can be accessed from other plugins using the region data API. A reference to WorldGuard should be available, which is explained in :doc:`../dependency`.

.. topic:: Regions and thread safety

    The region API is completely thread safe. However, collections (lists, sets, maps) returned from various parts of the region API should generally not be modified. While many of these objects have been made immutable, there is some legacy code remaining.

Sections
========

Region data can be accessed via the :doc:`RegionContainer <managers>`. Every region is an instance of :doc:`ProtectedRegion <protected-region>`. If you want to check build permissions, check flags, find overlapping regions, or see what regions contain a point, you must perform a :doc:`spatial query <spatial-queries>`. Once you've gotten a spatial query result, you can :doc:`calculate flags <flag-calculation>` or :doc:`check build permissions <protection-query>`.

.. toctree::
    :maxdepth: 3
    :titlesonly:

    managers
    protected-region
    custom-flags
    spatial-queries
    flag-calculation
    protection-query
    events