As of version 2.5, you will need to plug in the USB cable *once* to enable the web UI after installing this patch. Once you've done it one time, it should persist through reboots without the need to re-enable.

If you don't have the USB cable handy for some reason, you can also do the following via SSH:

```
reMarkable: ~/ echo 1 > /tmp/1.txt
reMarkable: ~/ mount --bind /tmp/1.txt /sys/class/power_supply/imx_usb_charger/online
```

This will enable the UI toggle that allows you to turn on the web interface. Thanks to [@saleemrashid](https://github.com/saleemrashid) for the tip!
