=========
Blacklist
=========

The blacklist allows an action (or several) to be performed whenever a player does something (place a block, use an item, etc.). Some example applications include:

* Blocking players from mining gold ore
* Notifying all administrators when a diamond block is found
* Telling the player a message when they place an enchantment table

An example of a blacklist file is:

.. code-block:: ini

    # Deny lava buckets
    [lavabucket]
    ignore-groups=admins,mods
    on-use=deny,tell
    message=Sorry, you can't use lava buckets!

    # Deny some ore
    [goldore,ironore]
    ignore-groups=admins
    on-break=deny,tell,notify

    # No TNT!
    [tnt]
    ignore-groups=admins
    on-place=deny,notify,kick

Blacklist Files
===============

For every world, you will find per-world configuration files:

* ``worlds/world/blacklist.txt``
* ``worlds/world_nether/blacklist.txt``
* ``worlds/mining_world/blacklist.txt``

.. hint::
    Unfortunately, you cannot share one blacklist file for several worlds from within WorldGuard. However, you may be able to create a "symlink" on your operaitng system to have two files refer to the same one.

The Format
==========

A blacklist file consists of several sections in the format of:

.. code-block:: ini

    [a list of items/blocks to match]
    event to watch=what to do
    event to watch=what to do
    event to watch=what to do
    option=value

Lines starting with ``#`` are ignored by WorldGuard. You can use this to place notes for yourself.

Matching Syntax
~~~~~~~~~~~~~~~

Items and blocks can be matched by either their (legacy) ID number or by specifying one of the entries on `Bukkit's material list <http://jd.bukkit.org/rb/apidocs/org/bukkit/Material.html>`_. Multiple items and blocks can be matched by separating each entry by a comma::
    
    [wood,brick,glass]

Data values, while being phased out by Minecraft, can be specified in the blacklist by appending ``:#`` as so::

    [wood:0]

Multiple data values can be matched by separating each one with a semicolon (;)::

    [wood:0;2;3]

In addition, ranges of data values are supported (they are inclusive)::

    [wood:2-3]

Lastly, both greater than (``>=``) and less than (``<=``) are accepted::

    [wood:>=2,<=3]

.. tip::
    You can combine all of these matchers as seen below::

        [wood:0;>=2,grass:1-2]

Events
======

Now that you have specified the items or blocks to watch out for, you must specify the situations in which you would like to catch, as well as the action to perform.

The available events are listed below.

.. csv-table::
    :header: Event, Explanation
    :widths: 10, 30

    on-break,When the matched block is being mined
    on-destroy-with,When the matched item or block is being held by the player during mining
    on-place,When the matched block is being placed
    on-use,When an item in the player's inventory is used
    on-interact,"When a player interacts (i.e. right click) with the matched block (door, lever, etc.)"
    on-drop,When the player drops the matched item
    on-acquire,When the player acquires the matched item
    on-dispense,When a dispenser dispenses the matched item

When specifying an event, a list of "actions" must be specified afterwards, as illustrated below::

    [enchantment_table]
    on-place=deny,tell
    on-drop=notify

Available Actions
~~~~~~~~~~~~~~~~~

Multiple actions can be specified for each event.

.. csv-table::
    :header: Action, Explanation
    :widths: 7, 30

    deny,Denies the action (only if the blacklist **not** in "whitelist mode" (explained later))
    allow,Permits the action (only if the blacklist is in "whiteist mode")
    notify,Notify admins with the ``worldguard.notify`` permission
    log,"Log to console, file, or database"
    tell,Tell the player that the action was not allowed
    kick,Kick the player
    ban,Ban the player

Options
=======

Options are specified in the same place as events, as illustrated below::

    [enchantment_table]
    on-place=deny,tell
    message=Sorry, you can't use enchantment tables!

In this case, ``message`` is an option that overrides the message used by the "tell" action.

.. csv-table::
    :header: Option, Explanation
    :widths: 10, 30

    ignore-groups,Comma-separated list of permission groups to not affect
    ignore-perms,Comma-separated list of permissions to not affect -- make up your very own permissions!
    comment,Message for yourself that is printed with ``log`` and ``notify`` actions (to override the default message)
    message,Message to show the user (to override the default message). Put %s in the message to have it be replaced with the item name (in English)

Examples
========

.. topic:: Block all bucket use

    .. code-block:: ini

        [lavabucket,waterbucket,bucket]
        on-use=deny,tell

.. topic:: Kick players using TNT and notify administrators

    .. code-block:: ini

        [tnt]
        ignore-groups=admins
        on-place=deny,notify,kick

.. topic:: Allow only the people in the "obsidian" group and administrators to use obsidian

    .. code-block:: ini

        [obsidian]
        ignore-groups=admins,obsidian
        on-place=deny,tell
        on-break=deny,tell

Logging
=======

With the ``log`` action, messages can be logged to several places:

* Console
* File
* Database

These log targets can be enabled or disabled in the :doc:`../config`. Multiple log targets can be enabled at one time. By default, only the console log target is enabled.

Console Logging
~~~~~~~~~~~~~~~

Console logging merely prints the log entries to the server console.

File Logging
~~~~~~~~~~~~

File logging writes the log entries to a file. In the :doc:`../config`, the path for the log file can be specified with special variables in it (like today's date), so you can have logs automatically rotated every day.

The following variables can be used:

* %Y the year (YYYY)
* %m the month (MM)
* %d the day (DD)
* %W the week of the year (00-52)
* %H 24-hour time (HH)
* %h 12-hour time (HH)
* %i the minute (mm)
* %s the second (ss)
* %u the user's name
* %% translates to a single percent sign "%"

Database Logging
~~~~~~~~~~~~~~~~

WorldGuard can write the log entries to a MySQL database. However, you will have to create the database and table yourself first. The SQL needed to create the table is provided below:

.. code-block:: sql

    CREATE TABLE IF NOT EXISTS `blacklist_events` (
     `id` int(11) NOT NULL AUTO_INCREMENT,
     `world` varchar(10) NOT NULL,
     `event` varchar(25) NOT NULL,
     `player` varchar(16) NOT NULL,
     `x` int(11) NOT NULL,
     `y` int(11) NOT NULL,
     `z` int(11) NOT NULL,
     `item` int(11) NOT NULL,
     `time` int(11) NOT NULL,
     `comment` varchar(255) DEFAULT NULL,
     PRIMARY KEY (`id`)
    );