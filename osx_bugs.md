# OSX bugs

A running list of bugs and catches I've encountered with OSX

## MATLAB looks fuzzy on a Retina computer

You need to update your Java Runtime Environment.

## Keynote can't save files or export them

Take screenshots and do whatever you can to save your work. If you try closing the file you will get the opportunity to duplicate before discard. Before you discard, try making a new document and copying all your slides into it.

All of you movie links will break for no reason. You need to manually re-import using Inspector, because right-clicking and choosing "replace" will only work for media that you've copied into Photos, which you don't use because it's 2016. If replace keeps failing, try copying your slides manually into a new document.

## Deleted Evernote app still trying to use location services

This is the same bug as described [here](https://discussions.apple.com/thread/4618820?start=0&tstart=0)

I tried removing location services access for the app. However, the only known full solution is [this one](https://superuser.com/questions/429344/remove-application-from-location-services-in-security-privacy-on-mac-os-x-10-7)
But Sierra would not let me CD into that folder


## Volume suddenly jumps to the right

This seems to happen sometimes when using Flash on Chrome, for some reason the system volume preferences change mysteriously. It can be fixed in System Settings > Sound by dragging the slider back to the middle

## Mission Control / Spaces / hotkeys stop working

In the terminal, type `kilall Dock` to restart the Dock

Answer found on [Stackexchange](http://apple.stackexchange.com/questions/170488/osx-yosemite-mission-control-stopped-working)

## System Preferences says No IP address, but I appear to be connected to the internet

This tends to happen after altering your internal IP address using a command-line tool like ipconfig. The Wifi menu bar icon will show "loading" or error, but you can browse the web normally.

In System Preferences, choose Network > Wifi > Assist Me > Diagnostic

Go through the wizard and it should be able to figure out what's going on.

## MATLAB MEX files won't compile

+ Append the MATLAB.app/bin to the FRONT of your path, then retry the install. The issue is that TeXLive has a Polish language command with the same name, which should should not recieve preference

+ Check to see where within XCode the current MACOSX10.?.sdk file resides. On my system this was located at:

	/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.13.sdk

+ There's a hard-coded path inside MATLAB's mexopts clang files that might not include recent macOS releases. These need to be manually edited (seriously) to include recent releases

	/Applications/MATLAB_R2014a.app/bin/maci64/mexopts/clang_maci64.xml
	/Applications/MATLAB_R2014a.app/bin/maci64/mexopts/clang++_maci64.xml

In each file, there are two instances that need to be edited. Each one looks like

	<dirExists name="$$/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.13.sdk" /> 

and

    <cmdReturns name="find $$ -name MacOSX10.13.sdk" />
                       
Oddly, the operating system version you are using is not equivalent to the name of the SDK file. I am using macOS 10.12.6, but the SDK file was named `MacOSX10.13.sdk`

## External hard drive not ejected properly, and now it won't mount

Connect the drive

	diskutil list

Find the identifier, something like `disk2` probably

	diskutil eject disk2

Now plug it into a different USB port on your computer and it will probably mount.

This advice was taken from [Stackexchange](http://apple.stackexchange.com/questions/243559/sometimes-mac-doesnt-recognise-my-external-hard-drive)

Other advice is to just leave it alone for 15-30 minutes while OSX figures out what's going on. This advice is from [this Stackexchange thread](http://superuser.com/questions/432831/mac-finder-wd-my-passport-wont-mount)

## An external hard drive will not mount, even after proper ejection

I have encountered this issues with Western Digital drives containing Time Machine backups. It may be related to when Spotlight is re-indexing my computer. In terminal, type:

	diskutil list

You should get output that lists all the drives connected the computer. My disk looks something like

	/dev/disk0
	   #:                       TYPE NAME                    SIZE       IDENTIFIER
	   0:      GUID_partition_scheme                        *500.3 GB   disk0
	   1:                        EFI EFI                     209.7 MB   disk0s1
	   2:          Apple_CoreStorage                         499.4 GB   disk0s2
	   3:                 Apple_Boot Recovery HD             650.1 MB   disk0s3
	/dev/disk1
	   #:                       TYPE NAME                    SIZE       IDENTIFIER
	   0:                  Apple_HFS Macintosh HD           *499.1 GB   disk1
	                                 Logical Volume on disk0s2
	/dev/disk2
	   #:                       TYPE NAME                    SIZE       IDENTIFIER
	   0:      GUID_partition_scheme                        *1.0 TB     disk2
	   1:                        EFI EFI                     209.7 MB   disk2s1
	   2:                  Apple_HFS Pawhuska                999.8 GB   disk2s2

If you don't see your hard drive then this won't work. To mount "Pawhuska" above:

	diskutil mount /dev/disk2s2


## Completely delete a program and its remnants from your computer

Use the application's onw uninstaller if at all possible

Delete it from /Users/william/Applications
Delete it from /Users/william/ApplicationData
Delete the plist files from LaunchAgents and LaunchDaements (Important! see below)

Here is an example for uninstalling KeyAccess (from [UT Austin](https://www.uta.edu/oit/cs/software/sassafras/keyaccess-62-mac/uninstall.php))

	Login with a local administrator account
	Open /Applications/Utilities/Activity Monitor.app
	Select the process with the Process Name of KeyAccess and click the Quit Process icon in the tool bar and select Force Quit
	Delete /Library/KeyAccess
	Delete /Library/StartupItems/KeyAccess
	Delete /Library/LaunchAgents/com.sassafras.KeyAccess.plist
	Delete /Library/LaunchDaemons/com.sassafras.KeyAccess.plist
	Delete /Library/PreferencePanes/KeyAccessPref.prefPane
	Delete /Library/Preferences/KeyAccess/

## Junk appearing at startup that does not appear under login items


### Check Launch Agents and Launch Daemons

Look for any Adobe or Skype stuff in LaunchAgents

	ls ~/Library/LaunchAgents/com.adobe*
	ls ~/Library/LaunchAgents/com.adobe*
	ls ~/Library/LaunchAgents/com.skype*

Unload anything you find. For example,

	launchctl unload -w ~/Library/LaunchAgents/com.adobe.AAM.Updater-1.0.plist 

If this breaks anything, you can re-enable it

	launchctl load -w ~/Library/LaunchAgents/com.adobe.AAM.Updater-1.0.plist 

Also worthwhile to disable notifications from Creative Cloud app

Now do the same for the sister folder, LaunchDaemons

Delete any plists for old programs

For a general search,

	sudo launchctl list

For any processes that really do not need to run,

	launchctl remove com.sassafras.KeyAccess.kass.1668

Directions from [here](http://apple.stackexchange.com/questions/74779/launchtl-any-way-to-disable-a-daemon-after-removing-the-plist-file)

For Adobe products, use:

Follow the advice [here](https://apple.stackexchange.com/questions/138941/how-do-i-stop-the-adobe-creative-cloud-app-from-auto-launching-on-login)

	launchctl unload -w /Library/LaunchAgents/com.adobe.AdobeCreativeCloud.plist

### Disable Adobe CoreSync and other background processes Adobe runs for its apps

Follow the incredible instructions [here](https://apple.stackexchange.com/questions/236577/how-to-disable-adobe-core-sync-app-on-os-x-from-being-launched-automatically)

Can also just kill off Creative Cloud using the instructions [here](https://apple.stackexchange.com/questions/218681/how-do-i-programatically-kill-the-cclibrary-process-by-pid)

### Check startup items folder

	ls -a /Volumes/Library/StartupItems

For example, remove the old KeyAccess software by killing the process, torching its folder in the StartupItems,

### Delete all remnants of other programs, as needed


### Python problems (especially matplotlib)

	 python setup.py build_ext --inplace

### Permission denied errors in bash

	chmod u+x my_script.sh


