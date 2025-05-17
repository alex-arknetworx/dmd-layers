Connect your AT&T DMD 5620 Terminal Emulator (commercial version of the Blit terminal) to a real Debian Linux host (rather than SIMH emulated UNIX) and run the layers window manager. Screenshot at the end.

see https://loomcom.com/3b2/dmd5620-emulator/

Some minor edits have been made to enable compiling of the psuedo-tty version of AT&T's layers software on a Debian 11 host.

On the Debian host (example a minimal Debian 11.11 install on a libvirt/virt-manager virtual machine with an emulated serial device via pty) you will require the following apt packages: sudo, nano, build-essential, git.

git clone this repo then

cd dmd-layers

sudo make

sudo make install

This will install the layers software into /usr/local/dmdlayers/bin/

You will then need to configure a serial-getty on your Debian host for either a real serial port or emulated serial port (example though the virtual machine which appears on the bare metal host as /dev/pts/0) by:

sudo nano /lib/systemd/system/serial-getty@.service

Find the line ExecStart=-/sbin/agetty -o '-p -- \\u' --keep-baud 115200,57600,38400,9600 %I $TERM
and replace it with
ExecStart=-/sbin/agetty 9600 %I $TERM

save changes, exit then

sudo systemctl daemon-reload

sudo systemctl enable serial-getty@ttyS0.service

where @ttyS0 is my emulated Debian host's serial port /dev/ttyS0 (which is routed through to /dev/pts/0 on the bare metal host). Substitute with other /dev/tty devices such as ttyUSB0 on a real Debian host.

sudo reboot

On the bare metal host running the virtualised Debian host, follow the instructions to compile one of the versions of DMD 5620 emulator (Linux GTK version pictured), refer https://loomcom.com/3b2/dmd5620-emulator/

On the bare metal host, start the dmd5620 executable and pass a reference to the bare metal host's serial port which is connected to the Debian host's serial port:

sudo ./dmd5620 --nvram ~/.dmd5620_nvram --device /dev/pts/0

Press the enter key a few times to get the logon. Once logged into the Debian host via the DMD 5620, run the following at the prompt:

cd /usr/local/dmdlayers/bin

sudo ./layers

Enjoy creating multiple terminal windows and working with the Debian host from Layers. Future work: see if we can compile and run graphical programs such as jim and clock.

![running_vm_dmd-layers](https://github.com/user-attachments/assets/f5d22587-e816-4d85-bf6d-505878678238)
