Installing
#######################################

1. Download the archive/installer matching your OS from `Github Releases <https://github.com/happypandax/server/releases>`_
2. Extract the archive or run the installer
3. Continue to :ref:`Using HappyPanda X`

HPX is self-contained.
Everything HPX creates and modifies, it does so in its own folder.
This means it's portable.
The installer is just there to ease the install.
On Windows it creates 3 shortcuts to ``happypandax_gui.exe``.
They are placed in your ``Desktop``, ``Start Menu`` and ``Start Menu/Programs`` folders.

If you're on Windows < 10, you need to install ``Microsoft Visual C++ Redistributable 2015`` to run HPX.
The setup file is included in the archive, look for ``vc_redist.x86.exe``.
If you're using the installer, this setup file will automatically be run for you.

.. Warning::
    HPX requires sufficient permissions to read and write from it's own folder to be able to run properly. This is required for the auto-update process to work.
    On Windows it means you shouldn't install HPX somewhere like ``C:\Program Files (x86)``. If you still choose to do so just remember to run HPX as admin.
    (Not recommended)

Docker
======================================

It is also an option to use HPX with Docker.
If you're new to Docker or don't know what Docker is then I cannot recommend you go through this route.
This is mostly for advanced users or users who experience issues with installing HPX normally.

Before you can use HPX with Docker, make sure it's `installed. <https://www.docker.com/products/docker-desktop>`_

Now, there's two ways to do this. You can either choose to use HPX with Docker alone or also complement it with Docker Compose (recommended).
The main benefit of using it with Docker Compose is easier configuration.
If you want to use HPX with Docker Compose, make sure it's `installed <https://docs.docker.com/compose/install/#install-compose>`_ (if you installed Docker from the link above, then you should already have Docker Compose installed)

Using with Docker
-------------------------------------

Start by copying the appropriate commands for your OS over onto a file.
Then edit the variables so that they point to the appropriate folders and ports.

Finally, copy your edit of commands over to a command-line and execute them.

**Linux & Mac OS:**

------------

::

    HPX_PORT=7007
    HPX_WEBPORT=7008
    HPX_TORRENTPORT=7006
    HPX_DATA="/path/to/desired/hpx/datadir"
    HPX_CONTENT="/path/to/desired/hpx/contentdir"
    RUN_IN_BACKGROUND=true
    AUTOSTART_ON_BOOT=always
    docker run -p $HPX_PORT:7007 -p $HPX_WEBPORT:7008 -p $HPX_TORRENTPORT:7006 --name happypandax --restart AUTOSTART_ON_BOOT --volume=$HPX_DATA:/data --volume=$HPX_CONTENT:/content -d=$RUN_IN_BACKGROUND twiddly/happypandax

**Windows:**

------------

**NOTE**: This is intended for ``cmd.exe`` not PowerShell

::

    set HPX_PORT=7007
    set HPX_WEBPORT=7008
    set HPX_TORRENTPORT=7006
    set HPX_DATA="C:\path\to\desired\hpx\datadir"
    set HPX_CONTENT="C:\path\to\desired\hpx\content"
    set RUN_IN_BACKGROUND=false
    set AUTOSTART_ON_BOOT=always
    docker run -p %HPX_PORT%:7007 -p %HPX_WEBPORT%:7008 -p %HPX_TORRENTPORT%:7006 --name happypandax --restart %AUTOSTART_ON_BOOT% --volume=%HPX_DATA%:/data --volume=%HPX_CONTENT%:/content -d=%RUN_IN_BACKGROUND% twiddly/happypandax

The configuration file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The default configuration file is set to:

::

    import:
        skip_existing_galleries: true
        transparent_nested_folders: true
    watch:
        enable: true
        dirs:
          - ${HPX_CONTENT}
        options:
          ${HPX_CONTENT}:
            import.scan_on_startup: true

The config file supports environment variables, so ``${HPX_CONTENT}`` will automatically expand to your specified content folder *inside docker*.
Another useful variable is ``${HPX_DATA}`` pointing to the data folder *inside docker*.
Note that these environment variables are pre-defined by the image inside docker. This means you can't just define new variables from outside and expect the docker instance to see them.

To modify this configuration, create a file named ``config.yaml`` inside your ``HPX_DATA`` folder with these contents as a starting point.

About the commands
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:``HPX_PORT`` & ``HPX_WEBPORT`` & ``HPX_TORRENTPORT``:
    Your desired ports to connect to the HPX servers and clients with on your machine

:``HPX_DATA``:
    A folder on your machine where HPX should save its data, this includes config files, databases, backups, etc.

:``HPX_CONTENT``:
    A folder for where manga and doujins are stored, you usually set this to your pre-existing folder where those things are stored.

:``RUN_IN_BACKGROUND``:
    If ``false`` the docker container will run in the foreground, meaning that closing the command-line window will also exit the docker container.

:``AUTOSTART_ON_BOOT``:
    Automatically start the HPX container whenever Docker starts, available values are ``always``, ``no``, ``on-failure`` and ``unless-stopped``, read more about it `here. <https://docs.docker.com/config/containers/start-containers-automatically/>`_

:``docker run ...``:
    You can read more about what the different docker flags means `here. <https://docs.docker.com/engine/reference/run/>`_

The docker image is set so that subsequent arguments after the docker image name in the ``docker run`` command are passed to happypandax,
meaning this:

``docker run <yadda.. yadda..> twiddly/happypandax --help``

