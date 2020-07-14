# Developing on a Windows machine

Sadly, many devs think that real software development on Windows is either an oxymoron
or just for finance companies paying tons of money for MSDN licenses.

This is wildly untrue: I do all of my personal dev (which is extensive) on a Windows machine.

In the past, all my dev was in a Linux desktop/GUI OS in Virtualbox
but Windows itself has come a long, long way just in the past year and some.

These days I do all my dev inside vagrant in virtualbox, using cygwin bash in windows terminal
-- usually with docker apps running inside all of that

But [WSL2](https://www.hanselman.com/blog/HowToSetUpDockerWithinWindowsSystemForLinuxWSL2OnWindows10.aspx) is out
and that's a strong option as well.

## Why here?

Note that this repo is about "tech job advice" --
and in coronavirus quarantine, a lot of tech interviews (all?) are remote.
A lot of devs have windows machines at home and are (surprisingly?)
unprepared for programming on those devices. This should help.

## Windows dev options

There are three main categories of options:

1. Install everything directly into Windows
1. Run a Linux virtual machine with full desktop UI
1. Do some other funky options, mostly involving more VMs of various kinds

There are a variety of ways to achieve all of these options,
so I'm going to tell you my recommended favorites
and then let you explore beyond that later if you reall want.

### 1. Install directly

When installing a lot of software quickly in Windows, the first thing to do is
install chocolatey, a package manager for Windows, effectively:

https://chocolatey.org/install

Run Powershell as Administrator and in that:

    Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

In that same admin powershell, install windows terminal for sure, probably with cygwin:

    choco upgrade -y cygwin microsoft-windows-terminal

You can also add cygwin to your path to use linux commands in cmd or powershell:

    setx /M PATH "$env:PATH;C:\tools\cygwin\bin"

All of those steps should finish within a few minutes.
You will want to use either `cmd` or `cygwin` (bash) in `terminal` thereafter.
Terminal enables proper utf8 support in both of those shells,
and is the future of Windows console/terminal apps.

Now install the languages you want (remove what you don't from here):

    choco upgrade -y ruby python nodejs jdk11 dotnetcore-sdk golang rustup.install # or ojdkbuild instead of jdk11

You can read about these packages on chocolatey's website, e.g. https://chocolatey.org/packages/python

You might also want VS Code, or other IDEs like Jetbrains' IntelliJ, RubyMine, PyCharm, or WebStorm, etc:

    choco upgrade -y vscode intellijidea-community rubymine pycharm-community webstorm

If you want C or C++, you probably want full Visual Studio:

    choco upgrade -y visualstudio2019community

For the record, I personally actively avoid installing any languages into my windows os itself.
I do use IDEs in Windows, mostly use VS Code, and sometimes IntelliJ Rider since I like C#.
I just use the IDE to edit code on my windows filesystem that I then run inside VMs per below.

You will likely want to install docker for windows at this point,
if you aren't going to install virtualbox or vagrant as below:
https://docs.docker.com/docker-for-windows/install/

### 2. Use a VM desktop

Running Linux directly in Virtualbox, if you are familiar with Linux,
is an easy way to get everything you want in a contained environment,
with the ability to copy files back to the host OS as desired --
even via drag and drop!

...[Unless you have Docker for Windows installed!](
https://nickjanetakis.com/blog/docker-tip-13-get-docker-for-windows-and-virtualbox-working-together)
Because Docker for Windows conflicts with Virtualbox for now.
(Technically you can run both with config changes, with a performance hit on vbox, but it's complicated.)

    choco upgrade -y virtualbox

Now you need a Linux OS to install inside vbox. The popular choice is Ubuntu, I like Xubuntu or Elementary variants:

https://ubuntu.com/download/desktop # common but bloated and somewhat unconventional UI \
https://xubuntu.org/ # minimalist, classic gnome style UI \
https://elementary.io/ # very pretty, OS X-like, but some notable differences from upstream

Download a desktop image ISO for one of those, run Virtualbox and create a new VM using that ISO.

You now have a fully functioning linux desktop os (guest) inside your windows os (host) and can use it like a normal linux box. Install software using apt-get, snap, wget, etc. That said, I recommend installing everything via asdf:

https://asdf-vm.com/#/core-manage-asdf-vm?id=install

And that has all the languages you could want to use.
This is my own script I use for setting up a new ubuntu box for myself,
which uses asdf for a variety of languages:

https://github.com/rogusdev/vagrants/blob/master/all/setup_ubuntu.sh

This is how did I 100% remote work across the world for 7 years, plus a smart phone with ssh (android only).

Notice that you can run docker inside a vm in virtualbox inside windows.

With this option you will likely want all your IDEs and tools to live inside the guest os
and not need to install editors or anything in your windows host.

### 3. Other options

Basically all of these will benefit from installing chocolatey and terminal
as in the "Install Directly" section above, so do that first.
Just don't install any of the languages via choco.

#### WSL2

Windows Subsystem for Linux version 2 is a complete Linux kernel running inside Windows!

You need win20 2004 or later, and then to enable a variety of features:

https://docs.microsoft.com/en-us/windows/wsl/install-win10

With this, you can install the linux distro of your choice from the windows app store
and then, inside that, all the languages and tools that you want using apt, yum, etc.

Windows Terminal should have an option to run a WSL2 shell once both are installed,
if you click the down arrow to the right of the plus arrow to the right of all tabs in Terminal.

WSL2 is a special VM running inside windows that allows you to run a full linux OS inside it.
It is similar to Virtualbox, and therefore you cannot install both WSL2 and Virtualbox at the same time.
(Specifically you cannot enable the features necessary for WSL2 at the same time as virtualbox.)

WSL2 can be installed at the same time as Docker for Windows however, and then docker will run inside WSL2.
(Technically docker inside WSL2 runs inside windows, but it's close enough.)

[Setting up WSL2 to run Docker](https://docs.docker.com/docker-for-windows/wsl/)

#### Docker

With Docker for Windows, you can run any docker vm you want, and do whatever you want inside that.
No need to install WSL, Virtualbox, or anything else, docker is completely self contained.
Docker containers are perhaps dangerously disposable however, so you should know what you are doing
if you are trying to do development inside of one, but it is possible:

https://docs.docker.com/docker-for-windows/install/

Then you can run these just in cmd/powershell in Windows Terminal:

    docker run --rm -it ruby /bin/bash
    docker run --rm -it python /bin/bash
    docker run --rm -it node /bin/bash
    docker run --rm -it openjdk /bin/bash
    docker run --rm -it mcr.microsoft.com/dotnet/core/sdk /bin/bash
    docker run --rm -it golang /bin/bash
    docker run --rm -it rust /bin/bash

To edit file inside a docker container you need to mount a volume pointing to a directory,
but all such configuration should be investigated on your own if you choose this approach.

#### Vagrant

Install vagrant, using virtualbox as your vm provider:

    choco upgrade -y virtualbox vagrant

Then start a new vagrant vm and connect to it:

    vagrant init bento/ubuntu-18.04
    vagrant up && vagrant ssh

Now you can install all the languages in Linux like normal (same as virtualbox above).
You might want to operate inside `/vagrant` as this is the folder the vagrant init started in,
shared between Windows and Vagrant's VM (your Ubuntu box in the example above).

However... windows filesystems are slightly different from linux filesystems
(notably: symlinks and file perms ala chmod are... different)
so this requires some nontrivial effort to get it working smoothly.

That said, this is what I normally do these days.
I also like destroying and rebuilding VMs frequently, so that's part of the motivation.
I want my environments to be inherently and quickly repropducible by others.

#### Cygwin

Install cygwin via chocolatey as above in "install directly".
You should now have "Cygwin64 Terminal" in your start menu.
You will need to install the packages you need from cygwin setup by running this from your start menu:

    C:\tools\cygwin\cygwinsetup.exe

Select packages with View: "Not Installed" and Search: the language of choice, e.g. "python3", "ruby", etc.

Now in windows terminal you can open a cygwin bash shell, and in that run the languages of your choice.
You might want to move your bash shell to a directory on your normal windows filesystem such as

    cd /cygdrive/c/Users/USERNAME/Desktop

Your cygwin home dir is still on your windows filesystem though, by default in:

    C:\tools\cygwin\home\USERNAME

Cygwin does not have the full set of linux tools available, but it does have a great many of them.
And they are all running natively in windows, no virtual machine involved.

#### MinGW / MSYS

MinGW is a gcc clone C/C++ compiler, which then enables MSYS/MSYS2
which are rebuilt binaries of common GNU tools for windows.

    choco upgrade -y mingw msys2

This is similar to cygwin, but slightly more native.

http://www.mingw.org/

## A note about networking

If you are running a VM (virtualbox, vagrant, wsl2, docker), your host os (windows)
might have some difficulty reaching websites (IPs) inside the VM e.g. localhost (127.0.0.1).
There are ways around this, but it might involve modifying hosts, vagrant config, etc.

Be aware that if you open your windows browser to http://localhost:8080
expecting to see the web server running inside your VM, it might take some work to get there.
Inside the VM curl should work just fine, as will a browser inside a vbox desktop gui.

Similarly file system access can get a bit complicated inside these, but
they all have ways of accessing guest and host fs from each other, once configured properly.

## Conclusion

All of these options work, they just offer different experiences in certain ways.
Find the one that fits you and work with that. :)

Good luck, have fun! ;)
