=============
Event Logging
=============

Event Funneling
===============

Bukkit notifies plugins of things that happen in the game via "events." There are many, many events, such as:

* Bucket fill
* Bucket empty
* Right click of an entity by a player
* Placement of a block by a player
* Digging of a block by a player
* Change of a block by an entity
* Piston push
* Push retract

However, it really comes down to three fundamental objects in Minecraft:

* Items
* Blocks
* Entities

You can do certain things with these objects:

* Place them
* Break them
* Interact with them
* Damage them

To simplify matters, WorldGuard funnels the Bukkit events into it a more fundamental set of events:

* Bucket fill → Interact with a block, Interact with an item
* Bucket empty → Interact with a block, Interact with an item
* Right click of an entity by a player → Interact with an entity
* Placement of a block by a player → Interact with a block
* Digging of a block by a player → Interact with a block
* Change of a block by an entity → Interact with a block
* Piston push → Interact with a block
* Push retract → Interact with a block

Parts of WorldGuard, like region protection, only then need to handle "interact with a block," "interact with an entity," and so on and determine whether a block or entity can be placed, broken, or interacted with.

Tracking Cause
==============

Another important facet of figuring out whether something should be permitted is to determine *who* is doing it.

What can make this complex is that causes are something indirect. For example, if a player shoots an arrow at another player, the immediate cause --- the arrow --- is not the true cause; rather, the player is. Another example is the act of placing a block of gravel in way so it falls down: the block that ends up on the ground is caused by (1) the falling gravel block entity, which was caused by the (2) original gravel block, which was originally caused by (3) a player.

Keep in mind that a cause could be a player, or it could be a block (pistons) or entity (Endermen or Creepers).

However, it is not always possible to track the true cause of an event. Sometimes WorldGuard must deal with untraceable chains of events.

Displaying Internal Events
==========================

There is only a handful of internal events that are used to funnel Bukkit's events. It's possible to output these events as they are created to the server log, which allows you to:

* Figure out which blacklist event is being triggered for certain actions
* See whether WorldGuard is handling an action at all
* Assist in development of WorldGuard to see how it handles certain events

To use this mode, specify ``-Dworldguard.debug.listener=true`` on the command line.

.. tip::
    This feature should be enabled on a private test server as it will emit many log entries on a busy server.

.. topic:: Example: Enabling the mode in a Batch file on Windows

    If your batch file reads like::

        @ECHO OFF
        SET BINDIR=%~dp0
        CD /D "%BINDIR%"
        "%ProgramFiles(x86)%\Java\jre7\bin\java.exe" -Xincgc -Xmx1G -jar craftbukkit.jar
        PAUSE

    You'd add ``-Dworldguard.debug.listener=true`` like so::

        @ECHO OFF
        SET BINDIR=%~dp0
        CD /D "%BINDIR%"
        "%ProgramFiles(x86)%\Java\jre7\bin\java.exe" -Dworldguard.debug.listener=true -Xincgc -Xmx1G -jar craftbukkit.jar
        PAUSE

    The option can go anywhere after "java.exe" but before "-jar". In this case, the example puts it right after java.exe.

Interpreting the Output
=======================

Let's take the example of placing gravel above a protected region. You'd see, in your server log or console, the following output::

    * USE   GRAVEL         [Player{sk89q}] @world :BlockPlaceEvent
    * PLACE GRAVEL @0,99,0 [Player{sk89q}] :BlockPlaceEvent
    * SPAWN FALLING_BLOCK  [Block{0,99,0}] @-0,99,0 :EntityChangeBlockEvent
    * PLACE GRAVEL @       [Block{0,99,0} | FallingSand] :EntityChangeBlockEvent [CANCELLED]
    * SPAWN DROPPED_ITEM   [Block{0,99,0} | FallingSand] @-0,0,0 :EntityChangeBlockEvent

.. note::
    The output has been been condensed and formatted for purposes of explanation.

The general syntax for each line is::

    ACTION TYPE/LOCATION [CAUSES] @LOOCATION :BUKKIT-EVENT [CANCELLED?]

A cancelled event is one that has been blocked.

Tracing Gravel Placement
~~~~~~~~~~~~~~~~~~~~~~~~

First, when the gravel block is used, it emits a use block event::

    * USE   GRAVEL         [Player{sk89q}] @world :BlockPlaceEvent

The cause is, of course, the player. If the use block event isn't cancelled (due to the blacklist or some WorldGuard feature), then it moves onto the actual placement::

    * PLACE GRAVEL @0,99,0 [Player{sk89q}] :BlockPlaceEvent

Because the gravel block is placed in the air, it must drop. Dropping blocks become entities (like skeletons or paintings), so this results in an entity spawn event with the cause being the placed gravel block. Note that WorldGuard does not attempt to track who placed the original gravel block.

.. code-block:: text

    * SPAWN FALLING_BLOCK  [Block{0,99,0}] @-0,99,0 :EntityChangeBlockEvent

When the (falling) gravel block hits the ground, it attempts to place a new gravel block on the ground and then removes itself, the falling block entity. The direct cause is the falling block entity, but the chain of events started with the placed block, and this is illustrated in the log entry::

    * PLACE GRAVEL @       [Block{0,99,0} | FallingSand] :EntityChangeBlockEvent [CANCELLED]

As you can see, the gravel placement event was blocked. This is because the gravel fell in a protected region and it originated from outside the region. Because WorldGuard doesn't want to cause players to lose their blocks accidentally on survival, an item is spawned instead, which results in another internal event::

    * SPAWN DROPPED_ITEM   [Block{0,99,0} | FallingSand] @-0,0,0 :EntityChangeBlockEvent

You can see that the cause still extends all the way back to the original placed block. Because item drops for non-members (the original gravel block is a non-member because it came from outside the region) are not disabled in the region, the item event is not cancelled and the item drop is made.