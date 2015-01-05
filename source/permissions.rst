===========
Permissions
===========

In order for yourself, moderators, and players to use WorldGuard, you must provide the proper permissions. One way is to provide op to moderators and administrators (unless disabled in the :doc:`configuration <config>`), but providing the permission nodes on this page (through a permissions plugin) is the more flexible.

.. tip::
    You can give the ``worldguard.*`` permission to give yourself and other administrators full access to WorldGuard.

Moderator
=========

.. csv-table::
    :header: Permission, Explanation
    :widths: 15, 25

    worldguard.notify.*,"Receive notification with the 'notify' :doc:`blacklist <blacklist/index>` action and the 'notify' :doc:`region flags <regions/flags>`."
    worldguard.region.bypass.<world>.*,"Bypass region protection, which includes bypassing no-PvP flags."
    worldguard.chest-protection.override.*,"Bypass :doc:`chest-protection` and open protected chests."

.. warning::
    If you are op or have all permissions, you will implicitly have these protection bypass permissions and it will appear that protection does not work.

Commands
========

See the :doc:`commands` page for an explanation of some of these commands.

.. csv-table::
    :header: Permission, Explanation
    :widths: 15, 25

    worldguard.god,"Be able to use ``/god`` (and ``/ungod``)."
    worldguard.god.other,"Be able to use ``/god`` (and ``/ungod``) on others."
    worldguard.heal,"Be able to use ``/heal``."
    worldguard.heal.other,"Be able to use ``/heal`` on others."
    worldguard.slay,"Be able to use ``/slay``."
    worldguard.slay.other,"Be able to use ``/slay`` on others."
    worldguard.locate,"Be able to use ``/locate``."
    worldguard.stack,"Be able to use ``/stack``."
    worldguard.stack.illegitimate,"Be able to make stack sizes with ``/stack`` that exceed normal limits (i.e. 64 buckets in one stack)."
    worldguard.stack.damaged,"Be able to ``/stack`` items that should not be stacked because they use their value for storing other data."
    worldguard.fire-toggle.stop,"Be able to use ``/stopfire`` and ``/allowfire``."
    worldguard.halt-activity,"Be able to use ``/stoplag``."
    worldguard.reload,"Be able to use ``/wg reload``."
    worldguard.report,"Be able to use ``/wg report``."
    worldguard.report.pastebin,"Be able to have a report submitted to a pastebin service."
    worldguard.flushstates,"Be able to use ``/wg flushstates``."
    worldguard.running,"Be able to use ``/wg running``."
    worldguard.debug.event.*,"Be able to use the event testing commands under ``/wg debug``. Those commands are used to simulate an action so you can trace down, for example, which plugin is blocking player versus player combat. However, this essentially allows a moderator to impersonate another player."

Regions
=======

.. csv-table::
    :header: Permission, Explanation
    :widths: 15, 25

    worldguard.region.load.*,"Be able to use  ``/rg load``."
    worldguard.region.save.*,"Be able to use  ``/rg save``."
    worldguard.region.migratedb.*,"Be able to use  ``/rg migratedb``."
    worldguard.region.migrateuuid.*,"Be able to use  ``/rg migrateuuid``."
    worldguard.region.define.*,"Be able to use  ``/rg redefine``."
    worldguard.region.claim.*,"Be able to use  ``/rg claim``."
    worldguard.region.unlimited.*,"Bypass claiming limits."
    worldguard.region.list.*,"Be able to use  ``/rg list``."
    worldguard.region.list.own.*,"Be able to use  ``/rg list`` and have it show one own's regions."

Per-Region Commands
~~~~~~~~~~~~~~~~~~~

The following commands support a base permission (such as ``worldguard.region.redefine``), but also allow you to use specific permissions that only apply if a player is an owner or member of a region:

* ``worldguard.region.redefine.own.<region name>.*``
* ``worldguard.region.redefine.member.<region name>.*``
* ``worldguard.region.redefine.<region name>.*``

.. csv-table::
    :header: Permission, Explanation
    :widths: 15, 25

    worldguard.region.redefine.*,"Be able to use  ``/rg define``."
    worldguard.region.remove.*,"Be able to use  ``/rg remove``."
    worldguard.region.setpriority.*,"Be able to use  ``/rg setpriority``."
    worldguard.region.setparent.*,"Be able to use  ``/rg setparent``."
    worldguard.region.select.*,"Be able to use  ``/rg select``."
    worldguard.region.info.*,"Be able to use  ``/rg info``."
    worldguard.region.teleport.*,"Be able to use  ``/rg teleport``."
    worldguard.region.addmember.*,"Be able to use  ``/rg addmember``."
    worldguard.region.addowner.*,"Be able to use  ``/rg addowner``."
    worldguard.region.removemember.*,"Be able to use  ``/rg removemember``."
    worldguard.region.removeowner.*,"Be able to use  ``/rg removeowner``."

.. hint::
    If you wanted players to only look up information for regions that they are the owner or member of, you would provide the following permission::

        worldguard.region.info.own.*
    
    If you wanted to allow everyone to use ``/rg teleport`` for the region named "city," you would use::

        worldguard.region.teleport.city.*

Flag Command
~~~~~~~~~~~~

The ``/rg flag`` command has the basic permission::

    worldguard.region.flag.*

However, rather than providing that encompassing permission, you can provide the combination of the following two:

* To determine *which regions* can have their flag changed by the player, any of the following permissions can be given (and they work like the permissions in the previous section):

    * ``worldguard.region.flag.regions.own.<region name>.*``
    * ``worldguard.region.flag.regions.member.<region name>.*``
    * ``worldguard.region.flag.regions.<region name>.*``

* However, to determine *which types of flags* can be set by the player, permissions of the following pattern can be given:

    * ``worldguard.region.flag.flags.<flag name>.<region name>.*``

.. hint::
    If you wanted players to be able to (1) only set flags for their own regions and (2) only be able to set the ``use`` and ``chest-access`` flags, you would provide the following permissions::

        worldguard.region.flag.regions.own.*
        worldguard.region.flag.flags.use.*
        worldguard.region.flag.flags.chest-access.*