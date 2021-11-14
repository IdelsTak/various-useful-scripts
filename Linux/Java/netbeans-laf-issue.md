# NetBeans LaF Issue on (Arch) Linux

How to solve:

1. Install gtk2, gtk-engine-murrine
2. Edit the `/etc/profile.d/jre.sh` file and add: `export JDK_JAVA_OPTIONS=-Djdk.gtk.version=2.2`
3. Reboot
