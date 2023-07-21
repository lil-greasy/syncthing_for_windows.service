# syncthing_for_windows.service
A systemd unit file for managing Syncthing for Windows. Why would anyone create such an unholy thing?

## Background
On systems running Windows Subsystem for Linux, I’ve long found it necessary to run both Linux and Windows versions of Syncthing, because the Linux version has trouble watching the Windows filesystem and vice versa. I resisted this for a long time, because I worried that running both versions concurrently would be complicated. Turns out, all you have to do is change the GUI port for one version, which is defined by a single line in `config.xml`. I installed the web interfaces for both versions like PWA’s, and I was off to the proverbial races.

**Of course, just because the setup doesn’t have to be complicated doesn’t mean I won’t find a way to *make* it complicated.** I became a bit obsessed with figuring out the best way to “organize̦” the two daemons, especially since Windows doesn’t provide a straightforward way to run a console application like Syncthing in the background.

First, and most simply, I created a split-screen profile for Windows Terminal that ran both versions, and I just kept that profile open in a tab at all times. This was a pretty good setup most of the time. But if for any reason Windows Terminal closed, the Windows version of Syncthing would close with it, but not the Linux version, which could get a little awkward when I reopened Windows Terminal; the Windows version would restart but the Linux version would try and fail to start a second copy.

So next, I wrote a script that would create a split-screen tmux session. If the tmux session already existed, the script would instead reattach to it. I could close my terminal and both Syncthings would keep running. This was theoretically a more elegant strategy, but somehow I kept running into weird edge cases that I lost patience with diagnosing.

As it happens, the Linux version of Syncthing comes with a systemd unit file, which behaves just as you would expect. At some point, I wondered to myself, *why not just manage the Windows version the same way?* One of the things I really love about WSL is that it often feels like there isn’t a boundary between Windows and Linux so much as a vast gray area in which you can use whatever tools you like, without worrying about which operating system they’re part of.

## Usage notes
* You’ve got to customize `ExecStart=` to reflect the location of your Syncthing executable, which probably starts with “`/mnt/c/`”.
* You’ve got to customize `ExecStop=` to reflect your API key, which is in the `[gui]` section of your config.xml. If you’ve changed the port for your Syncthing GUI, the command will also have to reflect that.
