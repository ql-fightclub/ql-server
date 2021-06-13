# Quake Live Server Standards - Free For All

This Docker image contains the standard configuration for Free For All as defined by [Quake Live Server Standards](https://github.com/quakelive-server-standards).

## How to use this image

Use this image in conjunction with the Docker-based Quake Live Server Standards [server framework](https://github.com/quakelive-server-standards/server-framework). There it is part of a Docker Compose environment which also adds the needed Redis database.

## Directory structure and files

- `/home/steam/ql/baseq3/access.txt` - User Steam ids which are admin, mod or banned
- `/home/steam/ql/baseq3/server.cfg` - [Standard server configuration](https://github.com/quakelive-server-standards/server-framework/blob/master/configs/standard/server.cfg)
- `/home/steam/ql/baseq3/mappool.txt` - [Standard Free For All map pool](https://github.com/quakelive-server-standards/server-framework/blob/master/mappools/standard/ffa/mappool.txt)
- `/home/steam/ql/baseq3/workshop.txt`- [Standard Free For All Steam workshop items](https://github.com/quakelive-server-standards/server-framework/blob/master/workshop/standard/ffa/workshop.txt)
- `/home/steam/ql/baseq3/scripts/ffa.factories` - [Standard Free For All game type definitions](https://github.com/quakelive-server-standards/server-framework/blob/master/factories/standard/ffa/ffa.factories)
- `/home/steam/ql/minqlx-plugins` - [Standard Free For All minqlx plugins](https://github.com/quakelive-server-standards/server-framework/tree/master/minqlx-plugins/standard/ffa)

## Environment variables

The following environment variables are supported. They are named exactly like their cvar counterpart but in capital letters.

Beware that those are translated into command line parameters given to the Quake Live dedicated server executable. They have a lower priority than any cvar set in the `server.cfg` or `autoexec.cfg`.

### `NET_PORT`

This is the UDP port of the actual game itself. The default value is 27960.

### `ZMQ_RCON_PORT`

This is the port for ZeroMQ rcon connections, a remote console into a server. It uses the TCP protocol. If no value is given, it is set to `NET_PORT` + 1000 which is the standard recommended by id software. The [Quake Live Server Standards standard](https://github.com/quakelive-server-standards/server-framework/blob/master/configs/standard/README.md) defines that the rcon feature has to be enabled.

### `ZMQ_STATS_PORT`

This is the port for ZeroMQ published stats. The Quake Live server sends out specific events which can be used to track the activity on the server. It uses the TCP protocol. If no value is given, it is set to `NET_PORT` which is the standard recommended by id software. The [Quake Live Server Standards standard](https://github.com/quakelive-server-standards/server-framework/blob/master/configs/standard/README.md) defines that the stats feature has to be enabled.

### `SV_HOSTNAME`

The name which is displayed in the Quake Live server browser. The default value is `Quake Live Server Standards - Free For All`.

### `SV_TAGS`

The tags with which a server can be found in the Quake Live server browser. The default value is `ffa`.

### `SERVERSTARTUP`

The map the server chooses when starting up. If you set an invalid value, the server will not start properly. The default value is `startRandomMap`.

### `G_PASSWORD`

A password that is needed when joining the server. The default value is none.

### `SV_MAXCLIENTS`

The maximum amount of players that can join the server. Its default value is `16`.

### `SV_PRIVATECLIENTS`

The number of private slots. Use in conjunction with `SV_PRIVATEPASSWORD`. The default value is `0`.

### `SV_PRIVATEPASSWORD`

The password needed to join into a private slot. The default value is none.

### `G_ALLTALK`

Defines if players can talk to each other globally (`1`) or only inside their own team (`0`). The default value is `0`.

### `COM_HUNKMEGS`

The amount of memory the server is allowed to use. The default value is `60`.

## Use the container without the Quake Live Server Standards server framework

If you want to use this image without the Quake Live Server Standards server framework you have to override the values used to connect to the Redis database needed by minqlx.

Create an autoexec.cfg file containing the following cvars. The autoexec.cfg is executed after the server.cfg, thus it will overwrite any settings made.

- `set qlx_database` - The default database to use. The default `Redis`.
- `qlx_redisAddress` - The address to the Redis database. The default is `redis`.
- `qlx_redisDatabase` - The Redis database number. The default is `0`.
- `qlx_redisUnixSocket` - A boolean that determines whether or not qlx_redisAddress is a path to a UNIX socket. The default is `0`.
- `qlx_redisPassword` - The password to the Redis server, if any. The default is none.

Mount the file into the following location in the container: `/home/steam/ql/baseq3/autoexec.cfg`.

Any additional customizations that could not be made with the help of the provided environment variables also go in this file.