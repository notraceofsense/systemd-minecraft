# systemd-minecraft
**systemd-minecraft** runs Minecraft servers under [systemd](https://github.com/systemd/systemd), allowing for easy, automatic administration of servers in the modern Linux environment.

## Requirements
- A Linux machine running systemd as the init system (which is most popular Linux distros nowadays)
- [mcrcon](https://github.com/Tiiffi/mcrcon) to allow communication with the server through the built-in [RCON](https://developer.valvesoftware.com/wiki/Source_RCON_Protocol) interface enabled in `server.properties` (this must be on the PATH)

## Usage
### Installation of Units
1. Place the unit files in the proper folder for unit files (e.g. `/etc/systemd/system`)
2. Create a folder for your servers at `/opt/minecraft`
3. Create a new system user (i.e. one with no login shell) `minecraft` with a home directory of `/opt/minecraft`
### Installing a new server
1. Create a new folder in `/opt/minecraft` (we will call this `<your-server>` - but call yours something fun!) 
2. Install your new Minecraft server in the folder
3. Create a script (or sym-link to an existing script) called `run.sh` to run the server and make it executeable (`chmod +x run.sh`)
4. Run the server (`./run.sh`) to complete the Minecraft EULA, create all associated files, and validate server functionality.
5. Open the file `server.properties` and make the following changes:
  - Change `enable-rcon` to `true` (`enable-rcon=true`)
  - Set `rcon.password` to something unique - don't use any shell reserved characters!
6. Create a new file called `server.conf`. In this file:
  - Add `RCON_PASSWORD=<your password>`
  - Add `SHUTDOWN_DELAY=<some value>` (I recommend 30) for a delay between the server shutdown warning broadcast to players and the actual shutdown
7. Change the file permissions for the `server.conf` file to read-only for the user *only* (`chmod 400 server.conf`)
8. Ensure that the minecraft user and group own the everything in the folder (`chown -R minecraft:minecraft .`)

You are now ready to run the server with systemd.

Optional Step: Create a backup.sh script to backup the server (this will be run by the backup service later.) For now, I'm leaving this one as an exercise for the user.
### Running the server
#### Basic Operation
(All of this is done with proper permissions for system-wide systemd administration (i.e. as `root`.)

- To start up the server:
  ```systemctl start minecraft@<your-server>```
- To enable automatic startup on boot:
  ```systemctl enable minecraft@<your-server>```
- To disable automatic startup on boot:
  ```systemctl disable minecraft@<your-server>```
- To stop the server:
  ```systemctl stop minecraft@<your-server>```
## Caveats
- This is still a heavily WIP side-project
- When using any of the auto timers, please only run one server at a time with them (have not tried two, it may lead to interesting behavior)

## Roadmap
- Make services run entirely under user mode instead of root, which allows us to...
  - Make a bunch of things tune-able through the `server.conf` file
  - Implement auto-restart functionality
- Implement stdin/stdout to the console using systemd sockets, thus removing the need for mcrcon
- Implement auto-installation (likely with `make`)
