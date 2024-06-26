===============
As a Dependency
===============

In order to use WorldGuard's API in your plugin, you need to add WorldGuard as a dependency. It may be a required or optional dependency.

API Versions
============

Within major versions (5.x, 6.x, 7.x), WorldGuard's API is extremely stable. Deprecation tends to occur over a period of at least 3 months, but more typically over at 6 months.

The currently supported version of the API is 7.x. Older versions of the API (and of Minecraft) no longer receive updates or support. You can use the version selector in the bottom-right-hand corner of this page to access older versions of the documentation,
but there may be changes or errors.

Build Script Dependency
=======================

If you compile your plugin or mod using something like `Maven <https://maven.apache.org/>`_ or `Gradle <https://www.gradle.org/>`_ (which you should!), you will need to add WorldGuard to the list of dependencies. You can find WorldGuard's artifacts in sk89q's Maven repository.

Note that some :doc:`objects that WorldGuard uses <native-objects>` come from WorldEdit. If you need to use those directly, you should also add WorldEdit as a compilation dependency (though it may be transitively provided depending on your build configuration).

* Maven repository: ``https://maven.enginehub.org/repo/``
* Artifacts: ``com.sk89q.worldguard:worldguard-bukkit:VERSION`` (where VERSION is your desired version of WorldGuard); note that this contains the api in the ``worldguard-core`` artifact.

The Maven repository should be online 24/7, and is one of Minecraft's longest running Maven repositories. If it isn't, see :doc:`../support`.

.. topic:: Example: Configuring a Maven pom.xml

    .. code-block:: xml

        <repositories>
            <repository>
                <id>sk89q-repo</id>
                <url>https://maven.enginehub.org/repo/</url>
            </repository>
        </repositories>

        <dependencies>
            <dependency>
                <groupId>com.sk89q.worldguard</groupId>
                <artifactId>worldguard-bukkit</artifactId>
                <version>VERSION</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

.. topic:: Example: Configuring a Gradle build script

    .. code-block:: groovy

        repositories {
            mavenCentral()
            maven { url "https://maven.enginehub.org/repo/" }
        }

        dependencies {
            compileOnly 'com.sk89q.worldguard:worldguard-bukkit:VERSION'
        }

Modifying plugin.yml
====================

First, it is important that you specify WorldGuard as a "depend" or "softdepend" in your plugin.yml file, so that Bukkit knows to make sure that WorldGuard is loaded before your plugin:

.. code-block:: yaml

    name: My Plugin
    version: 1.0
    description: This is my plugin!
    depend: [WorldGuard]

If you choose to make it a soft dependency instead, WorldGuard will load first if it's installed, but otherwise your plugin will still load without WorldGuard.

Accessing WorldGuard From Your Plugin
=====================================

Most WorldGuard APIs can be accessed using the ``WorldGuard.getInstance()`` method.

Adapting players can be done via the ``wrapPlayer`` method on ``WorldGuardPlugin.inst()``. See :doc:`native-objects` for details.

Note that you should keep :doc:`internal-apis` in mind while accessing parts of WorldGuard.

Classpath Woes with Soft Dependencies
=====================================

If you are using WorldGuard as hard dependency, you do not need to worry about WorldGuard classes potentially not existing at runtime. However, this is a concern if you are merely soft-depending on WorldGuard.

For example, if you tried to do:

.. code-block:: java

    class MyPlugin {
        public void onEnable() {
            ProtectedCuboidRegion region = new ProtectedCuboidRegion(...);
        }
    }

Your plugin would not even load because ``ProtectedCuboidRegion`` could not be found. The plugin manager wouldn't even get to be able to call ``onEnable()``. One way around this problem is to put the offending code in a different class entirely:

.. code-block:: java

    class RegionHolder {
        private final ProtectedCuboidRegion region;

        public RegionHolder() {
            region = new ProtectedCuboidRegion(...);
        }
    }

While you have the same problem here where you can't even create a ``RegionHolder``, at least you can catch the error from another class:

.. code-block:: java

    class MyPlugin {
        public void onEnable() {
            try {
                new RegionHolder();
            } catch (NoClassDefFoundError e) {
                // Do something here
            }
        }
    }

However, this issue does not apply in all cases. Chained method calls, starting with a static method call, can be used without causing the containing class from failing to load:

.. code-block:: java

    class MyPlugin {
        public void onEnable() {
            try {
                boolean result = SomeClass.staticMethod();
            } catch (NoClassDefFoundError e) {
                // Do something here
            }
        }
    }

It is therefore recommended that if you are using any sort of soft dependencies in your plugin, that you test your plugin without the soft dependencies installed.
