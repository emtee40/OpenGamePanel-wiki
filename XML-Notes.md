## OGP XML Notes
**New Additions 2017**

_The order of each XML element matters, and this guide presents them in their order of appearance!_
___
###Linux and Windows:
####Replacing Key Value Pairs in Configuration Files as Text
`<replace_texts>` Comes after `<mods>`.

Contains multiple `<text>` entries like so:

```
  <replace_texts>
	  <text key="control_password">
		<default>ServerAdminPassword=.*</default>
		<var>ServerAdminPassword=</var>
		<filepath>ShooterGame/Saved/Config/LinuxServer/GameUserSettings.ini</filepath>
		<options>sq</options>
	  </text>
	  <text key="home_name">
		<default>SessionName=.*</default>
		<var>SessionName=</var>
		<filepath>ShooterGame/Saved/Config/LinuxServer/GameUserSettings.ini</filepath>
		<options>sq</options>
	  </text>
  </replace_texts>
```

`<default>` within `<text>` is what the line to replace starts with.

`<var>` within `<text>` is the key for what should be kept when the line is replaced with the value entered by the user when replacing text occurs.

`<filepath>` within `<text>` specifies the text file to make the replacement in.

`<options>` within `<text>` specifies how to enter the user's value after the `<var>` key.  Possible options are:

```
        nothing / no value = placed as is
        s = space / separated
        q = quoted
        sq = space and quotes
        sc = space and ends with a comma
        sqc = space, quoted, and ends with a comma
```

####Server Params:
`<server_params>` Comes after `<replace_texts>`.

Contains multiple `<param>` entries like so:

```
  <server_params>
	<param id="RCONP" key="?RCONPort=" type="text">
      <option>ns</option>
      <caption>Standard Rcon Port is 32330</caption>
      <desc>Change this Port, if you have more then 1 server.</desc>
    </param>
  	<param id="QPORT2" key="-port=" type="text">
      <option>ns</option>
      <caption>7777 Port</caption>
      <desc>Change this Port, if you have more then 1 server.</desc>
    </param>
	<param id="DO" key="?DifficultyOffset=" type="text">
      <option>ns</option>
      <caption>Difficulty Offset</caption>
      <desc>Changes the difficulty on the server, choose 0 or 1</desc>
    </param>
	<param id="SP" key="?ServerPassword=" type="text">
      <option>ns</option>
      <caption>Server Password</caption>
      <desc>Players must know this password to connect.</desc>
    </param>
  </server_params>
```

id attribute on the `<param>` specifies which variable to replace in the `<cli_template>`

For example, `%SP%` in `<cli_template>` will be replaced with `?ServerPassword=` and the value entered by the user.  The value entered can be modified to fit your needs by using the `<option>` element within the `<param>` element.

Valid options are for the `<option>` element within the `<param>` element within the `<server_params>` element are:
```
        ns = no space between key and value
        q = quotes wrapped around value after key (no space added)
        s = space added after key before value (no quotes added)
        anything else = space after key and quotes around the value 
```
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