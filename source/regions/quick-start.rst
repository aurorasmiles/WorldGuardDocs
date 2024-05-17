===========
Quick Start
===========

Selecting Areas
===============

To create a region, you need to tell WorldGuard the physical area. `WorldEdit <https://www.enginehub.org/worldedit>`_ is used for this purpose.

.. topic:: New to WorldEdit?

    You can read `WorldEdit's quick start tutorial <https://worldedit.rtfd.io/en/latest/quickstart/>`_ to learn how to select areas with WorldEdit.

Regions can take the shape of:

* Cuboids (i.e. a cube or a box)
* 2D polygons with heights

If you try to use an unsupported shape such as a cylinder, WorldGuard will inform you of this.

Basic Commands
==============

Creating Regions
~~~~~~~~~~~~~~~~

All regions must have a name, and names must be unique. Names are case-insensitive. Create a region with ``/region define``::

    /region define town

Rather than using ``/region``, you can also use ``/rg`` as a shortcut::

    /rg define town

The newly created region **automatically prevents building** within it. To let specific players build, add members or *owners* to the region. The main reason why you'd have separate owners or members is because you can set :doc:`permissions to use commands separately for owners and members <../permissions>`.

Either players or permission groups can be a member or owner. To specify a permission group, prefix the name of the group with ``g:``. The relevant add and remove commands are illustrated below::

    /rg addmember town Notch sk89q g:builders
    /rg addowner town sk89q
    /rg removemember town g:builders
    /rg removeowner town sk89q

Full documentation for these commands can be found on the :doc:`commands` page.

Lastly, when you create a region, you can also conveniently specify the owners::

    /rg define town Notch sk89q g:builders

Region data is periodically saved, so you do not need to use a save command.

.. topic:: Example: Creating a spawn region where only the build team can build
    
    1. Select the area of spawn.
    2. Create a region named "spawn"::

        /rg define spawn

    3. Add the build team as a member::

        /rg addmember spawn g:builders

.. hint::
    Your changes to regions will automatically be saved after a small delay. However, region data can be force saved using ``/rg save``. All saves are done in the background and will not pause the server.

Other Commands
~~~~~~~~~~~~~~

You can remove regions::

    /rg remove town

You can list information about a region::

    /rg info town

You can view a list of regions::

    /rg list

You can change the shape of an existing region::

    /rg redefine town

For more commands, see the :doc:`region commands <commands>` page.

Properties of Regions
=====================

Handling Overlap
~~~~~~~~~~~~~~~~

Regions can overlap in WorldGuard, which provides a lot of flexibility but can be a source of complexity.

* When there are overlapping regions, a player must have permission to build in **all** overlapping regions.
* If you want a region to override another, use :doc:`priorities and region inheritance <priorities>`.
* If you want a region to override all others in regards to whether players can build, use the ``build`` :doc:`region flag <flags>`.
* If a region you made isn't supposed to protect its area, use the ``passthrough`` :doc:`region flag <flags>`.

.. topic:: Example: Creating a free-for-all mining zone that overlaps spawn
    
    1. Select the area of mining zone.
    2. Create a region named "mine"::

        /rg define mine

    3. Set the ``build`` flag to ``allow``, which permits building for all players and overrides spawn because spawn doesn't have the build flag explicitly set::

        /rg flag mine build allow

Flags
~~~~~

Each region can have extra settings applied to it called flags. For example, PvP can be blocked using the "pvp" flag::

    /rg flag town pvp deny

Read the :doc:`flags` guide for more information.

.. topic:: Example: Creating an arena at spawn where fighters can battle but cannot break blocks, while builders can still build within the arena
    
    1. Select the area of arena.
    2. Create a region named "arena"::

        /rg define arena

    3. Set the ``pvp`` flag to ``allow``::

        /rg flag arena pvp allow

    4. Builders cannot build in the arena because they aren't a member of the arena region, even if they are a member of the spawn region (if you have been following these examples). However, you created the arena region to set the PvP flag, not to protect the area, so set the ``passthrough`` flag to ``allow`` to make it transparent to build checks::

        /rg flag arena passthrough allow

Common Scenarios
================

If you're wondering how to do something (like allow the usage of doors, levers, pressure plates, etc.), check out the :doc:`common-scenarios` page.
