🐧 Ubuntu Developer Setup for Beginners
This guide provides a step-by-step process for setting up a comprehensive development environment on Ubuntu, tailored for beginner developers. It covers everything from installing the operating system to configuring essential tools, programming languages, IDEs, and utilities, ensuring a robust setup for software development.
✅ Suitable for Ubuntu 22.04 and 24.04💡 Designed for software engineers, system programmers, and hobbyists starting with Linux development.

📚 Table of Contents

- [Flashing Ubuntu ISO to USB (on macOS)](#flashing-ubuntu-iso-to-usb-on-macos)
- [System Update](#system-update)
- [Essential Tools Installation](#essential-tools-installation)
- [Installing Zsh and Oh My Zsh](#installing-zsh-and-oh-my-zsh)
- [C/C++ Development Tools](#cc-development-tools)
- [Python Setup](#python-setup)
- [Rust Installation](#rust-installation)
- [Go Installation](#go-installation)
- [Node.js and Yarn Setup](#nodejs-and-yarn-setup)
- [PostgreSQL Server Setup](#postgresql-server-setup)
- [Neovim Configuration](#neovim-configuration)
- [WezTerm Installation](#wezterm-installation)
- [Installing IDEs](#installing-ides)
- [Version Control Tools](#version-control-tools)
- [API Testing Tools](#api-testing-tools)
- [Wireshark Installation](#wireshark-installation)
- [pgAdmin Installation](#pgadmin-installation)
- [Docker and Docker Compose](#docker-and-docker-compose)
- [VirtualBox Installation](#virtualbox-installation)
- [STM32F4xx Development Tools](#stm32f4xx-development-tools)
- [Leptos Setup](#leptos-setup)
- [Tauri Setup](#tauri-setup)
- [Removing UFW and iptables](#removing-ufw-and-iptables)
- [nftables Firewall Configuration](#nftables-firewall-configuration)
- [SSH Configuration](#ssh-configuration)
- [Generating SSH Keys](#generating-ssh-keys)
- [Additional Tools and Utilities](#additional-tools-and-utilities)

1. Flashing Ubuntu ISO to USB (on macOS)
   diskutil list
   diskutil unmountDisk /dev/diskX
   sudo dd if=Downloads/ubuntu-24.04.2-desktop-amd64.iso of=/dev/diskX bs=1m
   diskutil eject /dev/diskX

2. System Update
   sudo apt update && sudo apt upgrade -y

3. Essential Tools Installation
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

4. Installing Zsh and Oh My Zsh
   sudo apt install -y zsh
   sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
   chsh -s $(which zsh) # Optional

Popular Plugins
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

Powerlevel10k Theme
git clone --depth=1 https://github.com/romkatv/powerlevel10k ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k

Sample ~/.zshrc
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
ZSH_THEME="powerlevel10k/powerlevel10k"

5. C/C++ Development Tools
   sudo apt install -y gcc g++ gdb make cmake
   sudo apt install -y clang clang-format clang-tidy valgrind cppcheck
   sudo apt install -y libboost-all-dev # Optional

6. Python Setup
   sudo apt install -y python3 python3-pip python3-venv python3-dev python3-setuptools
   pip3 install black flake8 mypy pytest
   sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
   sudo update-alternatives --config python
   pip3 install jupyter # Optional
   curl https://pyenv.run | bash

Add to ~/.zshrc
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
exec $SHELL

Example Usage
pyenv install 3.11.5
pyenv global 3.11.5

7. Rust Installation
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   source $HOME/.cargo/env
   rustup target add thumbv6m-none-eabi thumbv7em-none-eabihf
   cargo install cargo-watch cargo-audit rustfmt clippy

8. Go Installation
   sudo apt install -y golang

9. Node.js and Yarn Setup
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install -y nodejs
   curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
   echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
   sudo apt update && sudo apt install -y yarn

# Optional: NVM

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install node

10. PostgreSQL Server Setup
    sudo apt update
    sudo apt install -y postgresql postgresql-contrib
    sudo systemctl start postgresql
    sudo systemctl enable postgresql

# Optional: Create user and database

sudo -u postgres psql -c "CREATE USER myuser WITH PASSWORD 'mypassword';"
sudo -u postgres psql -c "CREATE DATABASE mydb OWNER myuser;"

11. Neovim Configuration
    For a beginner-friendly setup, follow this guide:👉 How to Setup Neovim (2024) by Josean
    A detailed setup guide, originally for macOS but adaptable for Ubuntu, is also available:👉 How to Setup Neovim (2024) by Josean
    For Ubuntu-specific instructions, refer to:

Install and Use Neovim on Ubuntu | GeeksforGeeks  
Install and Use Neovim on Ubuntu and other Linux

Install Nerd Font
mkdir -p ~/.local/share/fonts
cd ~/.local/share/fonts
curl -fLo "Fira Code Regular Nerd Font Complete.ttf" "https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Regular/complete/Fira%20Code%20Regular%20Nerd%20Font%20Complete.ttf"
fc-cache -fv

Install Neovim
sudo apt install -y neovim

# Optional: Plugin manager

curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

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

12. WezTerm Installation
    sudo apt install -y software-properties-common
    sudo add-apt-repository ppa:wez/ppa
    sudo apt update
    sudo apt install -y wezterm

More configuration tips:👉 Josean’s WezTerm Guide (macOS-focused, but similar) 13. Installing IDEs
sudo snap install --classic code
sudo snap install pycharm-community --classic
wget -O rustrover.tar.gz "https://download.jetbrains.com/rustrover/RustRover-2024.2.tar.gz"
tar -xzf rustrover.tar.gz
./RustRover-_/bin/rustrover.sh
wget -O jetbrains-toolbox.tar.gz "https://download.jetbrains.com/toolbox/jetbrains-toolbox-2.1.3.18901.tar.gz"
tar -xzf jetbrains-toolbox.tar.gz
./jetbrains-toolbox-_/jetbrains-toolbox

14. Version Control Tools
    wget https://release.gitkraken.com/linux/gitkraken-amd64.deb
    sudo dpkg -i gitkraken-amd64.deb
    sudo apt install -f
    sudo apt install -y tig

Git Configuration
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor nvim
git config --global init.defaultBranch main

Global .gitignore
curl https://raw.githubusercontent.com/github/gitignore/main/Global/Linux.gitignore >> ~/.gitignore
git config --global core.excludesfile ~/.gitignore

15. API Testing Tools
    sudo snap install postman
    sudo apt install -y httpie

16. Wireshark Installation
    sudo apt install -y wireshark

17. pgAdmin Installation
    curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
    echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" | sudo tee /etc/apt/sources.list.d/pgadmin4.list
    sudo apt update
    sudo apt install -y pgadmin4

18. Docker and Docker Compose
    sudo apt update
    sudo apt install -y docker.io
    sudo systemctl enable docker
    sudo systemctl start docker
    sudo usermod -aG docker $USER

# Optional: Docker Compose

sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

19. VirtualBox Installation
    sudo apt install -y build-essential dkms linux-headers-$(uname -r)
    wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
    sudo apt update
    sudo apt install -y virtualbox-7.0
    wget https://download.virtualbox.org/virtualbox/7.0.12/Oracle_VM_VirtualBox_Extension_Pack-7.0.12.vbox-extpack
    sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-7.0.12.vbox-extpack
    sudo usermod -aG vboxusers $USER
    sudo reboot

20. STM32F4xx Development Tools
    sudo apt update
    sudo apt install -y gcc-arm-none-eabi gdb-arm-none-eabi openocd gdb-multiarch
    sudo apt install -y qemu-system-arm
    cargo install cargo-flash
    cargo install cargo-embed
    sudo apt install -y stlink-tools
    sudo usermod -a -G dialout $USER

21. Leptos Setup
    cargo install cargo-leptos

22. Tauri Setup
    npm install -g @tauri-apps/cli

23. Removing UFW and iptables
    sudo ufw disable
    sudo apt remove --purge ufw
    sudo iptables -F
    sudo iptables -X
    sudo iptables -Z
    sudo apt remove --purge iptables

24. nftables Firewall Configuration
    sudo apt update
    sudo apt install nftables
    sudo systemctl enable nftables
    sudo systemctl start nftables
    sudo nano /etc/nftables.conf

Example config:
table inet filter {
chain input {
type filter hook input priority 0; policy drop;
iifname "lo" accept
tcp dport 22 accept
icmp type echo-request accept # Allow established connections
ct state established,related accept
}
chain output {
type filter hook output priority 0; policy drop;
oifname "lo" accept
tcp dport 53 accept
tcp dport 80 accept
tcp dport 443 accept
icmp type echo-request accept # Allow DNS queries over UDP
udp dport 53 accept
}
chain forward {
type filter hook forward priority 0; policy drop;
}
}

sudo nft -f /etc/nftables.conf
sudo systemctl restart nftables

25. SSH Configuration
    sudo apt install -y openssh-server
    sudo systemctl enable ssh
    sudo systemctl start ssh

26. Generating SSH Keys
    ssh-keygen -t ed25519 -C "your_email@example.com"
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_ed25519

27. Additional Tools and Utilities
    sudo apt install -y glances bpytop neofetch
    sudo apt install -y gnome-disk-utility udisks2
    sudo apt install -y nmap tcpdump mtr traceroute
    sudo apt install -y unattended-upgrades
    sudo dpkg-reconfigure --priority=low unattended-upgrades
    sudo apt autoremove -y
    sudo apt clean
    sudo journalctl --vacuum-time=1d

Happy coding! 🚀
