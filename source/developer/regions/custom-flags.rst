=================================
Custom Flags and Session Handlers
=================================

As of WorldGuard 6.2, custom flags and session handlers are supported. This allows third-party plugins to create their own flags for WorldGuard regions.

Registering New Flags
=====================

Make sure that you have added WorldGuard as a :doc:`../dependency` first. The order of registration is very important, so WorldGuard must load before your plugin for this to work.

Flags have to be registered with WorldGuard's ``FlagRegistry`` with the ``register(Flag<?> flag)`` method. The parameter should be an instance of any flag object, whether you use one of the default types or your own type.

Registering has to be done after WorldGuard is Loaded (in the Bukkit plugin lifecycle) but before it is Enabled. Thus, it is highly recommended that you register when your plugin loads. After WorldGuard is enabled, the FlagRegistry is locked and no new flags can be registered.

.. topic:: Example: Registering a custom flag

    .. code-block:: java

        // StateFlag with the name "my-custom-flag", which defaults to "allow"
        public static final Flag MY_CUSTOM_FLAG = new StateFlag("my-custom-flag", true);

        public boolean onLoad() {
            // ... do your own plugin things, etc

            FlagRegistry registry = WorldGuard.getInstance().getFlagRegistry();
            try {
                // register our flag with the registry
                registry.register(MY_CUSTOM_FLAG);
            } catch (FlagConflictException e) {
                // some other plugin registered a flag by the same name already.
                // you may want to re-register with a different name, but this
                // could cause issues with saved flags in region files. it's better
                // to print a message to let the server admin know of the conflict
            }

Once your flag is registered, WorldGuard will take care of loading and saving it from the region database, allowing users to set it via the ``/rg flag`` commands, and so on. Even if your plugin is removed from the server, WorldGuard will keep the flag saved to any regions it was set on, but it will be rendered inert until your plugin is loaded again.

Using Session Handlers
======================

In general, your flags will be used in event handlers by :doc:`querying values <flag-calculation>`. However, some flags may be designed for specific tasks such as:

* Running some method periodically on all players in regions with the given flag (e.g. heal flag)
* Responding to a player entering or leaving a region with the flag set (e.g. greeting flag)

Custom handlers can be registered any time after WorldGuard enables. A handler is instantiated by a factory method for each session (player) when it is created. There are also some methods in the Handler class which your custom handler has to override. If you are using a handler for the second type of behavior, it is recommended that you extend the FlagValueChangeHandler class instead, which handles a lot of the region-border-crossing logic for you.

.. topic:: Example: Creating and registering a custom handler

    .. code-block:: java

        public class CustomHandler extends FlagValueChangeHandler<State> {
            public static final Factory FACTORY = new Factor();
            public static class Factory extends Handler.Factory<CustomHandler> {
                @Override
                public CustomHandler create(Session session) {
                    // create an instance of a handler for the particular session
                    // if you need to pass certain variables based on, for example, the player
                    // whose session this is, do it here
                    return new CustomHandler(session);
                }
            }
            // construct with your desired flag to track changes
            public CustomHandler(Session session) {
                super(session, MyPlugin.MY_CUSTOM_FLAG);
            }
            // ... override handler methods here
        }


    .. code-block:: java

        SessionManager sessionManager = WorldGuard.getInstance().getSessionManager();
         // second param allows for ordering of handlers - see the JavaDocs
        sessionManager.registerHandler(MyCustomHandler.FACTORY, null);

.. tip::
     WorldGuard's inbuilt handlers can be found in the `com.sk89q.worldguard.session.handler <https://github.com/sk89q/WorldGuard/tree/master/worldguard-legacy/src/main/java/com/sk89q/worldguard/session/handler>`_ package. These should serve as good examples for implementing your own handlers.

.. note::
    Not all of WorldGuard's Session uses are flag-related. WorldGuard also uses Sessions to manage god mode, for example.