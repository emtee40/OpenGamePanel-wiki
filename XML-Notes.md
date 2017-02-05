## OGP XML Notes
**New Additions 2017**

_The order of each XML element matters, and this guide presents them in their order of appearance!_
___
###Windows:
####Pre-Start Commands:
Comes after the `<server_params>` element.  There can only be one `<pre_start>` element.  It can run multiple lines of script that will be executed by the cmd batch environment.

This can be filled with lines of script that will be run in a batch script just before the game server is started.  The script will always change directory into the home directory before your commands will run, so you can reference things locally.
```
<pre_start></pre_start>
```

####Environment Variables:
Comes after `<pre_start>` element.  There can only be one `<environment_variables>` element.  It can contain multiple entries one per line.

```
<environment_variables>
</environment_variables>
```
Used for setting environment variables which may be needed by some servers.  This is run in the batch environment, so please make sure you're using Windows commands for your environment SETS.

Special entries:

`{OGP_HOME_DIR}` will be replaced by the home directory for the game server.
___
###Linux:

####Pre-Start Commands:
Comes after the `<server_params>` element.  There can only be one `<pre_start>` element.  It can run multiple lines of script that will be executed by the bash shell.

This can be filled with lines of script that will be run in a bash script just before the game server is started. You do NOT need to provide the shebang "#!/bin/bash" in your commands.  The script will always change directory into the home directory before your commands will run, so you can reference things locally.
```
<pre_start></pre_start>
```

Example (writes hiya to a file named testingPreStart in the home directory of the server):

```
<pre_start>
    echo "hiya" >> testingPreStart
</pre_start>
```

####Environment Variables:
Comes after `<pre_start>` element.  There can only be one `<environment_variables>` element.  It can contain multiple entries one per line.

```
<environment_variables>
</environment_variables>
```
Used for setting environment variables which may be needed by some servers such as Rust.  

Example:

```
<environment_variables>
export LD_LIBRARY_PATH="{OGP_HOME_DIR}/RustDedicated_Data/Plugins/x86_64"
</environment_variables>
```

Special entries:

`{OGP_HOME_DIR}` will be replaced by the home directory for the game server.

####Locking / Protecting Additional Files:
Comes after `<environment_variables>` element.  There can only be one `<lock_files>` element.  It can contain multiple entries one per line.

```
<lock_files>
</lock_files>
```
Used for protecting additional game server files using chattr.  This is a Linux only element.   You can use relative (paths are relative to the home directory for the game server) or absolute paths.

Example:

```
<lock_files>
bin/AvorionServer
</lock_files>
```
The above example adds chattr +i to the bin/AvorionServer executable.  This prevents the user from changing it.  When SteamCMD or Rsync is run to update the files, everything is unlocked, but the files specified here will be locked post update / install.  These files are also locked again (just to make sure) before the server is restarted and started.  Entries here will not BREAK or AFFECT the steamcmd or Rsync update process.

Special entries:

`{OGP_HOME_DIR}` will be replaced by the home directory for the game server.  However, if you're using this variable, you should just use a local path.