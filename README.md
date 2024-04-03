# Windows Setup

## Start

Copy over SSH keys

Install [Firefox](https://www.mozilla.org/en-US/firefox/new)

Install [Git](https://git-scm.com/download/win). Make sure to install Windows-style checkout

Clone this repository

## Fonts

Install the _Lilex_ nerd font by extracting it, selecting all `.ttf` files, right cling and select `Install`

## Install Linux subsystem

Open Powershell and run

```bash
wsl --install
```

Then reboot your device. You will then get a command prompt where you can setup your Linux account.

Exit the Linux prompt and open the usuall Windows Terminal instead. 
Select the dropdown menu in the navbar and select _Settings_.

Select _Ubuntu_ as default profile (The one with the Linux symbol and not the Ubuntu symbol).

Select _Windows Terminal_ as Default terminal application.

Go to the Ubuntu/Linux profile.

Go to _Appearance_ and select the _One Half Dark_ colorscheme.

Select the Font Face _Lilex Nerd Font Mono_

Set background transparency to 95\%

Go to _Advanced_ and deselect all _Bell Notification Styles_.

Go through the other profiles and hide the undesired one from the dropdown menu

If you need to be able to ssh into the devices WSL instance, you should also go to _Startup_ and 
enable _Launch on machine startup_

## Terminal behaviour

### Setup Git script

Make a new script called `~/bin/git`

Add the following to it

```bash
#!/usr/bin/bash
# WSL 'git' wrapper

# Check real current path
REALPATH=`readlink -f ${PWD}`
if [ "${REALPATH:0:5}" == "/mnt/" ]; then
	# Change this path to the Windows Git CMD path
	/mnt/c/Program\ Files/Git/cmd/git.exe "$@"
else
	/usr/bin/git "$@"
fi
```

Make it executable

```bash
chmod +x ~/bin/git
```

This script will make sure to use the Windows version of git when standing in a 
Windows directory, or the Linux version when standing in a WSL directory
 
### Unbind CTRL+V for VIM integration

Open a terminal and go to settings. Under the _Actions_ tab you can select _Open JSON file_. 
In there, find the key _actions_, and paste the following as an extra action.

```json
{
	"_comment": "Unbind CTRL+V for VIM integration",
	"command": "unbound",
	"keys": "ctrl+v"
}
```

### Install and configure NeoVim

Follow [Neovim Setup](https://github.com/JosefUtbult/neovim-config)

### Setup ZSH

Follow [Zsh Setup](https://github.com/JosefUtbult/Zsh-Setup)

### Make some changes to the config

Open `.zshrc` and add the following to the bottom

```bash
# Remap git to custom script by prioritizing the bin folder in HOME
export PATH=$HOME/bin:$PATH

# Fix WSL coloring of Windows folders
export LS_COLORS='ow=01;36;40'

# Remap Vim and Vi to NeoVim
alias vim="nvim"
alias vi="nvim"
alias oldvim="vim"
```

## Remote Desktop

If you need to be able to access the computer over Remote Desktop, 
go to Settings > System > Remote Desktop and toggle on _Remote Desktop_

## SSH Server

Install OpenSSH Server on windows. Go to `Settings > Apps > Optional features > Add an optional feature`.
Search for `OpenSSH Server` and install it.

Open the start menu and open the application `Services`

Find `OpenSSH Server` in the list.

Begin with setting the startup type to `Automatic`

Click on `Start`

Do the same for `OpenSSH Autentication Agent`

Now you want to set a default SSH shell. If you indent to use Cygwin and WSL,
you will need to set this to Powershell, as network drives doesn't work when
running Cygwin from WSL.

Open Powershell and run

```bash
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
```

If you instead just want WSL, you can run the following

```bash
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "c:\Program Files\WSL\wsl.exe" -PropertyType String -Force
```

Now for setting up authorized keys. As your user is an admin, you need to add your clients public key to
`C:\ProgramData\ssh\administrators_authorized_keys`

Also, open `C:\ProgramData\ssh\sshd_config` and find the line `Match Group administrators`. On the line above that, add the following

```
StrictModes no
UsePrivilegeSeparation no
```

Try rebooting your computer and make sure in `Services` that OpenSSH Server is running.

Now you can try logging into the computer over SSH. Use the Windows user and computer name.

In the SSH session, try accessing WSL

```powershell
& 'C:\Program Files\WSL\wsl.exe'
```

For some reason, you can only run certain instances of the `wsl.exe` executable over SSH. For this reason, you need to make sure that the `wsl`
command in Powershell runs the one in `C:\Program Files\WSL`. Therefore, add that path to the environment PATH and move it all the way to the 
top of the list. This path will then be prioritized.

When you are in the environment editor for PATH, make sure to also add `C:\cygwin64`, to make the Cygwin bat script accessible from the terminal.

After this you should be able to log in over SSH and run just `wsl` to start WSL.

## Power Toys

Install [Microsoft Power Toys](https://apps.microsoft.com/store/detail/XP89DCGQ3K6VLD?ocid=pdpshare)

You can use _Keyboard Manager_ to remap keys.

If you have the machine as a remote desktop you might want to remap right control to the windows button.

You also want to remap caps lock to escape (on the non remote desktop machine)
