# thinkpadt480-linux
A repository featuring some of the tweaks and configs you can do on the ThinkPad T480 to make it better on Linux
All the things to setup:
1. Setup fingerprint
	1. Add the repositories required for fingerprint setup and install:
		sudo add-apt-repository ppa:ubuntuhandbook1/open-fprintd
		sudo apt update
		sudo apt install open-fprintd fprintd-clients python3-validity
		
	2. Now, check to make sure that the fingerprint sensor is actually working
		1. This might not make sense why we are doing this but I can explain it in a second, the first step is to run this:
			1. systemctl stop python-validity
			2. systemctl disable python-validity
			The reason why we are doing this is so that python-validity doesn't go wild when you restart and cause you to be unable to sign in, I had it happen to me.
			
		2. We're very close to getting the sensor working, we have one step that requires the BIOS, and it's this one, just follow these steps and you'll be fine
			1. Restart your computer, then spam F1 to enter the BIOS
			2. Use the right arrow key to navigate to "Security"
			3. Use the down arrow key to navigate to "Fingerprint"
			4. Hit enter
			5. Choose "Predesktop Authentication" -> "Disabled"
			6. Choose "Reset Fingerprint Data" -> "Enter"
				(This will reset your fingerprint sensor, Windows doesn't really like this)
			7. Hit F10 -> "Exit saving changes"
			8. Your computer will now restart
			
		3. Now to test the operation of the fingerprint sensor
			Run these commands:
			1. systemctl enable python-validity
			2. systemctl start python-validity
			That should start our fingerprint program, to test to make sure it's working run these:
			1. fprintd-enroll
			2. Choose the finger
			3. Enroll
			4. fprintd-verify
			If this is a success then you should see the fingerprint light turn on
			Scan your finger
			If you used the same finger you scanned it should say that it was a success, if not, try it a few more times
			If it is being very inaccurate, then go back to BIOS step
			
	2. Now it's time to make it work when the lid is shut
		So by default, python-validity doesn't like it if you close the lid on your ThinkPad, it makes it go crazy, so we are going to fix that
			1. Open terminal and run:
				sudo nano /lib/systemd/system/resume-fingerprint.service
			2. When nano opens it should say "New File" at the bottom of the Terminal window
			3. Paste this into that new file:

              [Unit]
              Description=Restart services to fix fingerprint integration
              After=suspend.target hibernate.target hybrid-sleep.target suspend-then-hibernate.target

              [Service]
              Type=simple
              ExecStart=systemctl restart open-fprintd.service python3-validity.service

              [Install]
              WantedBy=suspend.target hibernate.target hybrid-sleep.target suspend-then-hibernate.target
      
  4. Hit Ctrl+X -> "Y" -> Enter
			5. It should save a new systemd service file
			6. Now to enable it
			7. Run: 
			systemctl enable resume-fingerprint.service
			systemctl start resume-fingerprint.service
			8. Your fingerprint sensor should be working when the lid is shut now! To test, just close the lid, wait for it to start sleeping, then reopen it
			The only issue that you may encounter is it does take the fingerprint just a second to start up so when you open the lid wait a few seconds, then try
			
2. Setup Audio
	I don't know how much you have used your ThinkPad on Linux before following this guide, but if you have tried to listen to anything, you'll realize,
	the speakers sound like absolute doggy doodoo, so we are going to fix that
	1. Install Easy Effects
		You have two ways of doing this, I have both listed down below.
			Method 1: Using Terminal
				1. Open Terminal
				2. Run:
				flatpak install flathub com.github.wwmm.easyeffects
				3. Once flatpak finishes installing Easy Effects then you should be able to open it from your program launcher
			Method 2: Using the software center
				1. Open whatever software manager you use (make sure you have flatpak support turned on)
				2. Click on the search bar or search button
				3. Type in Easy Effects
				4. Click on the Easy Effects software listing
				5. Click Install
	2. Loading Easy Effects configuration files
		So now you have Easy Effects installed, yay! Now it's time to make your speakers sound good.
			1. Download the zip in the releases
			2. Extract the zip
			3. Copy the contents of "input" to "~/.config/easyeffects/input"
			4. Copy the contents of "irs" to "~/.config/easyeffects/irs"
			5. Copy the contents of "output" to "~/.config/easyeffects/output"
	3. Making sure Easy Effects is working properly
		Now you have copied the config files so now it is time to actually do something with all of these config files
		1. Open Easy Effects
		2. Click on Presets in the top left corner
		3. Load a preset and listen to how it sounds
		4. If it sounds the same as before, make sure that the global bypass is disabled, the button for that is right next to the presets button
You are now done! I hope that this guide has helped you get your ThinkPad T480 to be the perfect Linux machine!

Credits:
JackHack96 for the Perfect EQ base for the Dolby Audio configs
wwmm for actually making all of the audio configuration possible by making Easy Effects
