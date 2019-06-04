Plugin API
========================================

.. warning::

    Please know that the API is not stable at this time. API changes, additions and removals are
    only documented in the release changelogs, so make sure to read them. Things should stabilize once
    HPX enters beta status.

.. automodule:: happypanda.core.plugin_interface
   :members:
   :member-order: groupwise

Special Commands
----------------------------------------

Besides the command entries and events listed here, HPX also provides some plugin meta events that are specific for each plugin.
These events are emitted on plugin state changes. See :class:`.PluginState` for the different kinds of plugin states.

* ``init`` -- this event is emitted for a plugin after it has initialized and *after* all its dependencies has been initialized.
    It is therefore ideal to put the plugin's own initialization on this event.
* ``disable`` -- this event is emitted when a plugin has been disabled, either manually by the user or because of some other cause.
    The plugin should listen to this event to terminate any on-going process it has running.
* ``remove`` -- this event is emitted just before a plugin is to be removed. HPX will handle the deletion of the plugin folder.
    The ``disable`` event will always be emitted before this event. The plugin should listen to this event to remove any produced items
    that would still be lingering even after its folder has been deleted.
* ``before_update`` -- this event is emitted just before a plugin is updated.
    The plugin should listen to this event to perform actions before it is updated.
* ``after_update`` -- this event is emitted just after a plugin has been updated.
    The plugin should listen to this event to perform actions after it has been updated.
* ``config_update (dict)`` -- this event is emitted with a ``dict`` of configuration that has been updated, usually from a client.
    The plugin should listen to this event to be notified when its configuration has been updated.

These are special entries each plugin can attach to.

* ``message (dict) -> dict`` -- this entry is called is called with a message, usually from a client. A ``dict`` is expected to be returned.
    The plugin should attach to this entry when wanting to exchange messages with a client or another plugin.

Summary
----------------------------------------

.. exec::
    print(".. rubric:: Meta")
    autosummary_doc("happypanda.core.commands.meta_cmd")
    print(".. rubric:: I/O")
    autosummary_doc("happypanda.core.commands.io_cmd")
    print(".. rubric:: Database")
    autosummary_doc("happypanda.core.commands.database_cmd")
    print(".. rubric:: Item")
    autosummary_doc("happypanda.core.commands.item_cmd")
    print(".. rubric:: Gallery")
    autosummary_doc("happypanda.core.commands.gallery_cmd")
    print(".. rubric:: Item Data")
    autosummary_doc("happypanda.core.commands.itemdata_cmd")
    print(".. rubric:: Login")
    autosummary_doc("happypanda.core.commands.login_cmd")
    print(".. rubric:: Metadata")
    autosummary_doc("happypanda.core.commands.metadata_cmd")
    print(".. rubric:: Search")
    autosummary_doc("happypanda.core.commands.search_cmd")
    print(".. rubric:: Network")
    autosummary_doc("happypanda.core.commands.network_cmd")
    print(".. rubric:: Download")
    autosummary_doc("happypanda.core.commands.download_cmd")

Commands
----------------------------------------

.. exec::

    command_doc("happypanda.core.commands.meta_cmd")
    command_doc("happypanda.core.commands.io_cmd")
    command_doc("happypanda.core.commands.database_cmd")
    command_doc("happypanda.core.commands.item_cmd")
    command_doc("happypanda.core.commands.gallery_cmd")
    command_doc("happypanda.core.commands.itemdata_cmd")
    command_doc("happypanda.core.commands.login_cmd")
    command_doc("happypanda.core.commands.metadata_cmd")
    command_doc("happypanda.core.commands.search_cmd")
    command_doc("happypanda.core.commands.network_cmd")
    command_doc("happypanda.core.commands.download_cmd")
