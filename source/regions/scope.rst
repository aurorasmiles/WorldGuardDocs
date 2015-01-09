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