# Developing on a Windows machine

Sadly, many devs think that real software development on Windows is either an oxymoron
or just for finance companies paying tons of money for MSDN licenses.

This is wildly untrue: I do all of my personal dev (which is extensive) on a Windows machine.

In the past, all my dev was in a Linux desktop/GUI OS in Virtualbox
but Windows itself has come a long, long way just in the past year and some.

(These days I do all my dev inside vagrant in virtualbox, using cygwin bash in windows terminal
-- usually with docker apps running inside all of that. But I'm excited for
[WSL2](https://www.hanselman.com/blog/HowToSetUpDockerWithinWindowsSystemForLinuxWSL2OnWindows10.aspx) to come out!)

## Windows dev options

Running Linux directly in Virtualbox, if you are familiar with Linux,
is the easiest ([unless you have Docker for Windows installed!](
https://nickjanetakis.com/blog/docker-tip-13-get-docker-for-windows-and-virtualbox-working-together)),
but also the most effortful and the biggest shift of environment.

Otherwise you will be running in the Windows environment so
you will always want to install the new Microsoft Windows Terminal application,
almost certainly via Chocolatey (if you don't already have that):

### Install Chocolatey

https://chocolatey.org/install

Run Powershell as Administrator and in that:

    Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

### Install additional tools:

Research each of these if you are unfamiliar -- and I highly recommend you become familiar with all of them!

    choco upgrade -y microsoft-windows-terminal cygwin virtualbox vagrant

You might also want to install VS Code, or Jetbrains' IntelliJ, RubyMine, PyCharm, or WebStorm -- or any other IDEs of your choice.
For example:

    choco upgrade -y vscode intellijidea-community rubymine pycharm-community webstorm

## Setting up Windows dev envs

There are 6 main options, installation instructions follow -- make sure you can run a basic Hello World app in each:

#### 1. In Windows itself:

    choco upgrade -y ruby python nodejs ojdkbuild dotnetcore-sdk golang

Then you can run those languages in a cmd/powershell shell in Windows Terminal.

#### 2. In Cygwin bash:

After installing cygwin via choco above, you should now have "Cygwin64 Terminal" in your start menu.
You will need to install the packages you need from cygwin setup by running this from your start menu:

    C:\tools\cygwin\cygwinsetup.exe

Select packages with View: "Not Installed" and Search: the language of choice, e.g. "python3", "ruby", etc.

#### 3. In WSL, Windows Subsystem for Linux:

Follow the Microsoft instructions to install WSL -- a complete Linux kernel running inside Windows!

https://docs.microsoft.com/en-us/windows/wsl/install-win10

With this, you can install the distro of your choice and all the languages + tools inside of that that you want.

Windows Terminal should have an option to run a WSL shell once both are installed,
if you click the down arrow to the right of the plus arrow to the right of all tabs in Terminal.

#### 4. In Vagrant, leveraging Virtualbox:

Install Vagrant + Virtualbox via Chocolatey as above. Then start a new vagrant vm and connect to it:

    vagrant init bento/ubuntu-18.04
    vagrant up && vagrant ssh

Now you can install all the languages in Linux like normal (same as in WSL above).
You might want to operate inside `/vagrant` as this is the folder the vagrant init started in,
shared between Windows and Vagrant's VM (your Ubuntu box in the example above).

#### 5. In Virtualbox, with a Linux GUI:

Install Vagrant + Virtualbox via Chocolatey as above.
Then download an ISO of the desktop distro of your choice from their website.
(Ubuntu is the most common and most documented/supported: https://ubuntu.com/download/desktop )

Create a new virtual machine in virtualbox using that ISO,
install the OS like normal, and then install the languages you want per the norm in that distro
(apt/snap in debian/ubuntu, yum in redhat/centos/fedora, apk in alpine, etc)

Again, this is yet another Linux install, so the same as WSL and Vagrant versions,
only now you have a complete OS in Linux with GUI for IDEs and everything.

#### 6. In Docker, in wherever you can run Docker:

If you have Docker for Windows installed, you can run these just in cmd/powershell in Windows Terminal:

    docker run --rm -it ruby /bin/bash
    docker run --rm -it python /bin/bash
    docker run --rm -it node /bin/bash
    docker run --rm -it openjdk /bin/bash
    docker run --rm -it mcr.microsoft.com/dotnet/core/sdk /bin/bash
    docker run --rm -it golang /bin/bash
    docker run --rm -it rust /bin/bash

You can also run them inside vagrant/virtualbox, and
[there is a Docker version that uses WSL2](https://docs.docker.com/docker-for-windows/wsl/).
