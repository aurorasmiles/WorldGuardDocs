Internal APIs
=============

Some of WorldGuard is not considered public API and may be changed at any moment without warning. Usage of this
code is not considered proper, and will receive no support.

The precise definition of internal API is anything not accessible according to standard Java access rules,
and any of the following types:

- Anything in the platform implementations (i.e. ``worldguard-bukkit``, as opposed to ``-core``) with the exception of the ``wrapPlayer`` method on ``WorldGuardPlugin``
- Anything in the following packages:
   - ``com.sk89q.worldguard.internal``
   - ``com.sk89q.worldguard.commands``
   - ``com.sk89q.worldguard.util``
- Anything explicitly marked as internal (in `JavaDocs <https://enginehub.org/documentation>`_).