is the same as running the happypandax executable directly with ``happypanda.exe --help`` (the ``<yadda.. yadda..>`` is passed to docker, not hpx).

The ``<yadda.. yadda..>`` part are all those arguments from the command above just shortened for brevity. Make sure to include those arguments, or else... **or else Docker wouldn't know where to store data and IT WILL BE LOST**.

This means that you could generate an example config file like this:

``docker run <yadda.. yadda..> twiddly/happypandax --gen-config``

The config file will be generated into the folder you specified with ``HPX_DATA``.

You can also make user accounts this way:

``docker run <yadda.. yadda..> twiddly/happypandax user create -t "admin" -u "twiddly" -p "twiddly123"``

To stop the container, execute:

``docker stop happypandax``

And to start it again,

``docker start happypandax``

Remove it with:

``docker rm happypandax``

Updating
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Update to the latest docker image by running:

``docker pull twiddly/happypandax:latest``

For a specific version:

``docker pull twiddly/happypandax:0.11.0``

Then remove your current container

``docker rm happypandax``

Now, rerun the ``docker run <yadda.. yadda..> twiddly/happypandax`` command from before.

Updating will not reset your data as long as you keep mounting the same volumes.

Now, continue to :ref:`Using HappyPanda X` or read on if you intend to use Docker Compose.

Using with Docker Compose
-------------------------------------

Using HPX with Docker Compose lets us avoid those complicated-looking commands.
And while we are at it, we might as well set up HPX to use postgresql with docker as well.

Start by creating a folder for where you want your HPX config and data to be. You can just name this folder ``HappyPanda X``.

In that folder, create a folder named ``data`` and a file named ``docker-compose.yml`` with these contents:

::

    version: '3'

    services:
      happypandax:
        image: twiddly/happypandax:latest
        # start this container automatically on boot
        restart: always
        ports:
          - "7006:7006"
          - "7007:7007"
          - "7008:7008"
        volumes:
          - ./data:/data
          - ./content:/content
        links:
          - db

      db:
        image: postgres:latest
        restart: always
        ports:
          - 7005:5432
        environment:
          POSTGRES_USER: 'happypandax'
          POSTGRES_PASSWORD: 'postgres'
        volumes:
          - database:/var/lib/postgresql/data
        logging:
          driver: none

    volumes:
      database:
        driver: local

Change the ports and folder mappings as desired. In particular you should change ``./content:/content`` to ``<where your manga folder is located>:/content``,
or else the expected content folder will be a folder named ``content`` in the ``HappyPanda X`` folder.

You can also change the ports, if you wish to do so, do it like this: ``<your custom port>:7008``

**Notice that the port to postgres is set to 7005:5432**, meaning unless you change it, you will have to use port ``7005`` to connect to it with *from outside*.

Now, create a ``config.yaml`` file with these contents and save it inside the ``data`` folder
(unless you changed it then this folder is expected to be named ``data`` inside the ``HappyPanda X`` folder, create it if it doesn't exist):

::

    import:
        skip_existing_galleries: true
        transparent_nested_folders: true
    watch:
        enable: true
        dirs:
          - ${HPX_CONTENT}
        options:
          ${HPX_CONTENT}:
            import.scan_on_startup: true

    db:
        dialect: postgres
        username: happypandax
        password: postgres
        host: db
        port: 5432
        name: happypanda


The config file supports environment variables, so ``${HPX_CONTENT}`` will automatically expand to your specified content folder *inside docker*.
Another useful variable is ``${HPX_DATA}`` pointing to the data folder *inside dicker*.
Note that these environment variables are pre-defined by the image inside docker. This means you can't just define new variables from outside and expect the docker instance to see them.

Notice that the postgres host is the name of the database service as defined in ``docker-compose.yml`` to be named ``db``.
And the port is set to ``5432`` because that's the port postgres listens on *inside the container*, but to access it from outside docker, you will need to use ``7005`` or whatever was defined.

This config does a few things. It sets HPX to use the dockerized postgres service and also puts the content folder under watch so that new items in it will be added to HPX automatically.

Lastly, if you don't wish to use a postgres backend with docker, just remove this part from ``docker-compose.yml``:

::

        links:
          - db

      db:
        image: postgres:latest
        restart: always
        ports:
          - 7005:5432
        environment:
          POSTGRES_USER: 'happypandax'
          POSTGRES_PASSWORD: 'postgres'
        volumes:
          - database:/var/lib/postgresql/data


    volumes:
      database:
        driver: local

Make sure to edit your ``config.yaml`` to reflect this change.

With all that done, we can now start up everything. Run this command to start everything:

``docker-compose up``

If everything was successful, you should be able to open your browser and access HPX on ``localhost:7008`` (replace ``7008`` with whatever you set that port mapping to).

.. Note::
    In some cases, the postgres instance won't get ready in time for when HPX starts, resulting in HPX saying it can't connect to the database.
    If that happens, just run: ``docker-compose up --force-recreate``

To run in the background, supply the ``-d`` flag:

``docker-compose up -d``

You can stop everything with:

``docker-compose down``

Read more about what commands are available `here. <https://docs.docker.com/compose/reference/>`_

In the same vein as with ``docker run``, you can also pass commands to HPX in the same way, like:

``docker-compose run happypandax --help``

To create a new user:

``docker-compose run happypandax user create -t "admin" -u "twiddly" -p "twiddly123"``

Updating
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Update to the latest docker image by running:

``docker-compose pull``

And then:

``docker-compose up --force-recreate``
