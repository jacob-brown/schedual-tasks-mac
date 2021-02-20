# Using `launchd` MacOS
A simple example for setting periodic automated task on MacOS using launchctl.

Based on: 
* https://www.splinter.com.au/using-launchd-to-run-a-script-every-5-mins-on/
* https://www.launchd.info/

**LaunchControl** is also good as a GUI for quick monitoring.

Example: Add date and time to a file (simple shell script)

script: `myTask.sh`

## 1.Make script exactable
`chmod a+x myTask.sh`

## 2.Make a plist file
On **MacOS** plist files instruct `launchd` via `launchctl` (the controller)

The naming convention needs to be something like `com.mycompanyname.mydepartment.mytaskname.plist`

I will use: `com.jacobfeatures.datetofile.plist`

## 3. When to run
The below indicates to run every *5 minute*. 

```
<key>StartInterval</key>
<integer>300</integer>
```
We could also use *RunAtLoad*

```
<key>RunAtLoad</key>
<true/>
```

* `Program` is for simpler scripts (i.e. running a program starter script)
* `ProgramArguments` are for more complex strings eg. `rsync --archive loc1 loc2`

Watch paths can be useful
```
<key>WatchPaths</key>
<array>
	<string>/path/to/directory_or_file</string>
</array>
```

## 4. Check plist file is correctly configured
```
plutil com.jacobfeatures.datetofile.plist
```
## 5. How to run
Copy the task with `launchd`. 

First, copy it into your `LaunchDaemons` folder, or `LaunchAgents` if you want it to only run when you’re logged in.

```
sudo cp com.jacobfeatures.datetofile.plist /Library/LaunchDaemons

```

To add without rebooting system (on reboot, the plist will be detected and added)
```
launchctl load -w /Library/LaunchDaemons/com.jacobfeatures.datetofile.plist
```

Check it is working `launchctl list | grep com.jacobfeatures.datetofile.plist`

## Disabling
To disable, unload (as it is probably loaded and running) and remove. 

```
launchctl unload -w /Library/LaunchDaemons/com.jacobfeatures.datetofile.plist
sudo rm /Library/LaunchDaemons/com.jacobfeatures.datetofile.plist
```

## Troubleshooting
The `.sh` script that is being run should use full paths (not relative).

eg. `python3` is not good enough `Library/Frameworks/Python.framework/Versions/3.8/bin/python3` should be used instead. 