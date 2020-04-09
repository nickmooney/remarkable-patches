# reMarkable Firmware Patches

This is a set of firmware patches for the reMarkable 1 tablet. They are all in `bspatch` format. They are organized by firmware version (currently only 2.1.1.3 is supported).

Current patches:

* Disable file uploading and keep downloading (if you don't want your notes to leave your reMarkable)
* Make the web UI "invincible" (i.e. if you enable the UI, it will persist through reboots)
* Bind the web interface to `wlan0` (Wi-Fi IP) instead of `usb0`
* Bind the web interface to `lo` (localhost) instead of `usb0`

## Patches

### Disable file upload

When the reMarkable software is queueing files to be uploaded, it checks if the device is online. This patch causes that check to fail always, so that files are never uploaded from the device to the reMarkable cloud. Using the cloud to sync documents from your computer to the reMarkable will still work.

**Warning:** This patch will be undone by a firmware upgrade, and documents will start syncing with the reMarkable cloud again.

You can find this patch in `xochitl-2.1.1.3-no-upload.bspatch`.

### WebUI invincibility

This patch keeps the reMarkable web UI enabled across reboots. When this patch is first applied, you will have to toggle the web interface on. If you want to disable it again, you must toggle the Web UI switch off and then reboot the device.

I can confirm that this works consistently if you bind the web interface to `lo`. If you bind it to `wlan0` or keep the default behavior of binding to `usb0`, the web interface may not come up automatically, as `wlan0` and `usb0` both lack assigned IP addresses on boot.

You can find this patch in `xochitl-2.1.1.3-lo.bspatch`.

### Bind web interface to wlan0 or lo

Since the update to reMarkable v2.0, the web interface will no longer activate if `usb0` is not assigned an address. This means that you can't activate the web interface if the reMarkable isn't plugged in to a computer (or the interface is manually assigned an IP).

#### wlan0

This patch causes the web interface to bind to `wlan0` instead, meaning you should be able to access it over a wireless network. Note that the UI will still say to navigate to the USB IP, but this is erroneous.

You can find this patch in `xochitl-2.1.1.3-wlan0.bspatch`.

#### lo

This patch causes the web interface to bind to localhost instead, so you can access it via an SSH tunnel (e.g. `ssh -L 8080:localhost:80 root@remarkable`). I can confirm that this patch plays nicely with the Web UI invincibility patch.

You can find this patch in `xochitl-2.1.1.3-lo.bspatch`.

## Applying a patch

You'll need `bsdiff` and `bspatch` installed. On a macOS system, you can `brew install bsdiff`.

### Getting xochitl

`xochitl` is the binary that runs the reMarkable interface. You can get it from your reMarkable via scp:

```
scp remarkable:/usr/bin/xochitl /path/to/local/dir/xochitl
```

### Running `bspatch`

To apply the "bind web interface to wlan0" patch, for example:

```
bspatch xochitl xochitl-wlan0 xochitl-2.1.1.3-wlan0.bspatch
```

### Uploading the patched binary

Upload the patched binary to `/usr/bin/xochitl`. You'll need to stop the service before you do this.

I recommend that you make a backup of your system `xochitl` binary before modifying it. On your reMarkable, issue a `cp /usr/bin/xochitl /home/root/xochitl-backup`.

To upload a patched binary:

* On your reMarkable: `systemctl stop xochitl`
* On your computer: `scp xochitl-wlan0 remarkable:/usr/bin/xochitl`
* On your reMarkable: `systemctl restart xochitl`

# FAQ

* **Q**: Can I apply more than one patch?
* **A**: Yes, you just have to do them in tandem. I can't guarantee every patch will play nice, but you should be able to do something like the following:

```
$ bspatch xochitl xochitl-no-upload xochitl-2.1.1.3-no-upload.bspatch
$ bspatch xochitl-no-upload xochitl-no-upload-wlan0 xochitl-2.1.1.3-wlan0.bspatch
```

Feel free to file a GitHub issue for any other questions!
