# 🐧 Ubuntu Developer Setup for Beginners

This is a step-by-step guide for setting up a fresh Ubuntu-based development environment from scratch.  
It is aimed at **beginner developers**, and covers everything from OS installation and essential tools to language runtimes, IDEs, and developer utilities.

✅ Suitable for Ubuntu 22.04 and 24.04  
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
11. [Neovim Configuration](#11-neovim-configuration)
12. [WezTerm Installation](#12-wezterm-installation)
13. [Installing IDEs](#13-installing-ides)
14. [Version Control Tools](#14-version-control-tools)
15. [API Testing Tools](#15-api-testing-tools)
16. [Wireshark Installation](#16-wireshark-installation)
17. [pgAdmin Installation](#17-pgadmin-installation)
18. [Docker and Docker Compose](#18-docker-and-docker-compose)
19. [VirtualBox Installation](#19-virtualbox-installation)
20. [STM32F4xx Development Tools](#20-stm32f4xx-development-tools)
21. [Leptos Setup](#21-leptos-setup)
22. [Tauri Setup](#22-tauri-setup)
23. [Removing UFW and iptables](#23-removing-ufw-and-iptables)
24. [nftables Firewall Configuration](#24-nftables-firewall-configuration)

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
chsh -s $(which zsh)  # Optional
```

---

## 5. C/C++ Development Tools

```bash
sudo apt install -y gcc g++ gdb make cmake
sudo apt install -y clang clang-format clang-tidy valgrind cppcheck
sudo apt install -y libboost-all-dev  # Optional
```

---

## 6. Python Setup

```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev python3-setuptools
pip3 install black flake8 mypy pytest
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
sudo update-alternatives --config python
pip3 install jupyter  # Optional
```

---

## 7. Rust Installation

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
rustup target add thumbv6m-none-eabi thumbv7em-none-eabihf
cargo install cargo-watch cargo-audit rustfmt clippy
```

---

## 8. Go Installation

```bash
sudo apt install -y golang
```

---

## 9. Node.js and Yarn Setup

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install -y yarn

# Optional: NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install node
```

---

## 10. PostgreSQL Server Setup

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql

# Optional: Create user and database
sudo -u postgres psql -c "CREATE USER myuser WITH PASSWORD 'mypassword';"
sudo -u postgres psql -c "CREATE DATABASE mydb OWNER myuser;"
```

---

## 11. Neovim Configuration

For a better and beginner-friendly setup, follow this guide:  
👉 [How to Setup Neovim (2024) by Josean](https://www.josean.com/posts/how-to-setup-neovim-2024)

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

## 12. WezTerm Installation

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:wez/ppa
sudo apt update
sudo apt install -y wezterm
```

More configuration tips:  
👉 [Josean’s WezTerm Guide (macOS-focused, but similar)](https://www.josean.com/posts/how-to-setup-wezterm-terminal)

---

## 13. Installing IDEs

```bash
sudo snap install --classic code
sudo snap install pycharm-community --classic

wget -O rustrover.tar.gz "https://download.jetbrains.com/rustrover/RustRover-2024.2.tar.gz"
tar -xzf rustrover.tar.gz
./RustRover-*/bin/rustrover.sh

wget -O jetbrains-toolbox.tar.gz "https://download.jetbrains.com/toolbox/jetbrains-toolbox-2.1.3.18901.tar.gz"
tar -xzf jetbrains-toolbox.tar.gz
./jetbrains-toolbox-*/jetbrains-toolbox
```

---

## 14. Version Control Tools

```bash
wget https://release.gitkraken.com/linux/gitkraken-amd64.deb
sudo dpkg -i gitkraken-amd64.deb
sudo apt install -f

sudo apt install -y tig
```

---

## 15. API Testing Tools

```bash
sudo snap install postman
sudo apt install -y httpie
```

---

## 16. Wireshark Installation

```bash
sudo apt install -y wireshark
```

---

## 17. pgAdmin Installation

```bash
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" | sudo tee /etc/apt/sources.list.d/pgadmin4.list
sudo apt update
sudo apt install -y pgadmin4
```

---

## 18. Docker and Docker Compose

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER

# Optional: Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

---

## 19. VirtualBox Installation

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)

wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list

sudo apt update
sudo apt install -y virtualbox-7.0

wget https://download.virtualbox.org/virtualbox/7.0.12/Oracle_VM_VirtualBox_Extension_Pack-7.0.12.vbox-extpack
sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-7.0.12.vbox-extpack

sudo usermod -aG vboxusers $USER
sudo reboot
```

---

## 20. STM32F4xx Development Tools

```bash
sudo apt update
sudo apt install -y gcc-arm-none-eabi gdb-arm-none-eabi openocd gdb-multiarc
sudo apt install -y qemu-system-arm
cargo install cargo-flash
cargo install cargo-embed
sudo apt install -y stlink-tools
sudo usermod -a -G dialout $USER
```

---

## 21. Leptos Setup

```bash
cargo install cargo-leptos
```

---

## 22. Tauri Setup

```bash
npm install -g @tauri-apps/cli
```

---

## 23. Removing UFW and iptables

```bash
sudo ufw disable
sudo apt remove --purge ufw
sudo iptables -F
sudo iptables -X
sudo iptables -Z
sudo apt remove --purge iptables
```

---

## 24. nftables Firewall Configuration

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
    }
    chain output {
        type filter hook output priority 0; policy drop;
        oifname "lo" accept
        tcp dport 53 accept
        tcp dport 80 accept
        tcp dport 443 accept
        icmp type echo-request accept
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
