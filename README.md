# Ubuntu Developer Setup for Beginners

This guide provides a step-by-step process for setting up a comprehensive development environment on Ubuntu, tailored for beginner developers. It covers everything from installing the operating system to configuring essential tools, programming languages, IDEs, and utilities, ensuring a robust setup for software development.

✅ Suitable for Ubuntu 22.04 and 24.04  
💡 Designed for software engineers, system programmers, and hobbyists starting with Linux development.

## Table of Contents

1. [Flashing Ubuntu ISO to USB (on macOS)](#flashing-ubuntu-iso-to-usb-on-macos)
2. [System Update](#system-update)
3. [Essential Tools Installation](#essential-tools-installation)
4. [Installing Zsh and Oh My Zsh](#installing-zsh-and-oh-my-zsh)
5. [C/C++ Development Tools](#cc-development-tools)
6. [Python Setup](#python-setup)
7. [Rust Installation](#rust-installation)
8. [Go Installation](#go-installation)
9. [Node.js and Yarn Setup](#nodejs-and-yarn-setup)
10. [PostgreSQL Server Setup](#postgresql-server-setup)
11. [Neovim Configuration](#neovim-configuration)
12. [WezTerm Installation](#wezterm-installation)
13. [Installing IDEs](#installing-ides)
14. [Version Control Tools](#version-control-tools)
15. [API Testing Tools](#api-testing-tools)
16. [Wireshark Installation](#wireshark-installation)
17. [pgAdmin Installation](#pgadmin-installation)
18. [Docker and Docker Compose](#docker-and-docker-compose)
19. [VirtualBox Installation](#virtualbox-installation)
20. [STM32F4xx Development Tools](#stm32f4xx-development-tools)
21. [Leptos Setup](#leptos-setup)
22. [Tauri Setup](#tauri-setup)
23. [Removing UFW and iptables](#removing-ufw-and-iptables)
24. [nftables Firewall Configuration](#nftables-firewall-configuration)
25. [SSH Configuration](#ssh-configuration)
26. [Generating SSH Keys](#generating-ssh-keys)
27. [Additional Tools and Utilities](#additional-tools-and-utilities)

---

## Flashing Ubuntu ISO to USB (on macOS)

```bash
diskutil list
diskutil unmountDisk /dev/diskX
sudo dd if=Downloads/ubuntu-24.04.2-desktop-amd64.iso of=/dev/diskX bs=1m
diskutil eject /dev/diskX
```

> **Note**: Replace `/dev/diskX` with the correct disk identifier for your USB drive, found using `diskutil list`. Be cautious, as `dd` can overwrite data if the wrong device is specified.

---

## System Update

```bash
sudo apt update && sudo apt upgrade -y
```

> **Explanation**: This command updates the package lists and upgrades all installed packages to their latest versions, ensuring your system is secure and up-to-date.

---

## Essential Tools Installation

```bash
sudo apt install -y build-essential curl wget git unzip zip htop net-tools jq
sudo apt install -y printer-driver-all cups cups-pdf sane xsane
sudo apt install -y glances bpytop neofetch
sudo apt install -y gnome-disk-utility udisks2
sudo apt install -y nmap tcpdump mtr traceroute
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
sudo apt autoremove -y
sudo apt clean
sudo journalctl --vacuum-time=1d
```

> **Comment**: These commands install essential development and system monitoring tools (`build-essential`, `htop`, `neofetch`), printing utilities (`cups`, `sane`), network tools (`nmap`, `tcpdump`), and configure automatic security updates (`unattended-upgrades`). The cleanup commands remove unnecessary packages and logs to free up space.

---

## Installing Zsh and Oh My Zsh

```bash
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
chsh -s $(which zsh) # Optional
```

> **Note**: Zsh is a powerful shell alternative to Bash. Oh My Zsh enhances it with plugins and themes. Setting Zsh as the default shell (`chsh`) is optional but recommended for a better experience.

### Popular Plugins

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

> **Explanation**: The `zsh-autosuggestions` plugin provides real-time command suggestions based on your history, improving productivity.

### Powerlevel10k Theme

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

> **Comment**: Powerlevel10k is a highly customizable and fast Zsh theme. Run `p10k configure` after installation to customize its appearance.

### Sample ~/.zshrc

```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
ZSH_THEME="powerlevel10k/powerlevel10k"
```

> **Note**: This minimal `.zshrc` configuration enables useful plugins and sets the Powerlevel10k theme. Add `zsh-syntax-highlighting` by cloning its repository if needed.

---

## C/C++ Development Tools

```bash
sudo apt install -y gcc g++ gdb make cmake
sudo apt install -y clang clang-format clang-tidy valgrind cppcheck
sudo apt install -y libboost-all-dev # Optional
```

> **Explanation**: Installs compilers (`gcc`, `g++`), debugger (`gdb`), build tools (`make`, `cmake`), and code analysis tools (`clang-tidy`, `valgrind`). `libboost-all-dev` is optional for projects requiring the Boost library.

---

## Python Setup

```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev python3-setuptools
pip3 install black flake8 mypy pytest
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
sudo update-alternatives --config python
pip3 install jupyter # Optional
curl https://pyenv.run | bash
```

> **Comment**: This sets up Python 3 with essential tools for formatting (`black`), linting (`flake8`), type checking (`mypy`), and testing (`pytest`). `pyenv` allows managing multiple Python versions.

### Add to ~/.zshrc

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
exec $SHELL
```

> **Note**: These lines configure `pyenv` to manage Python versions and ensure it’s available in your shell.

### Example Usage

```bash
pyenv install 3.11.5
pyenv global 3.11.5
```

> **Explanation**: Installs Python 3.11.5 and sets it as the global version for your system.

---

## Rust Installation

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
rustup target add thumbv6m-none-eabi thumbv7em-none-eabihf
cargo install cargo-watch cargo-audit rustfmt clippy
```

> **Comment**: Installs Rust via `rustup`, adds embedded targets for microcontrollers, and installs tools for development (`cargo-watch`, `cargo-audit`, `rustfmt`, `clippy`).

---

## Go Installation

```bash
sudo apt install -y golang
```

> **Note**: Installs the Go programming language, suitable for building fast and scalable applications.

---

## Node.js and Yarn Setup

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install -y yarn
```

> **Explanation**: Sets up Node.js (version 18) and Yarn, a fast package manager for JavaScript projects.

### Optional: NVM

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install node
```

> **Comment**: Node Version Manager (NVM) allows switching between Node.js versions, useful for projects with specific version requirements.

---

## PostgreSQL Server Setup

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

> **Note**: Installs and enables PostgreSQL, a powerful relational database system.

### Optional: Create user and database

```bash
sudo -u postgres psql -c "CREATE USER myuser WITH PASSWORD 'mypassword';"
sudo -u postgres psql -c "CREATE DATABASE mydb OWNER myuser;"
```

> **Explanation**: Creates a new PostgreSQL user and database for development purposes.

---

## Neovim Configuration

For a beginner-friendly setup, follow this guide:  
👉 [How to Setup Neovim (2024) by Josean](https://www.josean.com/posts/how-to-setup-neovim-2024)

### Install Nerd Font

```bash
mkdir -p ~/.local/share/fonts
cd ~/.local/share/fonts
curl -fLo "Fira Code Regular Nerd Font Complete.ttf" "https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Regular/complete/Fira%20Code%20Regular%20Nerd%20Font%20Complete.ttf"
fc-cache -fv
```

> **Comment**: Nerd Fonts provide icons and glyphs for a better terminal and editor experience.

### Install Neovim

```bash
sudo apt install -y neovim
```

> **Note**: Installs Neovim, a modern and extensible text editor.

### Optional: Plugin manager

```bash
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

> **Explanation**: Installs Vim-Plug, a plugin manager for Neovim.

### Optional: Minimal init.vim

```vim
call plug#begin()
Plug 'tpope/vim-sensible'
Plug 'morhetz/gruvbox'
call plug#end()
colorscheme gruvbox
set number
```

> **Comment**: This basic configuration adds sensible defaults (`vim-sensible`), a color scheme (`gruvbox`), and line numbers.

---

## WezTerm Installation

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:wez/ppa
sudo apt update
sudo apt install -y wezterm
```

> **Note**: WezTerm is a GPU-accelerated terminal emulator with advanced features like multiplexing.

More configuration tips:  
👉 [Josean’s WezTerm Guide](https://www.josean.com/posts/wezterm)

---

## Installing IDEs

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

> **Explanation**: Installs Visual Studio Code, PyCharm, RustRover, and JetBrains Toolbox for managing JetBrains IDEs.

---

## Version Control Tools

```bash
wget https://release.gitkraken.com/linux/gitkraken-amd64.deb
sudo dpkg -i gitkraken-amd64.deb
sudo apt install -f
sudo apt install -y tig
```

> **Comment**: Installs GitKraken (GUI for Git) and `tig` (text-based Git interface).

### Git Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor nvim
git config --global init.defaultBranch main
```

> **Note**: Configures Git with your identity, sets Neovim as the default editor, and uses `main` as the default branch.

### Global .gitignore

```bash
curl https://raw.githubusercontent.com/github/gitignore/main/Global/Linux.gitignore >> ~/.gitignore
git config --global core.excludesfile ~/.gitignore
```

> **Explanation**: Sets up a global `.gitignore` file to ignore common Linux-specific files.

---

## API Testing Tools

```bash
sudo snap install postman
sudo apt install -y httpie
```

> **Comment**: Installs Postman (GUI for API testing) and HTTPie (CLI for HTTP requests).

---

## Wireshark Installation

```bash
sudo apt install -y wireshark
```

> **Note**: Wireshark is a network protocol analyzer for troubleshooting and monitoring.

---

## pgAdmin Installation

```bash
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" | sudo tee /etc/apt/sources.list.d/pgadmin4.list
sudo apt update
sudo apt install -y pgadmin4
```

> **Explanation**: Installs pgAdmin, a web-based GUI for managing PostgreSQL databases.

---

## Docker and Docker Compose

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

> **Comment**: Installs Docker for containerization and adds the user to the Docker group to run commands without `sudo`.

### Optional: Docker Compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

> **Note**: Docker Compose simplifies multi-container Docker applications.

---

## VirtualBox Installation

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

> **Explanation**: Installs VirtualBox for virtualization, including the extension pack for additional features, and requires a reboot to apply group changes.

---

## STM32F4xx Development Tools

```bash
sudo apt update
sudo apt install -y gcc-arm-none-eabi gdb-arm-none-eabi openocd gdb-multiarch
sudo apt install -y qemu-system-arm
cargo install cargo-flash
cargo install cargo-embed
sudo apt install -y stlink-tools
sudo usermod -a -G dialout $USER
```

> **Comment**: Sets up tools for STM32F4 microcontroller development, including compilers, debuggers, and flashing utilities.

---

## Leptos Setup

```bash
cargo install cargo-leptos
```

> **Note**: Installs Leptos, a Rust framework for building web applications.

---

## Tauri Setup

```bash
npm install -g @tauri-apps/cli
```

> **Explanation**: Installs Tauri, a framework for building lightweight desktop applications with web technologies.

---

## Removing UFW and iptables

```bash
sudo ufw disable
sudo apt remove --purge ufw
sudo iptables -F
sudo iptables -X
sudo iptables -Z
sudo apt remove --purge iptables
```

> **Comment**: Disables and removes UFW and iptables to prepare for `nftables` as the firewall solution.

---

## nftables Firewall Configuration

```bash
sudo apt update
sudo apt install nftables
sudo systemctl enable nftables
sudo systemctl start nftables
sudo nano /etc/nftables.conf
```

### Example config

```nftables
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

> **Explanation**: Configures `nftables` as a modern firewall, allowing SSH, web traffic, DNS, and ICMP while dropping other traffic by default.

---

## SSH Configuration

```bash
sudo apt install -y openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

> **Note**: Installs and enables the SSH server for remote access.

---

## Generating SSH Keys

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

> **Comment**: Generates an ED25519 SSH key pair for secure authentication and adds it to the SSH agent.

---

## Additional Tools and Utilities

```bash
sudo apt install -y glances bpytop neofetch
sudo apt install -y gnome-disk-utility udisks2
sudo apt install -y nmap tcpdump mtr traceroute
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
sudo apt autoremove -y
sudo apt clean
sudo journalctl --vacuum-time=1d
```

> **Explanation**: Reinstalls system monitoring and network tools, and configures automatic updates and cleanup for system maintenance.

---

Happy coding! 🚀
