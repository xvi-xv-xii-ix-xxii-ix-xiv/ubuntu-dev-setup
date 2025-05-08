# 🐧 Ubuntu Developer Setup for Beginners

This is a step-by-step guide for setting up a fresh Ubuntu-based development environment from scratch.
It is aimed at **beginner developers**, and covers everything from OS installation and essential tools to language runtimes, IDEs, and developer utilities.

📅 Suitable for Ubuntu 22.04 and 24.04
💡 Designed for software engineers, system programmers, and hobbyists starting with Linux development.

---

## 📚 Table of Contents

1. [Flashing Ubuntu ISO to USB (on macOS)](#1-flashing-ubuntu-iso-to-usb-on-macos)
2. [System Update](#2-system-update)
3. [Essential Tools Installation](#3-essential-tools-installation)
4. [Installing Zsh and Oh My Zsh](#4-installing-zsh-and-oh-my-zsh)
5. [C/C++ Development Tools](#5-cc-development-tools)
6. [Python Setup](#6-python-setup)
7. [Rust Installation](#7-rust-installation)
8. [Go Installation](#8-go-installation)
9. [Node.js and Yarn Setup](#9-nodejs-and-yarn-setup)
10. [PostgreSQL Server Setup](#10-postgresql-server-setup)
11. [Font Setup for Terminal/Neovim](#11-font-setup-for-terminalneovim)
12. [Neovim Configuration](#12-neovim-configuration)
13. [WezTerm Installation](#13-wezterm-installation)
14. [Installing IDEs](#14-installing-ides)
15. [Version Control Tools](#15-version-control-tools)
16. [API Testing Tools](#16-api-testing-tools)
17. [Wireshark Installation](#17-wireshark-installation)
18. [pgAdmin Installation](#18-pgadmin-installation)
19. [Docker and Docker Compose](#19-docker-and-docker-compose)
20. [VirtualBox Installation](#20-virtualbox-installation)
21. [STM32 Development Tools](#21-stm32-development-tools)
22. [Leptos Setup](#22-leptos-setup)
23. [Tauri Setup](#23-tauri-setup)
24. [OpenSSH and SSH Key Setup](#24-openssh-and-ssh-key-setup)
25. [System Cleanup & Maintenance](#25-system-cleanup--maintenance)
26. [Removing UFW and iptables](#26-removing-ufw-and-iptables)
27. [nftables Firewall Configuration](#27-nftables-firewall-configuration)

---

## 1. Flashing Ubuntu ISO to USB (on macOS)

```bash
diskutil list
diskutil unmountDisk /dev/diskX
sudo dd if=Downloads/ubuntu-24.04.2-desktop-amd64.iso of=/dev/diskX bs=1m
diskutil eject /dev/diskX
```

---

## 2. System Update

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 3. Essential Tools Installation

```bash
sudo apt install -y build-essential curl wget git unzip zip htop net-tools jq
sudo apt install -y printer-driver-all cups cups-pdf sane xsane
```

---

## 4. Installing Zsh and Oh My Zsh

```bash
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
chsh -s $(which zsh)

# Popular plugins
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Powerlevel10k theme
git clone --depth=1 https://github.com/romkatv/powerlevel10k ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k

# Example ~/.zshrc config:
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
ZSH_THEME="powerlevel10k/powerlevel10k"
```

---

## 6. Python Setup

```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev python3-setuptools
pip3 install black flake8 mypy pytest
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
sudo update-alternatives --config python
pip3 install jupyter  # Optional

# Install pyenv
curl https://pyenv.run | bash

# Add to ~/.zshrc:
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
exec $SHELL

# Usage example:
pyenv install 3.11.5
pyenv global 3.11.5
```

---

## 11. Font Setup for Terminal/Neovim

```bash
mkdir -p ~/.local/share/fonts
cd ~/.local/share/fonts
curl -fLo "Fira Code Regular Nerd Font Complete.ttf" "https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Regular/complete/Fira%20Code%20Regular%20Nerd%20Font%20Complete.ttf"
fc-cache -fv
```

---

## 12. Neovim Configuration

For Neovim installation, beginners are recommended to first review [this detailed guide](https://www.josean.com/posts/how-to-setup-neovim-2024).

```bash
sudo apt install -y neovim

# Optional: Plugin manager
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs      https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

# Optional: Minimal init.vim
mkdir -p ~/.config/nvim
cat <<EOF > ~/.config/nvim/init.vim
call plug#begin()
Plug 'tpope/vim-sensible'
Plug 'morhetz/gruvbox'
call plug#end()

colorscheme gruvbox
set number
EOF
```

---

## 13. WezTerm Installation

```bash
sudo apt install -y wget unzip

wget https://github.com/wez/wezterm/releases/download/20240203-110809/wezterm-20240203-110809-Ubuntu22.04.AppImage
chmod +x wezterm-*.AppImage
./wezterm-*.AppImage
```

## For additional configuration, [refer to the WezTerm setup guide](https://www.josean.com/posts/how-to-setup-wezterm-terminal) — written for macOS but generally applicable.

## 15. Version Control Tools

```bash
wget https://release.gitkraken.com/linux/gitkraken-amd64.deb
sudo dpkg -i gitkraken-amd64.deb
sudo apt install -f

sudo apt install -y tig

# Git configuration
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor nvim
git config --global init.defaultBranch main

# Global .gitignore
curl https://raw.githubusercontent.com/github/gitignore/main/Global/Linux.gitignore >> ~/.gitignore
git config --global core.excludesfile ~/.gitignore
```

---

## 24. OpenSSH and SSH Key Setup

```bash
sudo apt install -y openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh

# Generate SSH keys
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

---

## 25. System Cleanup & Maintenance

```bash
# Monitoring and system utilities
sudo apt install -y glances bpytop neofetch
sudo apt install -y gnome-disk-utility udisks2
sudo apt install -y nmap tcpdump mtr traceroute

# Configure automatic updates
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades

# Cleanup
sudo apt autoremove -y
sudo apt clean
sudo journalctl --vacuum-time=1d
```

---

## 27. nftables Firewall Configuration

```bash
sudo apt update
sudo apt install nftables
sudo systemctl enable nftables
sudo systemctl start nftables

sudo nano /etc/nftables.conf
```

Example config:

```nft
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        iifname "lo" accept
        tcp dport 22 accept
        icmp type echo-request accept

        ct state established,related accept
    }
    chain output {
        type filter hook output priority 0; policy drop;
        oifname "lo" accept
        tcp dport 53 accept
        tcp dport 80 accept
        tcp dport 443 accept
        icmp type echo-request accept

        udp dport 53 accept
    }
    chain forward {
        type filter hook forward priority 0; policy drop;
    }
}
```

```bash
sudo nft -f /etc/nftables.conf
sudo systemctl restart nftables
```

---

Happy coding! 🚀
