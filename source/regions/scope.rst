=================
What's Protected?
=================

Protection is extremely comprehensive:

* Block break and placement are prevented.
* Inventories (chests, furnaces) cannot be opened.
* TNT cannons are blocked (from outside a region).
* Gravel cannons are blocked (from outside a region).
* Tree growth grief (from outside a region) is prevented.
* Pistons are blocked (blocks cannot be pulled from a protected region nor pushed into).
* Sign change exploits are prevented.
* Crop trampling is prevented.
* Minecarts and boats are protected.
* Paintings and item frames are protected.
* Doors, buttons, pressure plates, and levers are protected.
* Animals are protected (leashing, taming, shearing).
* Unauthorized splash potions and projectiles are prevented.

In addition, WorldGuard supports mod-added custom blocks and entities (with some exceptions, explained later).

Water flow and lava flow protection is disabled by default, but it can be enabled in the :doc:`../config`.

.. tip:: If you find a way to bypass protection, please `file a ticket on the issue tracker <http://youtrack.sk89q.com/issues>`_.

Exceptions
==========

Some sensible defaults are used that permit certain activities from non-members:

* Item drops and item pickups are permitted.
* XP drops are permitted.

These exceptions can be removed globally or per-region by adjusting :doc:`flags`.

However, hoppers are one exception that cannot be changed. A hopper adjacent to a protected region can place items into a chest inside the region, so chests should not be placed on the edge of regions. This behavior is used because the preivous situation (where hoppers were protected) was a source of frequent confusion, though the exception may be removed in the future.

Additional exceptions can also be added per-region by adjusting flags, or globally by using the ``interaction-whitelist`` option in the :doc:`../config`.

.. topic:: Example: Allowing everyone to use doors, levels, buttons, and pressure plates even in protected regions

    The ``use`` flag concerns these types of items::

        /rg flag __global__ use allow

    See the :doc:`global-region` page for more information about ``__global__``.

.. topic:: Example: Blocking item drops and item pickups at spawn for all non-members of the spawn region

    There are different flags for item pickup and item drop::

        /rg flag spawn item-pickup -g nonmembers deny
        /rg flag spawn item-drop -g nonmembers deny

.. topic:: Example: Blocking XP drops for all non-members of all plot regions

    All the plot regions must be child of some template region. In this example, the template region is named ``mall_parent``::

        /rg flag mall_parent exp-drops -g nonmembers deny

    All plots would inherit this flag and "nonmembers" would apply to non-members of each plot region.

    This would **not work**::

        DOESN'T WORK: /rg flag __global__ exp-drops -g nonmembers deny

    This is because regions do not inherit from ``__global__``, so "nonmembers" on ``__global__`` implies non-members of **specifically** the global region.

Blocks and Entities
===================

One very important feature of how WorldGuard protects regions is how it handles blocks and entities. Players can obviously be either be added as a member of a protected region, but WorldGuard see blocks and entities the same way: they can either also be a member of a region.

However, entities and blocks cannot be explicitly added as member to a region. Instead, an entity or block is considered a member of a region if *it's within the region*. That's why, for example, a piston from outside a protected region cannot push into the region. It's because the piston is considered a non-member, and of course, someone or something that isn't a member cannot change blocks. On the other hand, a piston within a protected region can push blocks within the region because it's considered a member of the region.

WorldGuard also attempts to track the true *cause* of an event. For example, if a gravel block is placed above a protected region so that it falls into the protected region, WorldGuard considers the final placed block (that would exist after the gravel fell to the ground) to have been placed by the *falling block entity*, and the falling block entity to have been created by *the original gravel block high up*. (It's not as easy to determine who placed the original gravel block, however.) Because the original gravel block started outside the region, it cannot fall into the protected region because the original block was not a member.

.. hint::
    When the ``build`` flag is set to ``deny`` on a region, no one can build and pistons don't work. That's because the build flag will even prevent non-members from building, as as detailed above, pistons can be regular members of a region like any other player.

Mod Support
===========

.. sidebar:: What to watch out for
    
    * Blocks or entities that change other blocks and entities
    * Weapons, spells, or tools that shoot projectiles

WorldGuard can protect custom blocks and entities added by mods or plugins in most cases. Protection is primarily handled by preventing the right click or left click of unknown blocks and entities on the server, which is usually sufficient as these are the only ways to interact with most blocks and entities.

However, WorldGuard cannot protect blocks or entities that open GUIs on the client (which you can usually tell on a multiplayer server if the GUI opens much quicker than inventories) because they send data in a separate channel that WorldGuard is not aware of.

In addition, WorldGuard inherently cannot effectively control actions *done* on behalf of custom blocks or entities (such as a theoretical block mining drone), at least with high granularity. Base Minecraft itself does have blocks that can affect the world (like the piston), but the Bukkit team (or the maintainer of the server software that you use) properly notifies plugins when base game blocks change the world. However, mod-added blocks and entities rarely do so, so WorldGuard is unable to deal with those cases.

Some mods "fake" a player in order to perform some actions on behalf of blocks and entities. The convention for these fake players is have their name be of the format ``[ModName]``. However, this information is rarely useful beyond telling which mod is making the change so cannot be used by WorldGuard. For that reason, WorldGuard allows these fake players to bypass all protection. This behavior can be changed with ``fake-player-build-override`` in the :doc:`../config` but this effectively prevents blocks that change the world from working at all in a protected region if their mod uses a fake player.

Projectiles (and magic mods with projectile effects) are a major concern. This is because the mod likely does not notify plugins of effects of the projectile. WorldGuard has a workaround -- the ``emit-block-use-at-feet`` setting in the :doc:`../config` that lets you configure a list of item types -- that will *pretend* that listed items are trying to change the block at a player's feet. That effectively prevents a player *in* a protected region from firing his or her weapon because the player will be prevented from using the item in the region, but it does not prevent the player from firing into the protected region from *outside*.