============
Region Flags
============

Regions can have flags set upon it. Some uses of flags include:

* Blocking player versus combat with the ``pvp`` flag
* Denying entry to a region using the ``entry`` flag
* Disabling the melting of snow using the ``snow-melt`` flag
* Blocking players within the region from receiving chat using the ``receive-chat`` flag
* Halting the growth of vines by using the ``vine-growth`` flag

A region can have several different flags set at one time, although a certain flag can only have one value at a time. Flags are defined using the ``/region flag`` command, as illustrated below for the "spawn" region and "hospital" regions::

    /region flag spawn pvp deny
    /region flag spawn greeting Welcome to spawn!
    /region flag hospital heal-amount 2

Remove a flag by not specifying a value::

    /region flag spawn pvp

List flags by using the "info" command::

    /region info spawn

Region Groups
=============

Sometimes, it may be desired for a flag to only apply to a certain group of players rather than everyone that should enter the region. This can be achieved by specifying an additional "region group" when defining the flag, of which there are several options:

* all (everyone)
* members
* owners
* nonmembers
* nonowners

The group can be specified using the ``-g`` marker as illustrated below::

    /region flag spawn pvp deny -g nonmembers

It is **not** possible to set the same flag to different values for more than one group on the same region. If you need that functionality, consider making several regions.

.. tip::
    In previous versions of WorldGuard, if the region group was not set, some flags would default to "nonmembers" while others would default to "everyone." Starting with WorldGuard 6, it is *always* "everyone" by default. 

Types of Flags
==============

Each flag is of a certain type that determines what kind of values it may take. For example, the *heal-amount* flag is an numeric flag, so you can only set numeric values for it.

.. csv-table::
    :header: Type, Kind of values
    :widths: 5, 30

    state, "Either 'allow' or 'deny' (explaind later)"
    string, "Any form of text"
    integer, "An number that does not have decimals (5, but not 5.5)"
    double, "Numbers that may have decimals (5, 5.5, 2.425)"
    location, "A location in a world"
    boolean, "True or false"
    set, "A list of unique entries"

Internally, there are more types, but it should generally not be of concern.

Conflicting Flags
=================

Sometimes, a certain location may have multiple overlapping regions with different values for the same flag. The following rules are used to determine which values are selected:

* Regions will inherit the value of a flag from its parent, **if** the region did not have the flag set. 
* Higher priority regions will override lower-priority regions.
* The global region is considered like any other region, except it is at the lowest possible priority.

However, it is still possible for there to be conflicting flag values even after that process. Imagine two different regions at the same priority, for example. At that point, the value of the flag is decided differently depending on the type of flag:

* For state flags, if ``deny`` is present, the result is ``deny``. Otherwise, if ``allow`` is present, then the final value is ``allow``.
* For other flags, the result is not defined. For that reason, do not, for example, set two different greeting messages in the same area with the same priority.

If a flag is not defined at all, then the default behavior is whichever is most sensible. For example, if "item pickup" is not defined, WorldGuard defaults to allowing it.

Flag Listing
============

Flags are broken down into categories below.

Overrides
~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    passthrough,state,Make the region a non-protection region (explained below)
    build,state,Protection override (explained below)

.. warning::
    If you want to protect a region, do **not** set either of the flags above. Protection is on *by default*. The situations where you may want to adjust those flags is explained below.

    * **When would you use PASSTHROUGH?** Let's say you want to make a "hospital" area that auto-heals players. Since the hospital is in the marketplace, where you have already given some builders permission, you *don't* want the hospital region to affect protection and deny the builders. Therefore, you would use ``/region flag hospital passthrough allow`` to make the hospital a non-protection region. (Note: Setting the flag to "deny" does nothing.)
    * **When would you use BUILD?** Let's say you want to make a "mine" area that overlaps your "spawn" region and permits *everyone* to mine or place blocks. You would use ``/region flag mine build allow``. On the other hand, if you have "admin" region that overlaps various other regions and you want to block building for *everyone*, use "deny". The purpose of the ``build`` flag is to **override** overlapping regions.

Protection-Related
~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    block-break,state,Whether blocks can be mined
    block-place,state,Whether blocks can be placed
    use,state,"Whether doors, levers, etc. (but not inventories) can be used"
    interact,state,"Whether entities and blocks can be interacted with"
    pvp,state,Whether player versus player combat is permitted
    sleep,state,Whether sleeping in a bed is permitted
    tnt,state,Whether TNT detonation or damage is permitted
    chest-access,state,Whether inventories can be accessed
    vehicle-place,state,"Whether vehicles (boats, minecarts) can be placed"
    vehicle-destroy,state,Whether vehicles can be destroyed
    lighter,state,Whether flint and steel can be used
    ride,state,Whether vehicles (including animals) can be mounted

.. tip::
    If the ``build`` flag is set to ``allow``, then these flags will do nothing because the build flag explicitly allows any interaction. If the build flag is set to ``deny``, then these flags can override the build flag. Between these flags, however, (for example, both `use` and `interact` overlap in regards to a door), then ``deny`` on one flag always overrides ``allow`` on another flag.

