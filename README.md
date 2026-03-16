# Ubuntu Developer Setup

A comprehensive step-by-step guide to install and configure Ubuntu for development purposes.
Suitable for fresh installs on personal laptops or desktops. Covers language toolchains, IDEs,
Docker, virtualization, hardware tools (e.g. STM32), and much more.

âœ… Tested on Ubuntu 22.04 LTS and 24.04 LTS  
ðŸ’¡ Designed for software engineers, system programmers, and hobbyists starting with Linux development.

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
20. [STM32 Embedded Development (C/C++ and Rust)](#stm32-embedded-development-cc-and-rust)
21. [QEMU Installation (x86)](#qemu-installation)
22. [Leptos Setup](#leptos-setup)
23. [Tauri Setup](#tauri-setup)
24. [Removing UFW and iptables](#removing-ufw-and-iptables)
25. [nftables Firewall Configuration](#nftables-firewall-configuration)
26. [SSH Configuration](#ssh-configuration)
27. [Generating SSH Keys](#generating-ssh-keys)

---

## Flashing Ubuntu ISO to USB (on macOS)

```bash
diskutil list
diskutil unmountDisk /dev/diskX
sudo dd if=~/Downloads/ubuntu-24.04.2-desktop-amd64.iso of=/dev/rdiskX bs=1m status=progress
diskutil eject /dev/diskX
```

> **Note**: Replace `/dev/diskX` with the correct disk identifier found via `diskutil list`.
> Use `/dev/rdiskX` (raw device) instead of `/dev/diskX` for significantly faster write speeds.
> Double-check the target disk â€” `dd` will silently overwrite any device you specify.

---

## System Update

```bash
sudo apt update && sudo apt upgrade -y
sudo apt autoremove -y && sudo apt clean
```

> **Explanation**: Updates package lists and upgrades all installed packages. Running `autoremove`
> and `clean` afterwards removes orphaned dependencies and cached package files to free up space.

---

## Essential Tools Installation

```bash
sudo apt install -y \
  build-essential curl wget git unzip zip \
  htop btop neofetch \
  net-tools nmap tcpdump mtr traceroute \
  jq ripgrep fd-find bat tree \
  printer-driver-all cups cups-pdf sane xsane \
  gnome-disk-utility udisks2

# Enable automatic security updates
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades

# Free up disk space and truncate old logs
sudo apt autoremove -y
sudo apt clean
sudo journalctl --vacuum-time=7d
```

> **Comment**: In addition to the standard tools, this installs `ripgrep` (fast `grep` replacement),
> `fd-find` (fast `find` replacement), `bat` (a `cat` with syntax highlighting), and `btop`
> (a modern resource monitor). These are everyday productivity boosters for any developer.
> Log vacuum is set to 7 days â€” adjust to taste.

---

## Installing Zsh and Oh My Zsh

```bash
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
chsh -s $(which zsh)  # Set Zsh as the default shell (log out and back in to apply)
```

> **Note**: Zsh is a powerful shell alternative to Bash. Oh My Zsh enhances it with plugins and
> themes. After running `chsh`, log out and back in for the change to take effect.

### Recommended Plugins

```bash
# zsh-autosuggestions â€” inline command suggestions from history
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-syntax-highlighting â€” highlight valid/invalid commands as you type
git clone https://github.com/zsh-users/zsh-syntax-highlighting \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### Powerlevel10k Theme

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

> **Comment**: Powerlevel10k is a highly customizable and fast Zsh theme.
> Run `p10k configure` after installation to set up its appearance interactively.

### Sample ~/.zshrc Snippet

```zsh
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
ZSH_THEME="powerlevel10k/powerlevel10k"
```

> **Note**: Both plugins must be cloned (see above) before adding them here.

---

## C/C++ Development Tools

```bash
sudo apt install -y gcc g++ gdb make cmake ninja-build
sudo apt install -y clang clang-format clang-tidy valgrind cppcheck
sudo apt install -y libboost-all-dev   # Optional: Boost library
```

> **Explanation**: Installs GCC/Clang compilers, `gdb` debugger, and build systems (`cmake`,
> `ninja-build`). `clang-tidy` and `valgrind` are essential for catching bugs and memory issues.
> `libboost-all-dev` is optional â€” only install if your project requires it (it's large).

---

## Python Setup

```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev python3-setuptools

# Set python3 as the default 'python' command
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1

# Development tools
pip3 install --user black isort flake8 mypy pytest

# Optional: Jupyter notebooks
pip3 install --user jupyter

# pyenv â€” manage multiple Python versions
curl https://pyenv.run | bash
```

> **Comment**: `isort` is added here alongside `black` â€” together they handle all import and
> code formatting. Using `--user` flag installs packages for your user only, avoiding
> permission issues and conflicts with system Python.

### Add to ~/.zshrc (or ~/.bashrc)

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
exec $SHELL
```

### Example Usage

```bash
pyenv install 3.12.3
pyenv global 3.12.3
python --version  # Should show 3.12.3
```

---

## Rust Installation

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

# Embedded targets for microcontrollers (e.g. STM32)
rustup target add thumbv6m-none-eabi thumbv7em-none-eabihf

# Essential Rust development tools
rustup component add rustfmt clippy
cargo install cargo-watch cargo-audit cargo-expand
```

> **Comment**: `rustfmt` and `clippy` are now installed via `rustup component add` â€” the
> recommended way. `cargo-expand` is a useful addition for debugging macros.
> `cargo-audit` checks your dependencies for known security vulnerabilities.

---

## Go Installation

```bash
# Check the latest version at https://go.dev/dl/ before running
GO_VERSION="1.22.4"
wget "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go${GO_VERSION}.linux-amd64.tar.gz"
rm "go${GO_VERSION}.linux-amd64.tar.gz"
```

### Add to ~/.zshrc (or ~/.bashrc)

```bash
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.zshrc
source ~/.zshrc
go version
```

> **Note**: Installing Go from the official archive (instead of `apt`) ensures you get the
> latest stable version. The `apt` package is often several releases behind.

---

## Node.js and Yarn Setup

```bash
# Install Node.js 20 LTS via NodeSource
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Verify installation
node --version
npm --version
```

```bash
# Install Yarn via npm (recommended modern approach)
npm install -g yarn
yarn --version
```

> **Explanation**: Node.js 20 is the current LTS release (Node 18 reaches EOL in April 2025).
> Installing Yarn via `npm install -g yarn` is the current recommended approach â€” the old
> apt-key method for the Yarn repository is deprecated.

### Optional: NVM (Node Version Manager)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.zshrc
nvm install --lts   # Install latest LTS
nvm use --lts
```

> **Comment**: NVM is useful when you work on multiple projects requiring different Node.js
> versions. It takes precedence over the system-installed Node.js.

---

## PostgreSQL Server Setup

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib
sudo systemctl enable --now postgresql
```

> **Note**: `enable --now` both enables the service at boot and starts it immediately â€”
> a shorthand for two separate `enable` + `start` calls.

### Optional: Create User and Database

```bash
sudo -u postgres psql -c "CREATE USER myuser WITH PASSWORD 'mypassword';"
sudo -u postgres psql -c "CREATE DATABASE mydb OWNER myuser;"
```

> **Explanation**: Creates a dedicated PostgreSQL user and database for development.
> Replace `myuser`, `mypassword`, and `mydb` with your own values.

---

## Neovim Configuration

### Install Neovim

```bash
# Install the latest stable version via the official PPA
sudo add-apt-repository ppa:neovim-ppa/stable -y
sudo apt update
sudo apt install -y neovim
```

> **Note**: The `apt` version of Neovim is often outdated. The official PPA provides the
> latest stable release.

### Install a Nerd Font

```bash
mkdir -p ~/.local/share/fonts
wget -O ~/.local/share/fonts/FiraCodeNerdFont-Regular.ttf \
  "https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Regular/FiraCodeNerdFontMono-Regular.ttf"
fc-cache -fv
```

> **Comment**: Nerd Fonts provide icons and powerline glyphs required by most modern Neovim
> themes and status line plugins.

### Plugin Manager: lazy.nvim (recommended)

```bash
# lazy.nvim bootstraps itself â€” add this to the top of your init.lua
```

```lua
-- ~/.config/nvim/init.lua
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not vim.loop.fs_stat(lazypath) then
  vim.fn.system({
    "git", "clone", "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

require("lazy").setup({
  { "folke/tokyonight.nvim" },
  { "nvim-treesitter/nvim-treesitter", build = ":TSUpdate" },
  { "neovim/nvim-lspconfig" },
})

vim.cmd.colorscheme("tokyonight")
vim.opt.number = true
vim.opt.expandtab = true
vim.opt.shiftwidth = 2
```

> **Comment**: `lazy.nvim` is the modern standard plugin manager for Neovim â€” it replaces
> Vim-Plug and Packer. Plugins are loaded lazily by default, which greatly improves startup time.

For a detailed beginner-friendly configuration, follow this guide:  
ðŸ‘‰ [How to Setup Neovim (2024) by Josean](https://www.josean.com/posts/how-to-setup-neovim-2024)

---

## WezTerm Installation

```bash
curl -fsSL https://apt.fury.io/wez/gpg.key | sudo gpg --yes --dearmor \
  -o /usr/share/keyrings/wezterm-fury.gpg
echo 'deb [signed-by=/usr/share/keyrings/wezterm-fury.gpg] https://apt.fury.io/wez/ * *' \
  | sudo tee /etc/apt/sources.list.d/wezterm.list
sudo apt update
sudo apt install -y wezterm
```

> **Note**: This is the official installation method from WezTerm's documentation. The PPA
> (`ppa:wez/ppa`) has been replaced by the Fury repository.
> WezTerm is a GPU-accelerated terminal with built-in multiplexing, Lua configuration, and
> excellent font rendering.

More configuration tips:  
ðŸ‘‰ [Josean's WezTerm Guide](https://www.josean.com/posts/wezterm)

---

## Installing IDEs

```bash
# Visual Studio Code
sudo snap install --classic code

# PyCharm Community Edition
sudo snap install pycharm-community --classic

# JetBrains Toolbox (manages all JetBrains IDEs)
wget -O /tmp/jetbrains-toolbox.tar.gz \
  "https://data.services.jetbrains.com/products/download?platform=linux&code=TBA"
tar -xzf /tmp/jetbrains-toolbox.tar.gz -C /tmp
/tmp/jetbrains-toolbox-*/jetbrains-toolbox
```

> **Explanation**: JetBrains Toolbox is the recommended way to install and keep RustRover,
> CLion, IntelliJ IDEA, and other JetBrains IDEs up to date. Hardcoded version URLs in
> `wget` commands go stale quickly â€” always download the latest from
> [jetbrains.com/toolbox-app](https://www.jetbrains.com/toolbox-app/).

---

## Version Control Tools

```bash
# GitKraken (GUI Git client)
wget https://release.gitkraken.com/linux/gitkraken-amd64.deb -O /tmp/gitkraken.deb
sudo dpkg -i /tmp/gitkraken.deb
sudo apt install -f   # Fix any missing dependencies

# tig â€” terminal-based Git log browser
sudo apt install -y tig
```

### Git Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor nvim
git config --global init.defaultBranch main
git config --global pull.rebase false   # Merge strategy on pull (set to 'true' for rebase)
git config --global core.autocrlf input # Normalize line endings on commit
```

### Global .gitignore

```bash
curl https://raw.githubusercontent.com/github/gitignore/main/Global/Linux.gitignore \
  >> ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

> **Note**: Using `~/.gitignore_global` is more conventional than `~/.gitignore` (which
> some tools may treat as a project-level ignore file).

---

## API Testing Tools

```bash
# Postman â€” GUI API client
sudo snap install postman

# HTTPie â€” expressive CLI HTTP client
sudo apt install -y httpie

# Optional: Bruno â€” open-source Postman alternative (stores collections as files)
sudo snap install bruno
```

> **Comment**: Bruno is worth knowing about â€” it stores API collections as plain text files
> in your project repo, making them version-controllable without a cloud account.

---

## Wireshark Installation

```bash
sudo apt install -y wireshark
sudo usermod -aG wireshark $USER
# Log out and back in for the group change to take effect
```

> **Note**: Adding your user to the `wireshark` group allows capturing packets without `sudo`,
> which is safer than running the GUI as root.

---

## pgAdmin Installation

```bash
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub \
  | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] \
  https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" \
  | sudo tee /etc/apt/sources.list.d/pgadmin4.list

sudo apt update
sudo apt install -y pgadmin4
```

> **Explanation**: Installs pgAdmin 4, a web-based GUI for managing PostgreSQL databases.
> After installation, run `sudo /usr/pgadmin4/bin/setup-web.sh` to configure the web server.

---

## Docker and Docker Compose

```bash
# Add Docker's official GPG key and repository
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine + Compose plugin
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

# Run Docker without sudo
sudo usermod -aG docker $USER
newgrp docker  # Apply group without logging out
```

> **Comment**: This uses the **official Docker repository** instead of the `docker.io` package
> from Ubuntu's repos. The official package includes the `docker compose` (v2) plugin, which
> replaces the standalone `docker-compose` binary. Use `docker compose` (no hyphen) going forward.

---

## VirtualBox Installation

```bash
# Install kernel headers and DKMS (required for VirtualBox kernel modules)
sudo apt install -y build-essential dkms linux-headers-$(uname -r)

# Add VirtualBox official repository
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- \
  | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
  http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" \
  | sudo tee /etc/apt/sources.list.d/virtualbox.list

sudo apt update
sudo apt install -y virtualbox-7.1   # Check https://www.virtualbox.org/wiki/Linux_Downloads for latest

sudo usermod -aG vboxusers $USER
sudo reboot
```

> **Note**: Check [virtualbox.org](https://www.virtualbox.org/wiki/Linux_Downloads) for the
> latest package name before installing. The Extension Pack version must exactly match the
> installed VirtualBox version â€” download it from the same page.

---

## STM32 Embedded Development (C/C++ and Rust)

This section provides a complete setup for STM32 development â€” toolchains, debuggers,
flashing tools, IDE integration, and QEMU-based emulation â€” for both C/C++ and Rust workflows.

---

### Toolchain Installation

```bash
sudo apt update

# ARM bare-metal GCC toolchain + GDB
sudo apt install -y gcc-arm-none-eabi gdb-multiarch binutils-arm-none-eabi

# Build systems
sudo apt install -y make cmake ninja-build

# ST-Link utilities (st-flash, st-info, st-util)
sudo apt install -y stlink-tools

# OpenOCD â€” on-chip debugger, supports ST-Link, J-Link, CMSIS-DAP
sudo apt install -y openocd

# QEMU with ARM support (for emulation without hardware)
sudo apt install -y qemu-system-arm

# Optional: libusb (needed if building OpenOCD from source)
sudo apt install -y libusb-1.0-0-dev
```

> **Note**: The `gcc-arm-none-eabi` from Ubuntu repos is usually 1â€“2 major versions behind
> upstream. For the latest toolchain, download directly from
> [developer.arm.com/downloads](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)
> and add it to your `PATH`.

---

### udev Rules (USB Access Without sudo)

Without udev rules, you need `sudo` to access ST-Link and J-Link adapters. Configure them once:

```bash
# ST-Link V2 / V3
sudo tee /etc/udev/rules.d/49-stlink.rules > /dev/null << 'EOF'
# ST-Link V1
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3744", MODE="0666", GROUP="plugdev"
# ST-Link V2
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", MODE="0666", GROUP="plugdev"
# ST-Link V2-1
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", MODE="0666", GROUP="plugdev"
# ST-Link V3
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374e", MODE="0666", GROUP="plugdev"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374f", MODE="0666", GROUP="plugdev"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3753", MODE="0666", GROUP="plugdev"
EOF

# J-Link
sudo tee /etc/udev/rules.d/99-jlink.rules > /dev/null << 'EOF'
SUBSYSTEM=="usb", ATTR{idVendor}=="1366", MODE="0666", GROUP="plugdev"
EOF

# CMSIS-DAP (DAPLink, Black Magic Probe, etc.)
sudo tee /etc/udev/rules.d/98-cmsis-dap.rules > /dev/null << 'EOF'
SUBSYSTEM=="usb", ATTRS{product}=="*CMSIS-DAP*", MODE="0666", GROUP="plugdev"
EOF

# Apply rules
sudo udevadm control --reload-rules
sudo udevadm trigger

# Add yourself to the required groups
sudo usermod -aG dialout,plugdev $USER
# Log out and back in for group changes to take effect
```

---

### C/C++ Project Workflow

#### CMake + arm-none-eabi Toolchain File

Create `cmake/arm-none-eabi.cmake` in your project:

```cmake
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR arm)

set(CMAKE_C_COMPILER   arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_OBJCOPY      arm-none-eabi-objcopy)
set(CMAKE_SIZE         arm-none-eabi-size)

set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# Cortex-M4 with FPU (STM32F4xx) â€” adjust for your target
set(CPU_FLAGS "-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard")

set(CMAKE_C_FLAGS_INIT   "${CPU_FLAGS}")
set(CMAKE_CXX_FLAGS_INIT "${CPU_FLAGS}")
set(CMAKE_ASM_FLAGS_INIT "${CPU_FLAGS}")
set(CMAKE_EXE_LINKER_FLAGS_INIT "${CPU_FLAGS} -specs=nano.specs -specs=nosys.specs")
```

Build:

```bash
cmake -B build -DCMAKE_TOOLCHAIN_FILE=cmake/arm-none-eabi.cmake -GNinja
ninja -C build
arm-none-eabi-size build/firmware.elf
```

#### Flash via st-flash

```bash
# Convert ELF â†’ binary
arm-none-eabi-objcopy -O binary build/firmware.elf build/firmware.bin

# Flash (erase + write)
st-flash --reset write build/firmware.bin 0x08000000

# Erase chip
st-flash erase

# Read device info
st-info --probe
```

#### Flash via OpenOCD

```bash
openocd \
  -f interface/stlink.cfg \
  -f target/stm32f4x.cfg \
  -c "program build/firmware.elf verify reset exit"
```

---

### Debugging with GDB + OpenOCD

#### Terminal 1 â€” Start OpenOCD GDB server

```bash
openocd \
  -f interface/stlink.cfg \
  -f target/stm32f4x.cfg
# OpenOCD now listens on port 3333 (GDB) and 4444 (telnet)
```

#### Terminal 2 â€” Connect GDB

```bash
gdb-multiarch build/firmware.elf \
  -ex "target extended-remote :3333" \
  -ex "monitor reset halt" \
  -ex "load" \
  -ex "monitor reset init"
```

#### Useful GDB Commands for Embedded

```
(gdb) monitor reset halt     # Reset and halt the CPU
(gdb) monitor reset run      # Reset and run
(gdb) load                   # Flash the ELF to the target
(gdb) b main                 # Set breakpoint at main()
(gdb) c                      # Continue
(gdb) p variable_name        # Print variable
(gdb) x/10xw 0x20000000      # Examine 10 words at SRAM start
(gdb) info registers         # Show all CPU registers
(gdb) set $pc = 0x08000000   # Manually set program counter
(gdb) disassemble            # Disassemble current location
```

#### GDB Init File (.gdbinit)

Create `.gdbinit` in your project root for a faster debug loop:

```
target extended-remote :3333
monitor reset halt
load
monitor reset init
break main
continue
```

Then run simply: `gdb-multiarch build/firmware.elf`

> **Note**: To allow local `.gdbinit` files, add this to `~/.gdbinit`:
> `set auto-load safe-path /`

---

### Rust Embedded Workflow

#### Targets and Tooling

```bash
# Stable toolchain is sufficient for most embedded Rust work
rustup target add thumbv7em-none-eabihf   # Cortex-M4/M7 with FPU (STM32F4, F7, H7)
rustup target add thumbv7em-none-eabi     # Cortex-M4/M7 without FPU
rustup target add thumbv6m-none-eabi      # Cortex-M0/M0+ (STM32F0, G0)
rustup target add thumbv8m.main-none-eabihf # Cortex-M33 (STM32L5, U5, WB)

# cargo-binutils â€” arm-none-eabi-* wrappers for Rust
cargo install cargo-binutils
rustup component add llvm-tools-preview

# probe-rs â€” modern flashing and debugging (replaces cargo-embed + cargo-flash)
curl --proto '=https' --tlsv1.2 -LsSf https://github.com/probe-rs/probe-rs/releases/latest/download/probe-rs-tools-installer.sh | sh

# flip-link â€” zero-cost stack overflow detection
cargo install flip-link
```

#### Cargo Project Setup

Create a new embedded project:

```bash
cargo new --bin stm32-blink
cd stm32-blink
```

`.cargo/config.toml`:

```toml
[target.thumbv7em-none-eabihf]
# Use probe-rs as the runner â€” flashes and opens RTT console automatically
runner = "probe-rs run --chip STM32F411CEUx"

# Or use QEMU for emulation (no hardware needed):
# runner = "qemu-system-arm -cpu cortex-m4 -machine lm3s6965evb -nographic -semihosting-config enable=on,target=native -kernel"

rustflags = [
  "-C", "link-arg=-Tlink.x",
  "-C", "link-arg=-Tdefmt.x",  # Remove if not using defmt
]

[build]
target = "thumbv7em-none-eabihf"
```

`Cargo.toml` dependencies:

```toml
[dependencies]
cortex-m            = { version = "0.7", features = ["critical-section-single-core"] }
cortex-m-rt         = "0.7"
panic-probe         = { version = "0.3", features = ["print-defmt"] }
defmt               = "0.3"
defmt-rtt           = "0.4"

# HAL â€” choose one for your chip family:
stm32f4xx-hal       = { version = "0.21", features = ["stm32f411"] }
# stm32h7xx-hal     = { version = "0.16", features = ["stm32h743v"] }

[profile.release]
codegen-units = 1   # Better optimization
debug         = 2   # Keep debug info in release for GDB
lto           = "fat"
opt-level     = "z" # Minimize size; use "3" for speed
```

#### Flash and Run with probe-rs

```bash
# Flash and open RTT log stream
cargo run --release

# Flash only
probe-rs download --chip STM32F411CEUx target/thumbv7em-none-eabihf/release/firmware

# Interactive chip info
probe-rs info

# List connected probes
probe-rs list
```

#### Debugging Rust with probe-rs + GDB

```bash
# Terminal 1 â€” start GDB server
probe-rs gdb --chip STM32F411CEUx

# Terminal 2 â€” connect GDB
gdb-multiarch target/thumbv7em-none-eabihf/release/firmware \
  -ex "target extended-remote :1337" \
  -ex "monitor reset halt" \
  -ex "load" \
  -ex "break main" \
  -ex "continue"
```

#### RTT Logging with defmt

`defmt` + RTT provides fast, structured logging over the debug probe with zero overhead in release:

```rust
use defmt::*;
use defmt_rtt as _;
use panic_probe as _;

#[cortex_m_rt::entry]
fn main() -> ! {
    info!("Hello from STM32!");
    let value: u32 = 42;
    debug!("value = {}", value);
    loop {}
}
```

Run `cargo run` â€” logs appear in the terminal via the RTT channel automatically.

---

### VS Code Integration

Install the following extensions:

```bash
code --install-extension marus25.cortex-debug
code --install-extension rust-lang.rust-analyzer
code --install-extension ms-vscode.cpptools
code --install-extension probe-rs.probe-rs-debugger
```

#### launch.json for C/C++ (OpenOCD + Cortex-Debug)

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "STM32 Debug (OpenOCD)",
      "type": "cortex-debug",
      "request": "launch",
      "servertype": "openocd",
      "configFiles": ["interface/stlink.cfg", "target/stm32f4x.cfg"],
      "executable": "${workspaceFolder}/build/firmware.elf",
      "runToEntryPoint": "main",
      "svdFile": "${workspaceFolder}/STM32F411.svd",
      "showDevDebugOutput": "none"
    }
  ]
}
```

#### launch.json for Rust (probe-rs)

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "STM32 Debug (probe-rs)",
      "type": "probe-rs-debug",
      "request": "launch",
      "flashingConfig": { "flashingEnabled": true, "resetAfterFlashing": true },
      "chip": "STM32F411CEUx",
      "coreConfigs": [
        {
          "programBinary": "${workspaceFolder}/target/thumbv7em-none-eabihf/debug/firmware",
          "rttEnabled": true,
          "defmtEnabled": true
        }
      ]
    }
  ]
}
```

---

### SVD Files (Peripheral Register Viewer)

SVD files enable register-level inspection in Cortex-Debug â€” you can view and edit peripheral
registers (GPIO, UART, TIM, etc.) in the VS Code sidebar during a debug session.

```bash
# Download SVD for your chip from ST's CMSIS pack (example for STM32F4)
mkdir -p ~/stm32-svd && cd ~/stm32-svd
wget "https://raw.githubusercontent.com/posborne/cmsis-svd/master/data/STMicro/STM32F411.svd"
```

> Point `svdFile` in `launch.json` to this file. The Cortex-Debug extension will populate
> a **Peripherals** view with live register values during debugging.

---

### QEMU: STM32 Emulation (Without Hardware)

For quick iteration without a physical board:

```bash
# Rust â€” QEMU runner via .cargo/config.toml (see above)
cargo run   # Runs in QEMU automatically if runner is set

# C/C++ â€” run ELF directly in QEMU (lm3s6965 is well-supported for Cortex-M3/4 testing)
qemu-system-arm \
  -cpu cortex-m4 \
  -machine lm3s6965evb \
  -nographic \
  -semihosting-config enable=on,target=native \
  -kernel build/firmware.elf

# QEMU with GDB stub (pause at start, wait for debugger on port 1234)
qemu-system-arm \
  -cpu cortex-m4 \
  -machine lm3s6965evb \
  -nographic \
  -semihosting-config enable=on,target=native \
  -kernel build/firmware.elf \
  -s -S
# Then connect: gdb-multiarch build/firmware.elf -ex "target remote :1234"
```

> **Note**: QEMU doesn't emulate specific STM32 peripherals (GPIO, SPI, I2C, etc.). It's
> suitable for testing CPU logic, algorithms, and semihosting I/O. For peripheral testing,
> use real hardware or consider [Renode](https://renode.io/) which has partial STM32 support.

---

## QEMU Installation

```bash
sudo apt update
sudo apt install -y qemu-system-x86 qemu-utils
```

> **Explanation**: Installs QEMU for x86/x86_64 system emulation. `qemu-utils` provides
> tools like `qemu-img` for managing disk images. For full KVM acceleration, also run:
> `sudo apt install -y qemu-kvm libvirt-daemon-system && sudo usermod -aG kvm,libvirt $USER`

---

## Leptos Setup

```bash
# Leptos requires the nightly Rust toolchain and the WASM target
rustup toolchain install nightly
rustup target add wasm32-unknown-unknown
cargo install cargo-leptos
```

> **Note**: Leptos is a full-stack Rust framework for building reactive web apps (SSR + WASM).
> The `wasm32-unknown-unknown` target is required for client-side compilation.

---

## Tauri Setup

```bash
# System dependencies
sudo apt install -y libwebkit2gtk-4.1-dev libssl-dev libgtk-3-dev \
  libayatana-appindicator3-dev librsvg2-dev

# Tauri CLI
npm install -g @tauri-apps/cli

# Verify
npx tauri --version
```

> **Explanation**: Tauri builds lightweight desktop applications using a web frontend and a
> Rust backend. The `webkit2gtk` and GTK libraries are required on Linux for the webview.
> On Ubuntu 24.04, use `libwebkit2gtk-4.1-dev`; on 22.04 use `libwebkit2gtk-4.0-dev`.

---

## Removing UFW and iptables

> âš ï¸ **Warning**: Only do this if you intend to replace them with `nftables` immediately.
> Your system will have no firewall until `nftables` is configured.

```bash
sudo ufw disable
sudo apt remove --purge -y ufw

# Flush and reset iptables rules
sudo iptables -F
sudo iptables -X
sudo iptables -Z
sudo ip6tables -F
sudo ip6tables -X

sudo apt remove --purge -y iptables
```

> **Comment**: IPv6 tables (`ip6tables`) are also flushed here â€” the original guide skipped
> this, which can leave IPv6 rules in place unexpectedly.

---

## nftables Firewall Configuration

```bash
sudo apt install -y nftables
sudo systemctl enable --now nftables
sudo nano /etc/nftables.conf
```

### Example Configuration

```nft
#!/usr/sbin/nft -f
flush ruleset

table inet filter {

    chain input {
        type filter hook input priority 0; policy drop;

        # Allow loopback
        iifname "lo" accept

        # Allow established/related connections
        ct state established,related accept

        # Drop invalid packets
        ct state invalid drop

        # Allow SSH (rate-limited to reduce brute-force risk)
        tcp dport 22 ct state new limit rate 15/minute accept

        # Allow ICMP (ping)
        ip  protocol icmp  icmp  type echo-request accept
        ip6 nexthdr  icmpv6 icmpv6 type echo-request accept
    }

    chain output {
        type filter hook output priority 0; policy drop;

        # Allow loopback
        oifname "lo" accept

        # Allow established/related connections
        ct state established,related accept

        # Allow DNS, HTTP, HTTPS (including QUIC over UDP 443)
        tcp dport { 53, 80, 443 } accept
        udp dport { 53, 443 } accept

        # Allow ICMP outbound
        ip  protocol icmp  accept
        ip6 nexthdr  icmpv6 accept

        # Allow NTP
        udp dport 123 accept
    }

    chain forward {
        type filter hook forward priority 0; policy drop;
    }
}
```

```bash
sudo nft -f /etc/nftables.conf
sudo systemctl restart nftables
sudo nft list ruleset   # Verify rules are loaded
```

> **Explanation**: Several improvements over the original config:
> - SSH connections are rate-limited to 15 new connections/minute to slow brute-force attacks.
> - `ct state established,related accept` added to the output chain (without this, responses
>   to outbound connections could be dropped).
> - `ct state invalid drop` added to input to discard malformed packets.
> - UDP 443 (QUIC/HTTP3) added to output â€” required for modern browsers and applications.
> - NTP (UDP 123) added to output so the system clock can sync.
> - IPv6 ICMP rules included for proper IPv6 neighbour discovery.

---

## SSH Configuration

```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

### Recommended sshd_config Hardening

```bash
sudo nano /etc/ssh/sshd_config
```

Add or update the following lines:

```
PermitRootLogin no
PasswordAuthentication no   # Only after you have set up key-based auth!
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
X11Forwarding no
AllowTcpForwarding no
MaxAuthTries 3
LoginGraceTime 20
```

```bash
sudo systemctl restart ssh
```

> âš ï¸ **Important**: Set `PasswordAuthentication no` **only after** you have copied your
> public key to the server (`ssh-copy-id` or manually). Locking yourself out over SSH
> will require console access to recover.

---

## Generating SSH Keys

```bash
# Generate a modern Ed25519 key pair
ssh-keygen -t ed25519 -C "your_email@example.com"

# Start the SSH agent and add your key
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy your public key to a remote server
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@remote-host

# Add to GitHub / GitLab
cat ~/.ssh/id_ed25519.pub  # Copy this output and paste into your account settings
```

> **Comment**: Ed25519 is preferred over RSA â€” it's faster, smaller, and considered more
> secure. If you need RSA compatibility with an older system, use `ssh-keygen -t rsa -b 4096`.

---

Happy coding! ðŸš€
