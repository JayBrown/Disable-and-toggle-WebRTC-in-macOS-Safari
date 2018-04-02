# Disable and toggle WebRTC in macOS Safari <img src="https://github.com/JayBrown/Disable-and-toggle-WebRTC-in-macOS-Safari/blob/master/img/jb-img.png" height="20px"/>

WebRTC can be a serious privacy & security problem. While it is easy to disable WebRTC in Firefox, and fairly easy to do so in Chrome/Chromium (see [here](https://www.privacytools.io/#webrtc)), Apple doesn't let the user disable WebRTC in Safari.

With this hack you will be able to do so, but **_please note_** that you'll be messing (just a little bit) with a macOS system file. It is safe, and it can be reverted any time, but it's obviously not for the casual Mac user.

To be on the safe side, you should **create a backup** of your boot volume before proceeding.

## Procedure
* Launch Safari
  * Preferences > Advanced: tick **Show Develop menu in menu bar**
  * Develop > WebRTC: untick **Enable Legacy WebRTC API**
* Quit Safari
* If you have enabled **System Integrity Protection (SIP)**, [disable it](https://www.imore.com/el-capitan-system-integrity-protection-helps-keep-malware-away)
  * Don't launch Safari after login
* Run the **following commands** in Terminal, iTerm etc.:
  * `mkdir -p ~/Library/StagedFrameworks/Safari`
  * `sudo mv /System/Library/StagedFrameworks/Safari/libwebrtc.dylib ~/Library/StagedFrameworks/Safari/libwebrtc.dylib`
  * `sudo chflags norestricted ~/Library/StagedFrameworks/Safari/libwebrtc.dylib`
  * optional (recommended): `sudo chown admin:staff ~/Library/StagedFrameworks/Safari/libwebrtc.dylib`
  * `sudo ln -s ~/Library/StagedFrameworks/Safari/libwebrtc.dylib /System/Library/StagedFrameworks/Safari/libwebrtc.dylib`
* Reenable SIP again (_see above_)
* Launch Safari and determine status of WebRTC, e.g. on **[this website](https://browserleaks.com/webrtc)**
  * _WebRTC Support Detection_ should read "true"
  * Close the browser tab, but don't quit Safari
* **Disable WebRTC**: `mv ~/Library/StagedFrameworks/Safari/libwebrtc.dylib ~/Library/StagedFrameworks/Safari/libwebrtc.dylib.out`
* In Safari open a new tab and check the status of WebRTC again (_see above_)
  * _WebRTC Support Detection_ should now read "false"
* To **reenabble WebRTC** run the opposite move command: `mv ~/Library/StagedFrameworks/Safari/libwebrtc.dylib.out ~/Library/StagedFrameworks/Safari/libwebrtc.dylib`

## Notes
You can toggle WebRTC for Safari now, and you can include the `mv` commands in a **[BitBar](https://github.com/matryer/bitbar)** plugin, or embed the commands in an AppleScript that you can access from your AppleScript menulet: `do shell script "<mv command>"`

You can leave out the `sudo chown admin:staff` command (_see above_); in this case the file `libwebrtc.dylib` will still be owned by `root:wheel`, so you need to run the `mv` command with `sudo`. In automated setups (BitBar, AppleScript menu), you then need to add `with administrator privileges` to the commands:

* in a shell script (BitBar etc.): `osascript -e 'do shell script "<mv command>" with administrator privileges'`
* in an Apple Script: `do shell script "<mv command>" with administrator privileges`

## Bugs
If you find bugs or errors in the procedure, please report them!

Tested successfully on El Capitan (OS X 10.11.6).