Mobs, Fire, and Explosions
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    creeper-explosion,state,Whether creepers can do damage
    enderdragon-block-damage,state,Whether enderdragons can do block damage
    ghast-fireball,state,Whether ghast fireballs can do damage
    other-explosion,state,Whether explosions can do damage
    fire-spread,state,Whether fire can spread
    enderman-grief,state,Whether endermen will grief
    mob-damage,state,Whether mobs can hurt players
    mob-spawning,state,Whether mobs can spawn
    deny-spawn,set of entity types,A list of entity types that cannot spawn
    entity-painting-destroy,state,Whether non-player entities can destroy paintings
    entity-item-frame-destroy,state,Whether non-player entities can destroy item frames

.. topic:: Example: Preventing sheep and cows from spawning at spawn

    The entity types must be specified::

        /rg flag spawn deny-spawn cow,pig

Natural Events
~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    lava-fire,state,Whether lava can start fires
    lightning,state,Whether lightning can strike
    water-flow,state,Whether water can flow
    lava-flow,state,Whether lava can flow
    snow-fall,state,Whether snow will fall
    snow-melt,state,Whether snow will melt
    ice-form,state,Whether ice will form
    ice-melt,state,Whether ice will melt
    mushroom-growth,state,Whether mushrooms will grow
    leaf-decay,state,Whether leaves will decay
    grass-growth,state,Whether grass will grow
    mycelium-spread,state,Whether mycelium will spread
    vine-growth,state,Whether vines will grow
    soil-dry,state,Whether soil will dry

.. warning::
    The ``fire-spread``, ``water-flow`` and ``liquid-flow`` flags require that the "high frequency flags" option be enabled in the :doc:`configuration <../config>`. This is because these events can be very frequent, requiring more region lookups, and potentially slowing down your server (or at least warming the server room a bit more).

Map Making
~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    item-pickup,state,Whether items can be picked up
    item-drop,state,Whether items can be dropped
    exp-drops,state,Whether XP drops are permitted
    deny-message,string,The message issued to players that are denied an action
    entry,state,Whether players can enter the region
    exit,state,Whether players can exit the region
    greeting,string,The message that appears upon entering the region
    farewell,string,The message that appears upon leaving the region
    enderpearl,state,Whether enderpearls can be used
    invincible,state,Whether players are invincible
    game-mode,gamemode,"The gamemode (survival, creative, adventure) that will be applied to players that enter the region"
    heal-delay,integer,The number of seconds between heals (if ``heal-amount`` is set)
    heal-amount,integer,The amount of half hearts to heal (...or hurt if negative) the player at the rate of ``heal-delay``
    heal-min-health,double,The minimum number of half hearts that damage (via ``heal-amount``) will not exceed
    heal-max-health,double,The maximum number of half hearts that healing (via ``heal-amount``) will not exceed
    feed-delay,integer,"See equivalent heal flag, except this is for food"
    feed-amount,integer,"See equivalent heal flag, except this is for food"
    feed-min-hunger,integer,"See equivalent heal flag, except this is for food"
    feed-max-hunger,integer,"See equivalent heal flag, except this is for food"
    teleport,location,The location to teleport to when the ``/rg teleport`` command is used within the region
    spawn,location,The location to teleport to when a player dies within the region
    blocked-cmds,set of strings,A list of commands to block
    allowed-cmds,set of strings,A list of commands to permit

.. warning::
    The healing, feeding, greeting, and farewell message flags require that the "use player move event" option **not** be disabled in the :doc:`configuration <../config>`.

.. topic:: Example: Changing the message players receive when an action they try is blocked
    
    Set the ``deny-message`` flag::

        /rg flag spawn deny-message Sorry! You are at spawn. If you want to find a place to call home, use the rail station to leave spawn.

.. topic:: Example: Blocking the "/tp" and "/teleport" commands at spawn
    
    The commands in question can be blocked with::

        /rg flag spawn blocked-cmds /tp,/teleport

.. topic:: Example: Preventing non-members of a "secret_club" region from entering it
    
    The key is to set the region group to "nonmembers"::

        /rg flag secret_club entry -g nonmembers deny

.. topic:: Example: In a "hospital" region, heal players one heart every second up to half their health bar
    
    Without any buffs, the player's maximum health is 20, so 10 is half of that::

        /rg flag hospital heal-amount 2
        /rg flag hospital heal-max-health 10

Miscellaneous
~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    pistons,state,Whether pistons can be used
    send-chat,state,Whether players can send chat
    receive-chat,state,Whether players can receive chat
    potion-splash,state,Whether potions can have splash effects
    notify-enter,boolean,Whether players with the ``worldguard.notify`` permission are notified when another player enters the region
    notify-leave,boolean,Whether players with the ``worldguard.notify`` permission are notified when another player leaves the region

Unused
~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    allow-shop,state,"Not used by WorldGuard at this time, but third-party plugins may use it"
    buyable,boolean,"Not used by WorldGuard at this time, but third-party plugins may use it"
    price,double,"Not used by WorldGuard at this time, but third-party plugins may use it"