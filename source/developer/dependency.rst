===============
As a Dependency
===============

In order to use WorldGuard's API in your plugin, you need to add WorldGuard as a dependency. It may be a required or optional dependency.

API Versions
============

Within major versions (5.x, 6.x, 7.x), WorldGuard's API is extremely stable. Deprecation tends to occur over a period of at least 3 months, but more typically over at 6 months.

Currently, we recommend that you use some recent version of 7.x. The API had a partial overhaul in 6.x to fix some long-standing problems without breaking a significant amount of backwards compatibility, with more changes occurring in 7.x.

Build Script Dependency
=======================

If you compile your plugin or mod using something like `Maven <https://maven.apache.org/>`_ or `Gradle <https://www.gradle.org/>`_ (which you should!), you will need to add WorldGuard to the list of dependencies. You can find WorldGuard's artifacts in sk89q's Maven repository.

* Maven repository: ``http://maven.sk89q.com/repo/``
* Artifacts: ``com.sk89q.worldguard:worldguard-core:VERSION`` and ``com.sk89q.worldguard:worldguard-legacy:VERSION`` (where VERSION is your desired version of WorldGuard)

The Maven repository should be online 24/7, and is one of Minecraft's longest running Maven repositories. If it isn't, see :doc:`../support`

.. topic:: Example: Configuring a Maven pom.xml

    .. code-block:: xml

        <repositories>
            <repository>
                <id>sk89q-repo</id>
                <url>http://maven.sk89q.com/repo/</url>
            </repository>
        </repositories>

        <dependencies>
            <dependency>
                <groupId>com.sk89q.worldguard</groupId>
                <artifactId>worldguard-core</artifactId>
                <version>VERSION</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

        <dependencies>
            <dependency>
                <groupId>com.sk89q.worldguard</groupId>
                <artifactId>worldguard-legacy</artifactId>
                <version>VERSION</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

.. topic:: Example: Configuring a Gradle build script

    .. code-block:: groovy

        repositories {
            mavenCentral()
            maven { url "http://maven.sk89q.com/repo/" }
        }

        dependencies {
            compileOnly 'com.sk89q.worldguard:worldguard-core:VERSION'
            compileOnly 'com.sk89q.worldguard:worldguard-legacy:VERSION'
        }

Modifying plugin.yml
====================

First, it is important that you specify WorldGuard as a "depend" or "softdepend" in your plugin.yml file so that Bukkit knows to make sure that WorldGuard is loaded before your plugin:

.. code-block:: yaml

    name: My Plugin
    version: 1.0
    description: This is my plugin!
    depend: [WorldGuard]

If you choose to make it a soft dependency instead, WorldGuard will load first if it's installed, but otherwise your plugin will still load without WorldGuard.

Accessing WorldGuard From Your Plugin
=====================================

Most WorldGuard APIs can be accessed using the ``WorldGuard.getInstance()`` method, however some that require a copy of the WorldGuardPlugin for your platform.

On Bukkit, this is done through the Plugin Manager.

Using the Plugin Manager
~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: java

    import com.sk89q.worldguard.bukkit.WorldGuardPlugin;
    import org.bukkit.plugin.Plugin;

    private WorldGuardPlugin getWorldGuard() {
        Plugin plugin = getServer().getPluginManager().getPlugin("WorldGuard");

        // WorldGuard may not be loaded
        if (plugin == null || !(plugin instanceof WorldGuardPlugin)) {
            return null; // Maybe you want throw an exception instead
        }

        return (WorldGuardPlugin) plugin;
    }

Classpath Woes with Soft Dependencies
=====================================

If you are using WorldGuard as hard dependency, you do not need to worry about WorldGuard clases potentially not existing at runtime. However, this is a concern if you are merely soft-depending on WorldGuard.

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

        public MyPlugin() {
            region = new ProtectedCuboidRegion(...);
        }
    }

While you have the same problem here where you can't even create a ``RegionHolder``, at least you can catch the error from another class:

.. code-block:: java

    class MyPlugin {
        public void onEnable() {
            try {
                new RegionHolder();
            } catch (NoClassDefFoundException e) {
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
            } catch (NoClassDefFoundException e) {
                // Do something here
            }
        }
    }

It is therefore recommended that if you are using any sort of soft dependencies in your plugin, that you test your plugin without the soft dependencies installed.
