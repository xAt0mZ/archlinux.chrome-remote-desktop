This is a fork of https://github.com/frealgagu/archlinux.chrome-remote-desktop

Sole purpose is to have updates managed on my own without waiting for the official repository to be updated.

## Clone

```sh
git clone git@github.com:xAt0mZ/archlinux.chrome-remote-desktop.git
cd archlinux.chrome-remote-desktop
```

## Install instructions

Clone the repository then run

```sh
# NEVER RUN AS ROOT
makepkg -csf
pacman -U ./chrome-remote-desktop-[...].pkg.tar.zst
```

If you encounter any error during packaging (like invalid hunk), see **Handling issues**

## Update

The PKGBUILD already takes care of downloading the latest version.
You simply need to recreate the package with `makepkg` and reinstall with `pacman`

See **Install instructions**

## Usage

Based on https://blog.victormendonca.com/2020/04/02/how-to-install-chrome-remote-desktop-on-arch/

Copying the content in case the link gets lost at some point.

1. Install the package following **Install instructions**
2. Run `crd --setup` to configure your connection (only do it once !)
3. Select your Desktop Environment and save the file
4. Enter a new resolution if you would like to use something different than the default (1366x768). Save the file
5. You should see the confirmation that the setup is complete
6. Go to https://remotedesktop.google.com/headless and click on `Begin`
7. Click on `Next`
8. Click on `Authorize`
9. Select the Goole account you would like to use
10. Give it permission
11. Click on the Debian Linux copy button and paste on your terminal
12. Give a pin to access your computer

You should get a confirmation that everything went ok
```
Starting Xvfb on display :20
X server is active.
Launching X session: ['/bin/sh', '/home/victor/.chrome-remote-desktop-session']
Launching host process
['/opt/google/chrome-remote-desktop/chrome-remote-desktop-host', '--host-config=-', '--audio-pipe-name=/home/victor/.config/chrome-remote-desktop/pulseaudio#ae6329c099/fifo_output', '--server-supports-exact-resize', '--ssh-auth-sockname=/tmp/chromoting.victor.ssh_auth_sock', '--signal-parent']
wait() returned (1092272,0)
Host ready to receive connections.
Log file: /tmp/chrome_remote_desktop
```

-----
## Additional Configuration

The additional configuration will allow you to connect to an existing session instead of creating a new one when connecting.

1. Find what display number X is using

```sh
$ echo $DISPLAY
:0
```

2. Create a file in `~/.config/chrome-remote-desktop/Xsession` with the display value

```sh
$ echo "0" > ~/.config/chrome-remote-desktop/Xsession
```

3. Stop the `chrome-remote-desktop.service`

```sh
$ systemctl stop chrome-remote-desktop.service
```

4. Check if it stopped with `crd --status`. If it did not, stop it with `crd --stop`

```sh
$ crd --status
CRD status: STOPPED
```

5. Take a backup of `/opt/google/chrome-remote-desktop/chrome-remote-desktop`

6. Download the (patched)[https://gist.githubusercontent.com/victorbrca/7bd9b351ccf2788a86aa94d296f4d48c/raw/7948453c8da09965b6f23280a1dbf86c432ea4c9/chrome-remote-desktop] `/opt/google/chrome-remote-desktop/chrome-remote-desktop` to the same location, or follow the instructions to manually modify your file (here)[https://gist.github.com/nightuser/2ec1b91a66ec33ef0a0a67b6c570eb40#file-use_existing_session-patch].

Note: The patched version was tested with chrome-remote-desktop 81.0.4044.60-1

7. Start the agent with crd --start so you can see verbose output. You should receive a confirmation when it starts

```sh
$ crd --start

Launching X server and X session.
Using existing Xorg session: 0
Launching host process
['/opt/google/chrome-remote-desktop/chrome-remote-desktop-host', '--host-config=-', '--audio-pipe-name=/home/victor/.config/chrome-remote-desktop/pulseaudio#ae6329c099/fifo_output', '--ssh-auth-sockname=/tmp/chromoting.victor.ssh_auth_sock', '--signal-parent']
Host ready to receive connections.
Log file: /tmp/chrome_remote_desktop_20200402_202207_2vtQSb
```

8. Go to https://remotedesktop.google.com/ from another computer and try to access your computer
