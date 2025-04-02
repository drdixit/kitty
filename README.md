
# Current Kitty Terminal Config

~/.config/kitty



## Fonts in config: IosevkaTerm

- make sure you have wget and unzip before running this command
- this will install IosevkaTerm Fonts

```bash
wget -P ~/.local/share/fonts https://github.com/ryanoasis/nerd-fonts/releases/download/v3.3.0/IosevkaTerm.zip && cd ~/.local/share/fonts && unzip IosevkaTerm.zip && rm IosevkaTerm.zip LICENSE.md README.md && fc-cache -fv
```

- ### I am on debian 12 with dwm(xorg):

- #### First I install kitty through this command mentioned on [website](https://sw.kovidgoyal.net/kitty/binary/)
```bash
curl -L https://sw.kovidgoyal.net/kitty/installer.sh | sh /dev/stdin
```

- ### option 1: extand path
- #### after that i added kitty to path (put this line at end of .bashrc or .bash_profile dont put it in .profile)
- NOTE: dont forget to change user with your username
```bash
export PATH=$PATH:/home/user/.local/kitty.app/bin
```

- ### option 2: system wide symbolic link - I USE THIS ONLY FOR KITTY
- NOTE: dont forget to change user with your username
```bash
sudo ln -s /home/user/.local/kitty.app/bin/kitty /usr/local/bin/kitty
```
```bash
sudo ln -s /home/user/.local/kitty.app/bin/kitten /usr/local/bin/kitten
```

 - ### option 3: user specific symbolic link
 - NOTE: dont forget to change user with your username
```bash
ln -s /home/user/.local/kitty.app/bin/kitty ~/.local/bin/kitty
```
```bash
ln -s /home/user/.local/kitty.app/bin/kitten ~/.local/bin/kitten
```

- #### kitty throwing this error
```bash
user@debian:~$ kitty
[0.038] Traceback (most recent call last):
  File "kitty/main.py", line 540, in main
  File "kitty/main.py", line 528, in _main
  File "kitty/main.py", line 101, in init_glfw
  File "kitty/main.py", line 93, in init_glfw_module
RuntimeError: Failed to dlopen /home/user/.local/kitty.app/lib/kitty-extensions/kitty.glfw-x11.so with error: libxcb-xkb.so.1: cannot open shared object file: No such file or directory
```

- #### i dont know what excet what its missing on debian 12 but after installing this it fixed.
```bash
sudo apt install libx11-xcb1 libxcb1 libx11-dev libxcb-xkb1
```

- #### still some errors but previous ones gone
```bash
user@debian:~$ kitty
ignoreboth or ignorespace present in bash HISTCONTROL setting, showing running command will not be robust
[0.165] [glfw error 65544]: process_desktop_settings: failed with error: [org.freedesktop.DBus.Error.ServiceUnknown] The name org.freedesktop.portal.Desktop was not provided by any .service files
[0.165] [glfw error 65544]: Notify: Failed to get server capabilities error: [org.freedesktop.DBus.Error.ServiceUnknown] The name org.freedesktop.Notifications was not provided by any .service files
```

- #### two eroors haaa. first one is it complaining about .bashrc command history setting
- #### Fix: go to .bashrc and change setting named HISTCONTROL
```bash
export HISTCONTROL=ignoreboth
```
- #### change this to
```bash
HISTCONTROL=ignoredups
```
- #### ignoredups:
- This option prevents duplicate commands from being added to the history.
- If you run ls twice in a row, only the first occurrence will be saved to the history.
- #### ignorespace:
- Commands that begin with a space will not be saved to the history.
- If you run a command like ls, it won't be added to the history because it starts with a space.
- #### ignoreboth:
- This is a combination of ignoredups and ignorespace. It means both duplicate commands and commands that start with a space will be ignored.
- If you run ls twice, or run a command starting with a space, neither will be saved to the history.

- #### now this one left
```bash
user@debian:~$ kitty
[0.161] [glfw error 65544]: process_desktop_settings: failed with error: [org.freedesktop.DBus.Error.ServiceUnknown] The name org.freedesktop.portal.Desktop was not provided by any .service files
[0.161] [glfw error 65544]: Notify: Failed to get server capabilities error: [org.freedesktop.DBus.Error.ServiceUnknown] The name org.freedesktop.Notifications was not provided by any .service files
```
- #### looks like its missing notification demon
- #### i install this
```bash
sudo apt install xdg-desktop-portal xdg-desktop-portal-gtk libnotify-bin notification-daemon
```
- #### you cant find notification-daemon in path (i dont know why)
- this is how i find it
```bash
user@debian:~$ sudo apt search notification-daemon
Sorting... Done
Full Text Search... Done
dunst/stable 1.9.0-0.1 amd64
  dmenu-ish notification-daemon

libnotify4/stable,now 0.8.1-1 amd64 [installed,automatic]
  sends desktop notifications to a notification daemon

lxde-core/stable,stable 11 all
  metapackage for the LXDE core

mate-notification-daemon/stable 1.26.0-1+deb12u1 amd64
  daemon to display passive popup notifications

mate-notification-daemon-common/stable,stable 1.26.0-1+deb12u1 all
  daemon to display passive popup notifications (common files)

notification-daemon/stable,now 3.20.0-4+b1 amd64 [installed]
  daemon for displaying passive pop-up notifications

ukui-notification-daemon/stable 1.0.1-1 amd64
  daemon to display passive popup notifications

user@debian:~$ which notification-daemon
user@debian:~$ sudo find / -name notification-daemon
find: ‘/run/user/1000/doc’: Permission denied
/usr/share/doc/notification-daemon
/usr/lib/notification-daemon
/usr/lib/notification-daemon/notification-daemon
```
- #### Fix: i prefere to put this line to my .xinitrc or manually start from terminal

```bash
/usr/lib/notification-daemon/notification-daemon &
```

- ## from this, its gone off topic:
- #### we are at it we must install polkit or policykit agent
```bash
sudo apt install policykit-1-gnome
```
- #### again start it manually or from .xinitrc every time i do startx.
- again command is not showing up we need to start it with absolute path.
- #### Note: this command only works on debian 12 arch has different command/path find it your self.
```bash
/usr/lib/policykit-1-gnome/polkit-gnome-authentication-agent-1 &
```

- #### you can verify if policykit is running by
```bash
sudo apt install pkexec
pkexec ls
```
- after this you must see pop up window asking password.

- ### At end my .xinitrc looks like this
```bash
xset s off & # Disable screen saver (no blanking, no dimming)
xset -dpms & # Disable Display Power Management (no standby, suspend, or power-off)
dbus-update-activation-environment --systemd --all & # Don't Exactly know which variables are updated but it's good idea to run it with startup, seen on forms
/usr/lib/notification-daemon/notification-daemon & # Notification Demon
/usr/lib/policykit-1-gnome/polkit-gnome-authentication-agent-1 & # Policykit / Authentication Agent
exec dwm > ~/.dwm.log 2>&1
```