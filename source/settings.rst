Settings
========================================

To use these settings, create a configuration file named `config.yaml`.

Most of these settings should also be configurable from a client.

The configuration file supports the expansion of environment variables in the form of `${NAME}`.
This means, for example, `key: ${PATH}` is read by HPX as `key: /your/actual/path`.

.. documentedlist::
    :listobject: happypanda.common.config.config_doc
    :header: Option Default Scope Description
    :spantolast:
    :descend:
