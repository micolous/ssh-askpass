ssh-askpass
===========

ssh-askpass for OS X/macOS. Works in (at least) 10.7+ (including Monterey)

Used to accept (or deny) the use of the private key(s) added to the SSH authentication agent with `ssh-add -c`.

![Screenshot](https://github.com/theseal/ssh-askpass/raw/master/sample/ssh-askpass.png)

**If you’re having trouble with ssh-askpass after OS upgrade, please follow the installation steps again.**

## Installation

### [Homebrew](https://brew.sh/)

1. Run:

   ```sh
   brew install xquartz theseal/ssh-askpass/ssh-askpass
   ```

   See: [why install XQuartz?](#why-install-xquartz).

1. Log out and log in again.

1. Check that the `DISPLAY` environment variable is now set for `ssh-agent`
   by XQuartz in "inherited environment":

   ```sh
   launchctl print gui/$UID/com.openssh.ssh-agent
   ```

1. On Apple Silicon Macs, run:

   ```sh
   sudo mkdir -p /private/var/select/X11/bin
   sudo ln -s /opt/homebrew/bin/ssh-askpass /private/var/select/X11/bin/
   ```

   On Intel Macs, run:

   ```sh
   sudo mkdir -p /private/var/select/X11/bin
   sudo ln -s /usr/local/bin/ssh-askpass /private/var/select/X11/bin/
   ```

### [MacPorts](https://www.macports.org)

1. Install [XQuartz](https://www.xquartz.org/) from their packages.

   MacPorts package this as well, but they've modified their install to
   disable the user LaunchAgent by default. The upstream package just works.

   See: [why install XQuartz?](#why-install-xquartz).

1. Log out and log in again.

1. Check that the `DISPLAY` environment variable is now set for `ssh-agent`
   by XQuartz in "inherited environment":

   ```sh
   launchctl print gui/$UID/com.openssh.ssh-agent
   ```

1. Run:

   ```sh
   sudo port install ssh-askpass
   sudo mkdir -p /private/var/select/X11/bin
   sudo ln -s /opt/local/bin/ssh-askpass /private/var/select/X11/bin/
   ```

### Without Homebrew/MacPorts

1. Install [XQuartz](https://www.xquartz.org/) from their packages.

   See: [why install XQuartz?](#why-install-xquartz).

1. Log out and log in again, so Apple's `ssh-agent` picks up the `DISPLAY`
   environment variables.

1. Check that the `DISPLAY` environment variable is now set for `ssh-agent`
   by XQuartz in "inherited environment":

   ```sh
   launchctl print gui/$UID/com.openssh.ssh-agent
   ```

1. Install `ssh-askpass` to `/private/var/select/X11/bin/`:

   ```sh
   sudo mkdir -p /private/var/select/X11/bin
   sudo cp ssh-askpass /private/var/select/X11/bin/
   ```

   macOS has a broken symlink at `/usr/X11R6` to this path, so this
   creates it and puts `ssh-askpass` there.

You should now be able to use it with `ssh-add -c`.

If some tool does not look for `ssh-askpass` in
`/usr/X11R6/bin/ssh-askpass`, you can a LaunchAgent to provide the path in
the `SSH_ASKPASS` environment variable:

```sh
cp ssh-askpass.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/ssh-askpass.plist
```

## Enabling keyboard navigation
For security reasons ssh-askpass defaults to cancel since it's too easy to
press spacebar and accept a connection or other actions which might use
ssh-keys. To make it easier to press `OK`:

* Go to `System Preferences` and then `Keyboard`.

#### Pre 10.11
* Under the `Keyboard` tab, click on `All controls`.

#### 10.11-10.14
* Under the `Shortcuts` tab, click on `All controls`.

#### 10.15+
* At the bottom of the `Shortcuts` tab, check option `Use keyboard navigation to move focus between controls`.

Now you can press ⇥+spacebar to press `OK`.

## Why install XQuartz?

[Sonoma 14.6 and later block environment variables set by `launchctl setenv` from system LaunchAgents](https://github.com/theseal/ssh-askpass/issues/54#issuecomment-2264396356)
(eg: Apple's `ssh-agent`).

However, these changes **do not** affect environment variables set by
non-system LaunchAgents using `SecureSocketWithKey`.

When XQuartz' LaunchAgent is configured correctly, it instructs `launchd` to
setup a socket and expose it with the `DISPLAY` environment variable.

This also means you can't rely on the `SSH_ASKPASS` environment variable to
tell Apple's `ssh-agent` where `ssh-askpass` is - it must be available at
the default location (`/usr/X11R6/bin/ssh-askpass`).

## License
ISC license

## Contributors
* [theseal](https://github.com/theseal)
* [simmel](https://github.com/simmel)
