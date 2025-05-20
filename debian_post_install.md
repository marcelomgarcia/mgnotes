# Debian 12 Post Installation

## Sudo

Adding user to `sudo` group is not enough, but it's the first step

```
root@arda:~# usermod -aG sudo mgarcia
```

To edit the `sudoers` file we will install `vim` because `nano` is horrible

```
root@arda:~# apt install vim
root@arda:~# update-alternatives --config editor
(...)
Press <enter> to keep the current choice[*], or type selection number: 3
(...)
root@arda:~# visudo
```

## Jetbrains Mono

Download [Jetbrains mono](https://www.jetbrains.com/lp/mono/) and extract the _zip_ file

```
mgarcia@arda:~$ unzip JetBrainsMono-2.304.zip -d ~/.local/share/fonts
```

## Gnome Extensions

```
mgarcia@arda:~$ sudo apt install gnome-shell-extension-manager -y
```

## Build Essentias

Install development environment

```
mgarcia@arda:~$ sudo apt install build-essential dkms linux-headers-$(uname -r)
```


## restricted-extras

Codes for multimedia

```
sudo apt install ttf-mscorefonts-installer rar unrar libavcodec-extra gstreamer1.0-libav gstreamer1.0-plugins-ugly gstreamer1.0-vaapi
```

## Microsoft Fonts Compatibility

```
sudo apt install fonts-crosextra-carlito fonts-crosextra-caladea
```

## Enable GNOME Extensions

Visit the URL and enable the extension

```
https://extensions.gnome.org/
```


## Extra packages

```
mgarcia@arda:~$ sudo apt install curl tree ncal 
mgarcia@arda:~$ sudo apt install zip
mgarcia@arda:~$ sudo apt install git
mgarcia@arda:~$ sudo apt install python-is-python3
mgarcia@arda:~$ sudo apt install python3-venv
mgarcia@arda:~$ sudo apt install vlc
mgarcia@arda:~$ sudo apt install keepassxc
mgarcia@arda:~$ sudo apt install tmux
```


## VScode

Download [Visual Studio Code](https://code.visualstudio.com/):

```
mgarcia@arda:~/Downloads$ sudo apt install ./code_1.92.2-1723660989_amd64.deb 
```

## Virtualbox

Download [virtualbox](https://www.virtualbox.org/wiki/Downloads):

```
mgarcia@arda:~/Downloads$ sudo apt install ./virtualbox-7.0_7.0.20-163906~Debian~bookworm_amd64.deb 
```

## Vagrant

Install [Vagrant via Debian repository](https://developer.hashicorp.com/vagrant/install?product_intent=vagrant#linux):

```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vagrant
```

## Docker Engine

Installing [Docker Engine via apt repository](https://docs.docker.com/engine/install/debian/#install-using-the-repository) 

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Testing installation

```
mgarcia@arda:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
(...)
```

Without root privileges

```
mgarcia@arda:~$ grep docker /etc/group
docker:x:994:
mgarcia@arda:~$ sudo usermod -aG docker $USER
mgarcia@arda:~$ newgrp docker
mgarcia@arda:~$ 
mgarcia@arda:~$ docker run hello-world

Hello from Docker!
(...)
```

# Configuration Files

Some aliases

```
mgarcia@arda:~$ cat .bash_aliases
alias ll='ls -lh'
alias lll='ll -tr'
alias lls='ll -Sr'
alias tnew='tmux new -s ARDA'
alias ta='tmux a -d'

mgarcia@arda:~$ 
```

Vim config

```
mgarcia@arda:~$ cat .vimrc 
syntax on
set tabstop=4
set softtabstop=4
set shiftwidth=4
set autoindent
set smartindent
set expandtab
mgarcia@arda:~$
```

A SSH config 

```
mgarcia@arda:~$ head .ssh/config
Host *
    ControlMaster auto
    ControlPath ~/.ssh/master-%r@%h:%p.socket
    ControlMaster auto
    ControlPersist 10m

Host valinor
    Hostname 10.64.75.53
```

Tmux config file

```
# remap prefix from 'C-b' to 'C-a'
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# reload config file (change file location to your the tmux.conf you want to use)
bind r source-file ~/.tmux.conf

# switch panes using Alt-arrow without prefix
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Useful bindings
bind ^ command-prompt -p "joinp>" "join-pane -s %1"
bind / command-prompt -p "man>" "split-window -h -l 80 'exec man %%'"
bind S command-prompt -p "ssh>" "new-window -n %1 'ssh %1'"
bind m swapp -s 0 -t 1
bind e attach -d
bind C-a last-window

# don't rename windows automatically
set-option -g allow-rename off

# VI binging
set -g mode-keys vi

# Environment
set -g update-environment "DISPLAY WINDOWID SSH_ASKPASS SSH_AUTH_SOCK SSH_AGENT_PID SSH_CONNECTION"
```

## Git Config

[Initial config](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)

```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
# To be politically correct and use "main" instead of "master" 
$ git config --global init.defaultBranch main
```

# Reference

```
https://averagelinuxuser.com/debian-11-after-install/
```
