# Ubuntu Developer Setup

A comprehensive step-by-step guide to installing and configuring Ubuntu for development
purposes. Suitable for fresh installs on personal laptops or desktops. Covers language
toolchains, IDEs, Docker, virtualization, embedded hardware tools, and more.

Tested on Ubuntu 22.04 LTS and 24.04 LTS.
Designed for software engineers, system programmers, and hobbyists starting with Linux development.

---

## Table of Contents

1.  [Flashing Ubuntu ISO to USB (on macOS)](#1-flashing-ubuntu-iso-to-usb-on-macos)
2.  [System Update](#2-system-update)
3.  [Essential Tools](#3-essential-tools)
4.  [Zsh and Oh My Zsh](#4-zsh-and-oh-my-zsh)
5.  [C/C++ Development Tools](#5-cc-development-tools)
6.  [Python Setup](#6-python-setup)
7.  [Rust Installation](#7-rust-installation)
8.  [Go Installation](#8-go-installation)
9.  [Node.js and Yarn](#9-nodejs-and-yarn)
10. [PostgreSQL Server](#10-postgresql-server)
11. [Neovim Configuration](#11-neovim-configuration)
12. [WezTerm Installation](#12-wezterm-installation)
13. [IDEs](#13-ides)
14. [Version Control Tools](#14-version-control-tools)
15. [API Testing Tools](#15-api-testing-tools)
16. [Wireshark](#16-wireshark)
17. [pgAdmin](#17-pgadmin)
18. [Docker and Docker Compose](#18-docker-and-docker-compose)
19. [VirtualBox](#19-virtualbox)
20. [STM32 Embedded Development (C/C++ and Rust)](#20-stm32-embedded-development-cc-and-rust)
21. [QEMU -- x86 Emulation](#21-qemu--x86-emulation)
22. [Leptos Setup](#22-leptos-setup)
23. [Tauri Setup](#23-tauri-setup)
24. [Firewall: nftables](#24-firewall-nftables)
25. [SSH Configuration](#25-ssh-configuration)
26. [SSH Key Generation](#26-ssh-key-generation)

---

## 1. Flashing Ubuntu ISO to USB (on macOS)

```bash
diskutil list
diskutil unmountDisk /dev/diskX
sudo dd if=~/Downloads/ubuntu-24.04.2-desktop-amd64.iso \
     of=/dev/rdiskX bs=1m status=progress
diskutil eject /dev/diskX
```

Replace `/dev/diskX` with the correct identifier from `diskutil list`. Use `/dev/rdiskX`
(the raw device node) for significantly faster write speeds. The `dd` command will silently
overwrite any device you specify -- double-check before running.

---

## 2. System Update

```bash
sudo apt update && sudo apt upgrade -y
sudo apt autoremove -y && sudo apt clean
```

Updates package lists and upgrades all installed packages. `autoremove` and `clean` remove
orphaned dependencies and cached package archives to free up disk space.

---

## 3. Essential Tools

```bash
sudo apt install -y \
  build-essential curl wget git unzip zip \
  htop btop neofetch \
  net-tools nmap tcpdump mtr traceroute \
  jq ripgrep fd-find bat tree \
  printer-driver-all cups cups-pdf sane xsane \
  gnome-disk-utility udisks2

# Automatic security updates
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades

# Cleanup
sudo apt autoremove -y && sudo apt clean
sudo journalctl --vacuum-time=7d
```

Notable additions beyond the basics: `ripgrep` (faster `grep`), `fd-find` (faster `find`),
`bat` (syntax-highlighted `cat`), `btop` (interactive resource monitor). Log vacuum is set
to 7 days -- adjust to taste.

---

## 4. Zsh and Oh My Zsh

```bash
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
chsh -s $(which zsh)   # Log out and back in for the change to take effect
```

### Plugins

```bash
# Inline command suggestions from history
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Syntax highlighting as you type
git clone https://github.com/zsh-users/zsh-syntax-highlighting \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### Powerlevel10k Theme

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

Run `p10k configure` after installation to set up its appearance interactively.

### ~/.zshrc snippet

```zsh
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
ZSH_THEME="powerlevel10k/powerlevel10k"
```

---

## 5. C/C++ Development Tools

```bash
sudo apt install -y gcc g++ gdb make cmake ninja-build
sudo apt install -y clang clang-format clang-tidy valgrind cppcheck
sudo apt install -y libboost-all-dev   # Optional -- large package, install only if needed
```

Installs GCC/Clang compilers, `gdb`, and build systems (`cmake`, `ninja-build`).
`clang-tidy` and `valgrind` are essential for static analysis and memory error detection.

---

## 6. Python Setup

### System Python

```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev python3-setuptools

# Expose 'python' as an alias for python3
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1

# Core development tools (installed per-user)
pip3 install --user black isort flake8 mypy pytest

# Optional: Jupyter
pip3 install --user jupyter
```

`black` and `isort` handle code and import formatting respectively. The `--user` flag
installs packages into `~/.local` to avoid conflicts with system Python packages.

---

### Managing Multiple Python Versions with pyenv

`pyenv` lets you install and switch between any number of Python versions without touching
the system Python. It is the recommended approach for projects that require specific versions.

**Install pyenv**

```bash
curl https://pyenv.run | bash
```

**Add to `~/.zshrc` (or `~/.bashrc`)**

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
exec $SHELL
```

**Install and select versions**

```bash
# List all available versions
pyenv install --list

# Install specific versions
pyenv install 3.11.9
pyenv install 3.12.3

# Set a global default
pyenv global 3.12.3

# Override for a single project directory
cd ~/my-project
pyenv local 3.11.9     # Creates .python-version in the current directory

# Check active version and its origin
pyenv version          # e.g. "3.11.9 (set by /home/user/my-project/.python-version)"
python --version

# List installed versions
pyenv versions
```

**Version resolution order:** shell > local (.python-version file) > global

---

### Virtual Environments (venv)

A virtual environment is an isolated directory containing a Python interpreter and its own
set of installed packages. Use one for every project to keep dependencies separate.

**Creating and activating a virtual environment**

```bash
# Create a virtual environment named .venv in the current project directory
python -m venv .venv

# Activate it (all subsequent pip installs go into .venv)
source .venv/bin/activate

# Your prompt will show (.venv) to indicate the environment is active
(.venv) $ python --version
(.venv) $ pip install requests flask

# Deactivate when done
deactivate
```

**Using pyenv + venv together**

When `pyenv local` is set, `python` resolves to that version. Creating a venv then
captures that exact interpreter:

```bash
cd ~/my-project
pyenv local 3.11.9          # Pin Python version for this directory
python -m venv .venv        # venv uses pyenv's 3.11.9 interpreter
source .venv/bin/activate
python --version            # 3.11.9
```

**Freezing and restoring dependencies**

```bash
# Save current environment's packages to a file
pip freeze > requirements.txt

# Reproduce the environment on another machine
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

**Using venv with VS Code**

Open the project folder in VS Code, then select the interpreter via the Command Palette:
`Python: Select Interpreter` -- choose the `.venv/bin/python` entry. VS Code will activate
the environment automatically in its integrated terminal.

> Add `.venv/` to `.gitignore` -- virtual environments should not be committed to version
> control. Only `requirements.txt` (or `pyproject.toml`) belongs in the repository.

---

### Alternative: uv (modern Python package manager)

`uv` is a fast drop-in replacement for `pip` and `venv`, written in Rust. It resolves and
installs packages significantly faster and integrates cleanly into existing workflows.

```bash
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Create a project with a specific Python version (downloads it automatically if needed)
uv init my-project
cd my-project
uv python pin 3.12.3        # Pins the version in .python-version

# Add and remove dependencies (updates pyproject.toml and uv.lock)
uv add requests flask
uv remove flask

# Run a command inside the managed environment (no manual activation needed)
uv run python main.py
uv run pytest

# Sync environment to match pyproject.toml
uv sync

# Install a tool globally without polluting system Python
uv tool install ruff
uv tool install black
```

`uv` manages its own virtual environments transparently -- you rarely need to activate one
manually. It is compatible with `pyproject.toml`, `requirements.txt`, and PEP 517 build backends.

---

## 7. Rust Installation

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

# Formatting and linting (installed as toolchain components, not separate binaries)
rustup component add rustfmt clippy

# Useful cargo extensions
cargo install cargo-watch    # Re-run on file changes
cargo install cargo-audit    # Check dependencies for known vulnerabilities
cargo install cargo-expand   # Expand macros for debugging
```

`cargo-audit` is worth running periodically in any project: `cargo audit`.

---

## 8. Go Installation

```bash
# Check https://go.dev/dl/ for the latest version before running
GO_VERSION="1.22.4"
wget "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go${GO_VERSION}.linux-amd64.tar.gz"
rm "go${GO_VERSION}.linux-amd64.tar.gz"
```

**Add to `~/.zshrc`**

```bash
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.zshrc
source ~/.zshrc
go version
```

Installing from the official archive ensures the latest stable release. The `apt` package
is typically several versions behind.

---

## 9. Node.js and Yarn

```bash
# Node.js 20 LTS via NodeSource
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

node --version
npm --version

# Yarn via npm (the apt-key method is deprecated)
npm install -g yarn
yarn --version
```

### NVM -- Node Version Manager (optional)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.zshrc
nvm install --lts
nvm use --lts
```

NVM is useful for projects that require specific Node.js versions and takes precedence over
the system-installed Node.

---

## 10. PostgreSQL Server

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib
sudo systemctl enable --now postgresql
```

### Create a Development User and Database (optional)

```bash
sudo -u postgres psql -c "CREATE USER myuser WITH PASSWORD 'mypassword';"
sudo -u postgres psql -c "CREATE DATABASE mydb OWNER myuser;"
```

Replace `myuser`, `mypassword`, and `mydb` with your own values.

---

## 11. Neovim Configuration

### Install

```bash
# The official PPA provides the latest stable release
sudo add-apt-repository ppa:neovim-ppa/stable -y
sudo apt update && sudo apt install -y neovim
```

### Nerd Font

```bash
mkdir -p ~/.local/share/fonts
wget -O ~/.local/share/fonts/FiraCodeNerdFont-Regular.ttf \
  "https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/FiraCode/Regular/FiraCodeNerdFontMono-Regular.ttf"
fc-cache -fv
```

Nerd Fonts provide powerline glyphs and icons required by most Neovim themes and status line plugins.

### Plugin Manager: lazy.nvim

`lazy.nvim` is the current standard plugin manager. It self-bootstraps -- paste the following
into `~/.config/nvim/init.lua`:

```lua
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
vim.opt.number     = true
vim.opt.expandtab  = true
vim.opt.shiftwidth = 2
```

For a detailed beginner-friendly configuration see:
https://www.josean.com/posts/how-to-setup-neovim-2024

---

## 12. WezTerm Installation

```bash
curl -fsSL https://apt.fury.io/wez/gpg.key \
  | sudo gpg --yes --dearmor -o /usr/share/keyrings/wezterm-fury.gpg
echo 'deb [signed-by=/usr/share/keyrings/wezterm-fury.gpg] https://apt.fury.io/wez/ * *' \
  | sudo tee /etc/apt/sources.list.d/wezterm.list
sudo apt update && sudo apt install -y wezterm
```

WezTerm is a GPU-accelerated terminal with built-in multiplexing and Lua-based configuration.
The `ppa:wez/ppa` PPA has been superseded by the Fury repository above.

Configuration guide: https://www.josean.com/posts/wezterm

---

## 13. IDEs

```bash
# Visual Studio Code
sudo snap install --classic code

# PyCharm Community Edition
sudo snap install pycharm-community --classic

# JetBrains Toolbox -- manages RustRover, CLion, IntelliJ IDEA, and others
# Download the latest tarball from: https://www.jetbrains.com/toolbox-app/
wget -O /tmp/jetbrains-toolbox.tar.gz \
  "https://data.services.jetbrains.com/products/download?platform=linux&code=TBA"
tar -xzf /tmp/jetbrains-toolbox.tar.gz -C /tmp
/tmp/jetbrains-toolbox-*/jetbrains-toolbox
```

Hardcoded version URLs for JetBrains products go stale quickly. Always pull the latest
Toolbox release from jetbrains.com/toolbox-app rather than embedding a specific version string.

---

## 14. Version Control Tools

```bash
# GitKraken -- cross-platform GUI Git client
wget https://release.gitkraken.com/linux/gitkraken-amd64.deb -O /tmp/gitkraken.deb
sudo dpkg -i /tmp/gitkraken.deb
sudo apt install -f   # Resolve any missing dependencies

# tig -- terminal-based Git log and blame browser
sudo apt install -y tig
```

### Git Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor nvim
git config --global init.defaultBranch main
git config --global pull.rebase false       # Merge on pull; set 'true' to rebase instead
git config --global core.autocrlf input     # Normalize CRLF to LF on commit
```

### Global .gitignore

```bash
curl https://raw.githubusercontent.com/github/gitignore/main/Global/Linux.gitignore \
  >> ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

`~/.gitignore_global` is the conventional name. Avoid `~/.gitignore` -- some tools treat
that as a project-level ignore file.

---

## 15. API Testing Tools

```bash
# Postman -- GUI API client
sudo snap install postman

# HTTPie -- expressive CLI HTTP client
sudo apt install -y httpie

# Bruno -- open-source alternative; stores collections as plain files in your repo
sudo snap install bruno
```

Bruno's file-based storage makes API collections version-controllable alongside your code,
without requiring a cloud account.

---

## 16. Wireshark

```bash
sudo apt install -y wireshark
sudo usermod -aG wireshark $USER
# Log out and back in for the group change to take effect
```

Adding your user to the `wireshark` group allows packet capture without `sudo`, which is
safer than running the GUI as root.

---

## 17. pgAdmin

```bash
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub \
  | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] \
  https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" \
  | sudo tee /etc/apt/sources.list.d/pgadmin4.list

sudo apt update && sudo apt install -y pgadmin4
```

After installation, run `sudo /usr/pgadmin4/bin/setup-web.sh` to configure the web server.

---

## 18. Docker and Docker Compose

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

# Install Docker Engine and the Compose plugin
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

# Allow running Docker without sudo
sudo usermod -aG docker $USER
newgrp docker   # Apply group membership in the current session
```

This installs from the official Docker repository rather than Ubuntu's `docker.io` package.
The `docker-compose-plugin` provides the `docker compose` (v2) subcommand -- the standalone
`docker-compose` binary is no longer needed.

---

## 19. VirtualBox

```bash
# Prerequisites
sudo apt install -y build-essential dkms linux-headers-$(uname -r)

# Official repository
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- \
  | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] \
  http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" \
  | sudo tee /etc/apt/sources.list.d/virtualbox.list

sudo apt update
sudo apt install -y virtualbox-7.1   # Verify the latest package name at virtualbox.org

sudo usermod -aG vboxusers $USER
sudo reboot
```

The Extension Pack version must exactly match the installed VirtualBox version.
Download it from https://www.virtualbox.org/wiki/Linux_Downloads.

---

## 20. STM32 Embedded Development (C/C++ and Rust)

This section covers the full toolchain for STM32 bare-metal development: compilers,
on-chip debuggers, flashing utilities, udev rules, VS Code integration, and QEMU emulation
-- for both C/C++ and Rust workflows.

---

### Toolchain Installation

```bash
sudo apt update

# ARM bare-metal compiler, linker, and debugger
sudo apt install -y gcc-arm-none-eabi gdb-multiarch binutils-arm-none-eabi

# Build systems
sudo apt install -y make cmake ninja-build

# ST-Link host utilities: st-flash, st-info, st-util
sudo apt install -y stlink-tools

# OpenOCD -- on-chip debugger, supports ST-Link, J-Link, CMSIS-DAP
sudo apt install -y openocd

# QEMU ARM (for running firmware without hardware)
sudo apt install -y qemu-system-arm

# libusb (only needed if building OpenOCD from source)
sudo apt install -y libusb-1.0-0-dev
```

> The `gcc-arm-none-eabi` package from Ubuntu repos is typically 1-2 major versions behind.
> For the latest release download directly from:
> https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads
> and prepend the `bin/` directory to your `PATH`.

---

### udev Rules (USB Access Without sudo)

```bash
# ST-Link V1 / V2 / V2-1 / V3
sudo tee /etc/udev/rules.d/49-stlink.rules > /dev/null << 'EOF'
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3744", MODE="0666", GROUP="plugdev"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", MODE="0666", GROUP="plugdev"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", MODE="0666", GROUP="plugdev"
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

sudo udevadm control --reload-rules
sudo udevadm trigger

sudo usermod -aG dialout,plugdev $USER
# Log out and back in for group changes to take effect
```

---

### C/C++ Project Workflow

#### CMake Toolchain File

Create `cmake/arm-none-eabi.cmake` in your project root:

```cmake
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR arm)

set(CMAKE_C_COMPILER   arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_OBJCOPY      arm-none-eabi-objcopy)
set(CMAKE_SIZE         arm-none-eabi-size)

set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# Cortex-M4 with hard FPU (STM32F4xx) -- adjust for your target
set(CPU_FLAGS "-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard")

set(CMAKE_C_FLAGS_INIT            "${CPU_FLAGS}")
set(CMAKE_CXX_FLAGS_INIT          "${CPU_FLAGS}")
set(CMAKE_ASM_FLAGS_INIT          "${CPU_FLAGS}")
set(CMAKE_EXE_LINKER_FLAGS_INIT   "${CPU_FLAGS} -specs=nano.specs -specs=nosys.specs")
```

Build:

```bash
cmake -B build -DCMAKE_TOOLCHAIN_FILE=cmake/arm-none-eabi.cmake -GNinja
ninja -C build
arm-none-eabi-size build/firmware.elf
```

#### Flashing via st-flash

```bash
# Convert ELF to raw binary
arm-none-eabi-objcopy -O binary build/firmware.elf build/firmware.bin

# Erase and write
st-flash --reset write build/firmware.bin 0x08000000

# Erase the entire flash
st-flash erase

# Show connected probe information
st-info --probe
```

#### Flashing via OpenOCD

```bash
openocd \
  -f interface/stlink.cfg \
  -f target/stm32f4x.cfg \
  -c "program build/firmware.elf verify reset exit"
```

---

### Debugging with GDB and OpenOCD

**Terminal 1 -- Start the OpenOCD GDB server**

```bash
openocd \
  -f interface/stlink.cfg \
  -f target/stm32f4x.cfg
# Listens on port 3333 (GDB) and 4444 (telnet)
```

**Terminal 2 -- Connect GDB**

```bash
gdb-multiarch build/firmware.elf \
  -ex "target extended-remote :3333" \
  -ex "monitor reset halt" \
  -ex "load" \
  -ex "monitor reset init"
```

**Useful GDB commands for embedded targets**

```
monitor reset halt      Reset and halt the CPU
monitor reset run       Reset and run freely
load                    Flash the ELF to the target
b main                  Set a breakpoint at main()
c                       Continue execution
p variable_name         Print a variable's value
x/10xw 0x20000000       Examine 10 words at SRAM base address
info registers          Show all CPU registers
set $pc = 0x08000000    Manually set the program counter
disassemble             Disassemble at the current location
```

**Project .gdbinit**

Create `.gdbinit` in the project root for a one-command debug launch:

```
target extended-remote :3333
monitor reset halt
load
monitor reset init
break main
continue
```

Then launch with: `gdb-multiarch build/firmware.elf`

To allow project-local `.gdbinit` files, add this to `~/.gdbinit`:

```
set auto-load safe-path /
```

---

### Rust Embedded Workflow

#### Targets and Tooling

```bash
# Cortex-M0/M0+ -- STM32F0, G0
rustup target add thumbv6m-none-eabi

# Cortex-M4/M7 without FPU
rustup target add thumbv7em-none-eabi

# Cortex-M4/M7 with hard FPU -- STM32F4, F7, H7 (most common)
rustup target add thumbv7em-none-eabihf

# Cortex-M33 -- STM32L5, U5, WB
rustup target add thumbv8m.main-none-eabihf

# cargo-binutils -- llvm-based size, objcopy, etc. for Rust targets
cargo install cargo-binutils
rustup component add llvm-tools-preview

# probe-rs -- modern flash, GDB server, and RTT console in one tool
curl --proto '=https' --tlsv1.2 -LsSf \
  https://github.com/probe-rs/probe-rs/releases/latest/download/probe-rs-tools-installer.sh | sh

# flip-link -- stack overflow detection at zero runtime cost
cargo install flip-link
```

#### Project Configuration

`.cargo/config.toml`:

```toml
[target.thumbv7em-none-eabihf]
# probe-rs flashes the binary and opens the RTT console automatically
runner = "probe-rs run --chip STM32F411CEUx"

# To use QEMU instead (no hardware required):
# runner = "qemu-system-arm -cpu cortex-m4 -machine lm3s6965evb -nographic \
#           -semihosting-config enable=on,target=native -kernel"

rustflags = [
  "-C", "link-arg=-Tlink.x",
  "-C", "link-arg=-Tdefmt.x",  # Remove if not using defmt
]

[build]
target = "thumbv7em-none-eabihf"
```

`Cargo.toml` (typical dependencies):

```toml
[dependencies]
cortex-m       = { version = "0.7", features = ["critical-section-single-core"] }
cortex-m-rt    = "0.7"
panic-probe    = { version = "0.3", features = ["print-defmt"] }
defmt          = "0.3"
defmt-rtt      = "0.4"
stm32f4xx-hal  = { version = "0.21", features = ["stm32f411"] }

[profile.release]
codegen-units = 1
debug         = 2        # Retain debug symbols in release builds for GDB
lto           = "fat"
opt-level     = "z"      # Minimise binary size; use "3" for maximum speed
```

#### Flashing and Running

```bash
# Flash and open RTT log stream (runner is invoked by cargo)
cargo run --release

# Flash only, without opening a console
probe-rs download --chip STM32F411CEUx \
  target/thumbv7em-none-eabihf/release/firmware

# Show information about the connected probe and target
probe-rs info

# List all connected debug probes
probe-rs list
```

#### Debugging Rust with probe-rs and GDB

```bash
# Terminal 1 -- start the GDB server
probe-rs gdb --chip STM32F411CEUx

# Terminal 2 -- connect GDB
gdb-multiarch target/thumbv7em-none-eabihf/release/firmware \
  -ex "target extended-remote :1337" \
  -ex "monitor reset halt" \
  -ex "load" \
  -ex "break main" \
  -ex "continue"
```

#### RTT Logging with defmt

`defmt` provides structured, efficient logging over RTT (Real-Time Transfer) with near-zero
overhead. Log output appears in the terminal when running via `cargo run`.

```rust
use defmt::*;
use defmt_rtt as _;
use panic_probe as _;

#[cortex_m_rt::entry]
fn main() -> ! {
    info!("Device started");
    let value: u32 = 42;
    debug!("counter = {}", value);
    loop {}
}
```

---

### VS Code Integration

```bash
code --install-extension marus25.cortex-debug
code --install-extension rust-lang.rust-analyzer
code --install-extension ms-vscode.cpptools
code --install-extension probe-rs.probe-rs-debugger
```

**`launch.json` for C/C++ (Cortex-Debug + OpenOCD)**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "STM32 -- OpenOCD",
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

**`launch.json` for Rust (probe-rs)**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "STM32 -- probe-rs",
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

### SVD Files -- Peripheral Register Viewer

SVD files map peripheral registers (GPIO, USART, TIM, ADC, etc.) to human-readable names.
Cortex-Debug uses them to populate a live **Peripherals** panel in VS Code during a debug session.

```bash
mkdir -p ~/stm32-svd && cd ~/stm32-svd
# Example for STM32F411 -- find other chips in the same repository
wget "https://raw.githubusercontent.com/posborne/cmsis-svd/master/data/STMicro/STM32F411.svd"
```

Set the `svdFile` key in `launch.json` to the downloaded path.

---

### QEMU -- ARM Emulation (No Hardware Required)

```bash
# Run an ELF directly in QEMU (lm3s6965evb is well-supported for Cortex-M3/M4 code testing)
qemu-system-arm \
  -cpu cortex-m4 \
  -machine lm3s6965evb \
  -nographic \
  -semihosting-config enable=on,target=native \
  -kernel build/firmware.elf

# Same, but with a GDB stub -- pauses at startup and waits for a debugger on port 1234
qemu-system-arm \
  -cpu cortex-m4 \
  -machine lm3s6965evb \
  -nographic \
  -semihosting-config enable=on,target=native \
  -kernel build/firmware.elf \
  -s -S
# Connect: gdb-multiarch build/firmware.elf -ex "target remote :1234"
```

QEMU does not emulate STM32-specific peripherals (GPIO, SPI, I2C, etc.). It is suitable
for testing CPU logic, algorithms, and semihosting I/O. For peripheral-level emulation,
consider Renode (https://renode.io/), which has partial STM32 support.

---

## 21. QEMU -- x86 Emulation

```bash
sudo apt update
sudo apt install -y qemu-system-x86 qemu-utils
```

`qemu-utils` provides `qemu-img` for creating and converting disk images. For full KVM
hardware acceleration:

```bash
sudo apt install -y qemu-kvm libvirt-daemon-system
sudo usermod -aG kvm,libvirt $USER
```

---

## 22. Leptos Setup

```bash
rustup toolchain install nightly
rustup target add wasm32-unknown-unknown
cargo install cargo-leptos
```

Leptos is a full-stack Rust framework for reactive web applications (SSR + WASM client-side).
The `wasm32-unknown-unknown` target is required for the client-side bundle.

---

## 23. Tauri Setup

```bash
# System dependencies
sudo apt install -y \
  libwebkit2gtk-4.1-dev libssl-dev libgtk-3-dev \
  libayatana-appindicator3-dev librsvg2-dev

# Tauri CLI
npm install -g @tauri-apps/cli
npx tauri --version
```

On Ubuntu 22.04, replace `libwebkit2gtk-4.1-dev` with `libwebkit2gtk-4.0-dev`. Tauri
builds lightweight desktop applications with a web frontend and a Rust backend.

---

## 24. Firewall: nftables

> **Warning**: Complete the steps below in one session. Your system has no firewall between
> removing UFW/iptables and loading the nftables ruleset.

### Remove UFW and iptables

```bash
sudo ufw disable
sudo apt remove --purge -y ufw

sudo iptables  -F && sudo iptables  -X && sudo iptables  -Z
sudo ip6tables -F && sudo ip6tables -X

sudo apt remove --purge -y iptables
```

### Install and Configure nftables

```bash
sudo apt install -y nftables
sudo systemctl enable --now nftables
sudo nano /etc/nftables.conf
```

**`/etc/nftables.conf`**

```nft
#!/usr/sbin/nft -f
flush ruleset

table inet filter {

    chain input {
        type filter hook input priority 0; policy drop;

        iifname "lo"                          accept
        ct state established,related          accept
        ct state invalid                      drop

        # SSH -- rate-limited to slow brute-force attempts
        tcp dport 22 ct state new limit rate 15/minute accept

        # ICMP
        ip  protocol icmp  icmp  type echo-request accept
        ip6 nexthdr  icmpv6 icmpv6 type echo-request accept
    }

    chain output {
        type filter hook output priority 0; policy drop;

        oifname "lo"                          accept
        ct state established,related          accept

        # DNS, HTTP, HTTPS (TCP and UDP 443 for QUIC/HTTP3)
        tcp dport { 53, 80, 443 }             accept
        udp dport { 53, 443 }                 accept

        # NTP
        udp dport 123                         accept

        ip  protocol icmp                     accept
        ip6 nexthdr  icmpv6                   accept
    }

    chain forward {
        type filter hook forward priority 0; policy drop;
    }
}
```

```bash
sudo nft -f /etc/nftables.conf
sudo systemctl restart nftables
sudo nft list ruleset   # Verify rules loaded correctly
```

---

## 25. SSH Configuration

```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

### Harden sshd_config

```bash
sudo nano /etc/ssh/sshd_config
```

Recommended settings:

```
PermitRootLogin         no
PasswordAuthentication  no      # Set only AFTER key-based auth is working
PubkeyAuthentication    yes
AuthorizedKeysFile      .ssh/authorized_keys
X11Forwarding           no
AllowTcpForwarding      no
MaxAuthTries            3
LoginGraceTime          20
```

```bash
sudo systemctl restart ssh
```

Set `PasswordAuthentication no` only after you have confirmed key-based login works.
Losing SSH access with no console fallback requires physical access to recover.

---

## 26. SSH Key Generation

```bash
# Generate an Ed25519 key pair
ssh-keygen -t ed25519 -C "your_email@example.com"

# Start the agent and register the key
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Deploy the public key to a remote host
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@remote-host

# Copy the public key for GitHub / GitLab
cat ~/.ssh/id_ed25519.pub
```

Ed25519 is preferred over RSA: smaller keys, faster operations, equivalent security.
For legacy systems requiring RSA: `ssh-keygen -t rsa -b 4096`.

---

Happy coding.
