# Ubuntu Developer Setup for Beginners

This guide provides a step-by-step process for setting up a comprehensive development environment on Ubuntu, tailored for beginner developers. It covers everything from installing the operating system to configuring essential tools, programming languages, IDEs, and utilities, ensuring a robust setup for software development.

✅ Suitable for Ubuntu 24.04 LTS and 26.04 LTS
💡 Designed for software engineers, system programmers, embedded developers, and hobbyists starting with Linux development.

## Table of Contents

1. [Flashing Ubuntu ISO to USB (on macOS)](#flashing-ubuntu-iso-to-usb-on-macos)
2. [System Update](#system-update)
3. [Essential Tools Installation](#essential-tools-installation)
4. [Installing Zsh and Oh My Zsh](#installing-zsh-and-oh-my-zsh)
5. [Modern CLI Utilities](#modern-cli-utilities)
6. [Git and Version Control Tools](#git-and-version-control-tools)
7. [Generating SSH Keys](#generating-ssh-keys)
8. [C/C++ Development Tools](#cc-development-tools)
9. [Rust Installation and Ecosystem](#rust-installation-and-ecosystem)
10. [Embedded Rust Development](#embedded-rust-development)
11. [STM32 Development Tools (C/C++ Toolchain)](#stm32-development-tools-cc-toolchain)
12. [Python Setup](#python-setup)
13. [Go Installation](#go-installation)
14. [Node.js Setup](#nodejs-setup)
15. [PostgreSQL Server Setup](#postgresql-server-setup)
16. [pgAdmin Installation](#pgadmin-installation)
17. [Neovim Configuration](#neovim-configuration)
18. [WezTerm Installation](#wezterm-installation)
19. [Installing IDEs](#installing-ides)
20. [API Testing Tools](#api-testing-tools)
21. [Wireshark Installation](#wireshark-installation)
22. [Docker and Docker Compose](#docker-and-docker-compose)
23. [KVM + QEMU + libvirt Installation (Recommended)](#kvm--qemu--libvirt-installation-recommended)
24. [QEMU Installation](#qemu-installation)
25. [VirtualBox Installation](#virtualbox-installation)
26. [Rust GUI/Web Frameworks: Leptos and Tauri](#rust-guiweb-frameworks-leptos-and-tauri)
27. [SSH Server Configuration](#ssh-server-configuration)
28. [nftables Firewall Configuration](#nftables-firewall-configuration)
29. [System Maintenance and Automatic Updates](#system-maintenance-and-automatic-updates)

---

## Flashing Ubuntu ISO to USB (on macOS)

```bash
diskutil list
diskutil unmountDisk /dev/diskX
sudo dd if=~/Downloads/ubuntu-24.04.2-desktop-amd64.iso of=/dev/rdiskX bs=4m status=progress
diskutil eject /dev/diskX
```

> **Note**: Replace `diskX` with the correct disk identifier for your USB drive, found using `diskutil list`. Using the raw device (`/dev/rdiskX`) is significantly faster than `/dev/diskX`. Be cautious, as `dd` can overwrite data if the wrong device is specified. GUI alternatives: [balenaEtcher](https://etcher.balena.io/) or the official [Raspberry Pi Imager](https://www.raspberrypi.com/software/).

---

## System Update

```bash
sudo apt update && sudo apt full-upgrade -y
```

> **Explanation**: Updates the package lists and upgrades all installed packages. `full-upgrade` also handles changed dependencies (e.g., kernel updates), which plain `upgrade` may hold back.

---

## Essential Tools Installation

```bash
sudo apt install -y build-essential curl wget git unzip zip jq
sudo apt install -y htop btop net-tools fastfetch
sudo apt install -y nmap tcpdump mtr-tiny traceroute
sudo apt install -y gnome-disk-utility udisks2
sudo apt install -y printer-driver-all cups cups-pdf sane xsane # Optional: printing/scanning
```

> **Comment**: Installs core build tools (`build-essential`), download/archive utilities, system monitors (`htop`, `btop`), and network diagnostics (`nmap`, `tcpdump`, `mtr`). `btop` and `fastfetch` are the actively maintained successors of `bpytop` and the discontinued `neofetch`. Printing/scanning packages are only needed on desktop machines with printers.

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
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

> **Explanation**: `zsh-autosuggestions` provides real-time command suggestions based on your history; `zsh-syntax-highlighting` colorizes commands as you type, catching typos before you press Enter.

### Powerlevel10k Theme

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

> **Comment**: Powerlevel10k is a highly customizable and fast Zsh theme. Run `p10k configure` after installation to customize its appearance. It requires a Nerd Font (see the [Neovim section](#neovim-configuration) for font installation).

### Sample ~/.zshrc

```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
ZSH_THEME="powerlevel10k/powerlevel10k"
```

---

## Modern CLI Utilities

```bash
sudo apt install -y ripgrep fd-find bat fzf zoxide tmux
cargo install eza # After installing Rust (see below), or: sudo apt install -y eza (Ubuntu 24.10+)
```

> **Explanation**: Modern replacements for classic Unix tools, widely adopted in current development workflows:
> - `ripgrep` (`rg`) – dramatically faster `grep` that respects `.gitignore`.
> - `fd-find` (`fdfind`) – intuitive, fast alternative to `find`.
> - `bat` (`batcat`) – `cat` with syntax highlighting and Git integration.
> - `fzf` – fuzzy finder for files, history, and processes (`Ctrl+R` on steroids).
> - `zoxide` – smarter `cd` that learns your habits (add `eval "$(zoxide init zsh)"` to `~/.zshrc`).
> - `eza` – modern `ls` replacement with colors, icons, and Git status.
> - `tmux` – terminal multiplexer for persistent sessions.

> **Note**: On Ubuntu, the binaries are named `fdfind` and `batcat` due to naming conflicts. Add aliases to `~/.zshrc`: `alias fd=fdfind bat=batcat`.

---

## Git and Version Control Tools

### Git Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor nvim
git config --global init.defaultBranch main
git config --global pull.rebase true
```

> **Note**: Configures Git with your identity, sets Neovim as the default editor, uses `main` as the default branch, and prefers rebase on `git pull` for a linear history.

### Global .gitignore

```bash
curl https://raw.githubusercontent.com/github/gitignore/main/Global/Linux.gitignore >> ~/.gitignore
git config --global core.excludesfile ~/.gitignore
```

### CLI and GUI Clients

```bash
sudo apt install -y tig git-delta
# lazygit — popular TUI for Git
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | jq -r '.tag_name | ltrimstr("v")')
curl -Lo /tmp/lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
sudo tar -C /usr/local/bin -xzf /tmp/lazygit.tar.gz lazygit
# GitKraken — GUI client (optional)
wget https://release.gitkraken.com/linux/gitkraken-amd64.deb
sudo apt install -y ./gitkraken-amd64.deb
```

> **Comment**: `tig` is a text-based Git browser, `lazygit` is a full-featured Git TUI, and `git-delta` provides beautiful side-by-side diffs (add `pager = delta` under `[core]` in `~/.gitconfig`). GitKraken is an optional GUI client. `gh` (GitHub CLI) can be installed with `sudo apt install -y gh`.

---

## Generating SSH Keys

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub # Copy this to GitHub/GitLab → Settings → SSH Keys
```

> **Comment**: Generates an ED25519 SSH key pair for secure authentication (used by GitHub, GitLab, and remote servers) and adds it to the SSH agent. Do this early — you will need it to clone private repositories.

---

## C/C++ Development Tools

```bash
sudo apt install -y gcc g++ gdb make cmake ninja-build pkg-config
sudo apt install -y clang clangd clang-format clang-tidy lldb
sudo apt install -y valgrind cppcheck ccache
sudo apt install -y libboost-all-dev # Optional
```

> **Explanation**: Installs both major toolchains (`gcc`/`g++` and `clang`), debuggers (`gdb`, `lldb`), build systems (`make`, `cmake`, `ninja-build`), the `clangd` language server (used by VS Code/Neovim for code intelligence), code analysis tools (`clang-tidy`, `valgrind`, `cppcheck`), and `ccache` for faster rebuilds. `libboost-all-dev` is optional for projects requiring the Boost library.

---

## Rust Installation and Ecosystem

### Toolchain

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"
rustup component add rust-analyzer rust-src llvm-tools
rustc --version && cargo --version
```

> **Comment**: Installs Rust via `rustup`, the official toolchain manager. `rustfmt` (formatter) and `clippy` (linter) are included by default with the standard profile — they are rustup *components*, not crates, so never install them via `cargo install`. Additionally:
> - `rust-analyzer` – the official language server for IDE/editor integration.
> - `rust-src` – standard library sources, required for `rust-analyzer` and `build-std` (embedded/no_std work).
> - `llvm-tools` – needed by `cargo-binutils` and coverage tools.
>
> Useful toolchain commands: `rustup update` (update all toolchains), `rustup show` (list installed toolchains/targets), `rustup toolchain install nightly` (add nightly for experiments).

### cargo-binstall — Fast Tool Installation

```bash
curl -L --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash
```

> **Explanation**: `cargo binstall` downloads prebuilt binaries instead of compiling every tool from source, turning multi-minute `cargo install` runs into seconds. All tools below can be installed with either `cargo install` or the much faster `cargo binstall`.

### Essential Development Tools

```bash
cargo binstall -y cargo-nextest cargo-edit cargo-expand bacon
cargo binstall -y cargo-audit cargo-deny cargo-machete
cargo binstall -y cargo-llvm-cov cargo-generate
```

> **Explanation**:
> - `cargo-nextest` – next-generation test runner, significantly faster than `cargo test` with better output.
> - `cargo-edit` – provides `cargo upgrade` for bumping dependency versions (`cargo add`/`cargo remove` are built into cargo since 1.62).
> - `cargo-expand` – expands macros and shows the generated Rust code.
> - `bacon` – background code checker that re-runs `check`/`clippy`/`test` on file save (the actively maintained successor to `cargo-watch`, which is deprecated).
> - `cargo-audit` – scans `Cargo.lock` for crates with known security vulnerabilities.
> - `cargo-deny` – enforces license, source, and advisory policies across the dependency tree.
> - `cargo-machete` – finds unused dependencies in `Cargo.toml`.
> - `cargo-llvm-cov` – source-based code coverage.
> - `cargo-generate` – scaffolds new projects from templates (essential for embedded quickstarts).

### Faster Builds: mold Linker and sccache

```bash
sudo apt install -y mold clang
cargo binstall -y sccache
```

Add to `~/.cargo/config.toml`:

```toml
[target.x86_64-unknown-linux-gnu]
linker = "clang"
rustflags = ["-C", "link-arg=-fuse-ld=mold"]

[build]
rustc-wrapper = "sccache"
```

> **Comment**: `mold` is a drop-in linker that is many times faster than the default `ld`, noticeably speeding up incremental builds of large projects. `sccache` caches compilation artifacts across projects.

### Debugging Rust on the Host

```bash
sudo apt install -y gdb lldb
```

> **Note**: `rust-gdb` and `rust-lldb` (installed with the toolchain) wrap the system debuggers with Rust pretty-printers. In VS Code, install the **CodeLLDB** extension for a first-class Rust debugging experience; in Neovim, use `nvim-dap` with `codelldb`. For CLI-first debugging, `gdb -tui` or `lldb` work directly on `target/debug/` binaries.

---

## Embedded Rust Development

### Compilation Targets

```bash
# Cortex-M targets — pick the ones matching your chips:
rustup target add thumbv6m-none-eabi        # Cortex-M0/M0+ (e.g., STM32F0, RP2040)
rustup target add thumbv7m-none-eabi        # Cortex-M3 (e.g., STM32F1)
rustup target add thumbv7em-none-eabi       # Cortex-M4/M7 without FPU
rustup target add thumbv7em-none-eabihf     # Cortex-M4F/M7F with FPU (STM32F4/F7/H7)
rustup target add thumbv8m.main-none-eabihf # Cortex-M33 (e.g., STM32L5/U5, nRF9160)
rustup target add riscv32imac-unknown-none-elf # RISC-V MCUs (e.g., ESP32-C6, GD32V)
```

> **Comment**: For STM32F4xx use `thumbv7em-none-eabihf`. Modern embedded Rust is built around the async **[Embassy](https://embassy.dev/)** framework and the `stm32f4xx-hal`/`embedded-hal` ecosystem — both work with the targets above.

### Flashing, Debugging, and Binary Analysis Tools

```bash
# Modern flashing and debugging toolset (includes cargo-flash and cargo-embed)
cargo binstall -y probe-rs-tools

# Convenient wrappers around llvm binutils (cargo size, cargo objdump, etc.)
cargo binstall -y cargo-binutils

# Stack overflow protection for embedded programs
cargo binstall -y flip-link
```

> **Explanation**:
> - `probe-rs-tools` – flashing, debugging, and erasing microcontrollers using a wide range of debug probes (ST-Link, J-Link, CMSIS-DAP). It ships the `probe-rs`, `cargo-flash`, and `cargo-embed` binaries in one package — do **not** install `cargo-flash`/`cargo-embed` separately, and it replaces OpenOCD in most Rust workflows.
> - `cargo-binutils` – provides `cargo size`, `cargo objdump`, `cargo nm` for analyzing firmware binaries (requires the `llvm-tools` rustup component installed above).
> - `flip-link` – zero-cost stack overflow protection by placing the stack at the end of RAM.

### udev Rules and Permissions

```bash
sudo curl -L https://probe.rs/files/69-probe-rs.rules -o /etc/udev/rules.d/69-probe-rs.rules
sudo udevadm control --reload
sudo udevadm trigger
sudo usermod -aG dialout $USER # For USB-serial adapters; re-login to apply
```

> **Note**: Without the udev rules, `probe-rs` can only access debug probes as root. The `dialout` group grants access to `/dev/ttyUSB*`/`/dev/ttyACM*` serial ports.

### Project Scaffolding

```bash
# Classic bare-metal template
cargo generate --git https://github.com/rust-embedded/cortex-m-quickstart

# Or start an async Embassy project from its examples:
# https://github.com/embassy-rs/embassy/tree/main/examples
```

### Using `probe-rs` for Flashing and Debugging

| Command | Description |
|---------|-------------|
| `probe-rs list` | List all connected debug probes (shows VID:PID and serial number) |
| `probe-rs chip list \| grep -i stm32f7` | Find the exact chip name for `--chip` |
| `probe-rs run --chip <CHIP> target/thumbv7em-none-eabihf/debug/myapp` | Flash and run the firmware (with RTT/defmt log output) |
| `probe-rs attach --chip <CHIP> target/.../myapp` | Attach to a running target without reflashing |
| `probe-rs erase --chip <CHIP>` | Erase the entire flash of the target chip |
| `probe-rs download --chip <CHIP> target/.../myapp` | Flash without running |
| `probe-rs gdb --chip <CHIP>` | Start a GDB server for the target |

**Typical workflow for STM32F746:**

```bash
# Build your program
cargo build --release

# Flash and run with probe-rs (adjust chip name if needed)
probe-rs run --chip STM32F746NGHx target/thumbv7em-none-eabihf/release/myapp
```

**Selecting a specific ST‑Link when multiple are connected:**

1. List all probes and note the serial number:
   ```bash
   probe-rs list
   ```
   Example output:
   ```
   [0]: STLink V3 (VID: 0483, PID: 374e, Serial: 00123456789, StLink)
   [1]: STLink V2 (VID: 0483, PID: 3748, Serial: 00234567890, StLink)
   ```
2. Use the `--probe` option with VID:PID:Serial:
   ```bash
   probe-rs run --chip STM32F746NGHx --probe 0483:3748:00234567890 target/.../myapp
   ```
3. Or configure a default runner in `.cargo/config.toml`:
   ```toml
   [target.'cfg(all(target_arch = "arm", target_os = "none"))']
   runner = "probe-rs run --chip STM32F746NGHx --probe 0483:3748:00234567890"
   ```
   After that, `cargo run` will automatically flash and run on that probe.

### Logging and Interactive Debugging

> **Note**:
> - **defmt + RTT** is the standard logging stack for embedded Rust: add `defmt` and `defmt-rtt` to your firmware, and `probe-rs run` / `cargo embed` will decode and print the logs on the host.
> - `cargo embed` (part of `probe-rs-tools`) provides an interactive session with RTT console and an optional GDB server — configure it via `Embed.toml`.
> - For step debugging in **VS Code**, install the official `probe-rs.probe-rs-debugger` extension (uses the Debug Adapter Protocol, no GDB needed).
> - For GDB-based debugging: `probe-rs gdb --chip STM32F746NGHx` in one terminal, then `gdb-multiarch target/.../myapp -ex "target remote :1337"` in another.

---

## STM32 Development Tools (C/C++ Toolchain)

For C/C++ firmware (STM32Cube/HAL projects) or as a complement to the Rust workflow above:

```bash
sudo apt update
sudo apt install -y gcc-arm-none-eabi gdb-multiarch binutils-arm-none-eabi
sudo apt install -y openocd stlink-tools
sudo apt install -y qemu-system-arm # Optional: Cortex-M emulation
```

> **Comment**: `gcc-arm-none-eabi` is the bare-metal ARM cross-compiler; `gdb-multiarch` replaces the removed `gdb-arm-none-eabi` package and debugs any architecture. `openocd` and `stlink-tools` (`st-flash`, `st-info`) are the classic flashing/debugging stack, still needed for non-Rust projects. For a vendor GUI, STMicroelectronics provides [STM32CubeIDE and STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeide.html) as Linux downloads.

---

## Python Setup

```bash
sudo apt install -y python3 python3-venv python3-dev python3-pip pipx
pipx ensurepath
```

> **Important**: Since Ubuntu 23.04+, the system Python is *externally managed* (PEP 668) — `pip install` outside a virtual environment fails by design. Install CLI tools with `pipx` and project dependencies inside virtual environments.

### uv — Modern Python Project Manager (Recommended)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

> **Explanation**: [uv](https://docs.astral.sh/uv/) is an extremely fast package and project manager that has become the de facto standard. It replaces `pip`, `virtualenv`, `pip-tools`, `pipx`, and `pyenv` in one tool:
>
> ```bash
> uv python install 3.13        # Install a Python version (pyenv replacement)
> uv init myproject && cd myproject
> uv add requests               # Add a dependency
> uv run main.py                # Run inside the project venv automatically
> uv tool install ruff          # Install a CLI tool globally (pipx replacement)
> ```

### Linting, Formatting, Testing

```bash
uv tool install ruff   # Linter + formatter (replaces flake8, black, isort)
uv tool install mypy   # Static type checking
uv tool install pytest # Test runner (or add per-project: uv add --dev pytest)
```

> **Comment**: `ruff` is a Rust-based linter/formatter that replaces `flake8`, `black`, and `isort` with a single, dramatically faster tool (`ruff check`, `ruff format`).

### Optional: pyenv

```bash
curl -fsSL https://pyenv.run | bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
exec $SHELL
pyenv install 3.13
pyenv global 3.13
```

> **Note**: `pyenv` compiles Python versions from source and is still widely used, but `uv python install` covers the same need with prebuilt binaries. Pick one, not both.

---

## Go Installation

```bash
# Check the latest version at https://go.dev/dl/
GO_VERSION=1.26.0
curl -LO "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go${GO_VERSION}.linux-amd64.tar.gz"
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> ~/.zshrc
exec $SHELL
go version
```

> **Note**: The official tarball from go.dev is always current, while `apt install golang` lags several releases behind. Useful extras: `go install golang.org/x/tools/gopls@latest` (language server) and `go install github.com/go-delve/delve/cmd/dlv@latest` (debugger).

---

## Node.js Setup

### NVM (Recommended)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.5/install.sh | bash
exec $SHELL
nvm install --lts   # Installs the current LTS (Node.js 24)
node --version
```

> **Explanation**: Node Version Manager (NVM) installs Node.js per-user and lets you switch versions per project (`.nvmrc`). Node.js 18 and 20 are end-of-life; **Node.js 24 is the current Active LTS**.

### Package Managers via Corepack

```bash
corepack enable
corepack prepare yarn@stable --activate
corepack prepare pnpm@latest --activate
```

> **Comment**: `corepack` ships with Node.js and manages `yarn` and `pnpm` versions per project — the old Debian Yarn repository (`dl.yarnpkg.com` + `apt-key`) is deprecated and should not be used.

---

## PostgreSQL Server Setup

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib
sudo systemctl enable --now postgresql
```

> **Note**: Installs and starts PostgreSQL, a powerful relational database system. For the newest major versions, use the official [PGDG apt repository](https://www.postgresql.org/download/linux/ubuntu/).

### Optional: Create user and database

```bash
sudo -u postgres psql -c "CREATE USER myuser WITH PASSWORD 'mypassword';"
sudo -u postgres psql -c "CREATE DATABASE mydb OWNER myuser;"
```

---

## pgAdmin Installation

```bash
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" | sudo tee /etc/apt/sources.list.d/pgadmin4.list
sudo apt update
sudo apt install -y pgadmin4-desktop
```

> **Explanation**: Installs pgAdmin 4 desktop application for managing PostgreSQL databases. Use `pgadmin4-web` instead if you prefer the browser-based mode.

---

## Neovim Configuration

### Install Nerd Font

```bash
mkdir -p ~/.local/share/fonts
curl -Lo /tmp/FiraCode.zip https://github.com/ryanoasis/nerd-fonts/releases/latest/download/FiraCode.zip
unzip -o /tmp/FiraCode.zip -d ~/.local/share/fonts/FiraCode
fc-cache -fv
```

> **Comment**: Nerd Fonts provide icons and glyphs required by Powerlevel10k, Neovim statuslines, and `eza`. Other popular choices: `JetBrainsMono.zip`, `Hack.zip` — same URL pattern.

### Install Neovim

```bash
sudo snap install nvim --classic
nvim --version
```

> **Note**: The `apt` package is several major versions behind, which breaks modern plugin ecosystems. The snap (or the official tarball from [GitHub releases](https://github.com/neovim/neovim/releases)) tracks the current stable release.

### Configuration

The modern plugin ecosystem is built around the [lazy.nvim](https://github.com/folke/lazy.nvim) plugin manager (vim-plug is legacy). The recommended starting points:

- 👉 [kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim) — a single, heavily commented `init.lua` that teaches you the config as you read it (LSP, treesitter, fuzzy finding included).
- 👉 [LazyVim](https://www.lazyvim.org/) — a full pre-configured distribution if you want an IDE out of the box.
- 👉 [How to Setup Neovim (2024) by Josean](https://www.josean.com/posts/how-to-setup-neovim-2024) — step-by-step guide building a config from scratch.

```bash
# Quick start with kickstart.nvim
git clone https://github.com/nvim-lua/kickstart.nvim.git ~/.config/nvim
nvim # Plugins install automatically on first launch
```

> **Comment**: For Rust, `rust-analyzer` (installed in the Rust section) is picked up automatically by kickstart's LSP config; add `codelldb` via `:Mason` for debugging.

---

## WezTerm Installation

```bash
curl -fsSL https://apt.fury.io/wez/gpg.key | sudo gpg --yes --dearmor -o /usr/share/keyrings/wezterm-fury.gpg
echo 'deb [signed-by=/usr/share/keyrings/wezterm-fury.gpg] https://apt.fury.io/wez/ * *' | sudo tee /etc/apt/sources.list.d/wezterm.list
sudo apt update
sudo apt install -y wezterm
```

> **Note**: WezTerm is a GPU-accelerated terminal emulator with built-in multiplexing, configured in Lua. The command above uses the official apt repository from the WezTerm author. Popular alternatives: [Alacritty](https://alacritty.org/) and [Ghostty](https://ghostty.org/).

More configuration tips:
👉 [Josean's WezTerm Guide](https://www.josean.com/posts/wezterm)

---

## Installing IDEs

```bash
sudo snap install --classic code
sudo snap install rustrover --classic          # Rust IDE (free for non-commercial use)
sudo snap install pycharm-community --classic  # Python IDE
```

> **Explanation**: Installs Visual Studio Code, RustRover, and PyCharm Community via snap, which handles updates automatically (unlike pinned tarball downloads). Alternatively, manage all JetBrains IDEs with [JetBrains Toolbox](https://www.jetbrains.com/toolbox-app/) — download the latest tarball from the site, extract, and run `jetbrains-toolbox` once; it installs itself.

### Recommended VS Code Extensions

```bash
code --install-extension rust-lang.rust-analyzer   # Rust language support
code --install-extension vadimcn.vscode-lldb       # CodeLLDB debugger
code --install-extension probe-rs.probe-rs-debugger # Embedded debugging via probe-rs
code --install-extension ms-python.python
code --install-extension charliermarsh.ruff
code --install-extension golang.go
```

---

## API Testing Tools

```bash
sudo snap install postman
sudo apt install -y httpie
cargo binstall -y xh # Optional: fast HTTPie clone in Rust
```

> **Comment**: Installs Postman (GUI for API testing) and HTTPie (CLI for HTTP requests). Lightweight alternatives worth knowing: `xh` (Rust), [Bruno](https://www.usebruno.com/) (offline, Git-friendly Postman alternative), and [Hoppscotch](https://hoppscotch.io/) (web-based).

---

## Wireshark Installation

```bash
sudo apt install -y wireshark
sudo dpkg-reconfigure wireshark-common # Answer "Yes" to allow non-root capture
sudo usermod -aG wireshark $USER       # Re-login to apply
```

> **Note**: Wireshark is a network protocol analyzer. The group configuration lets you capture packets without running the GUI as root.

---

## Docker and Docker Compose

```bash
# Add Docker's official GPG key and repository
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

# Install Docker Engine, CLI, Buildx, and Compose v2
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable --now docker
sudo usermod -aG docker $USER # Re-login to apply

# Verify
docker run --rm hello-world
docker compose version
```

> **Comment**: Uses Docker's official repository instead of the outdated Ubuntu `docker.io` package. Compose v2 is now a plugin invoked as `docker compose` (with a space) — the standalone `docker-compose` binary is deprecated. Adding your user to the `docker` group allows running commands without `sudo` (note: this is root-equivalent access; use rootless Docker or Podman if that concerns you).

---

## KVM + QEMU + libvirt Installation (Recommended)

```bash
# Verify hardware virtualization support first
sudo apt install -y cpu-checker
kvm-ok

# Install core virtualization packages
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# Enable and start libvirt
sudo systemctl enable --now libvirtd

# Add your user to required groups (re-login to apply)
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER

# (Optional) install additional tools
sudo apt install -y virtinst virt-viewer
```

> **Explanation**: KVM (Kernel‑based Virtual Machine) together with QEMU and libvirt provides near‑native performance for virtual machines. This setup is ideal for kernel development, malware analysis in isolated snapshots, infrastructure testing, and running full operating systems inside Linux. It is the recommended virtualization stack on Linux — prefer it over VirtualBox unless you need cross-platform VM portability.

### Verify installation

```bash
virsh list --all
```
If no errors are shown, KVM is working correctly.

### Launch GUI manager

```bash
virt-manager
```

### Creating a VM (quick flow)

1. Open **virt-manager**
2. Click **Create new VM**
3. Choose ISO image
4. Allocate RAM / CPU cores
5. Create a disk image
6. Start the VM and install the guest OS

### Networking (bridge example)

To give your VMs direct access to the physical network, create a bridge:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

Example configuration (replace `eth0` with your interface name from `ip link`):

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
  bridges:
    br0:
      interfaces: [eth0]
      dhcp4: yes
```

Apply the changes:

```bash
sudo netplan apply
```

Now you can attach VMs to `br0` instead of the default NAT network.

### Optional: Enable nested virtualization

Nested virtualization allows you to run VMs inside a VM (useful for labs and testing hypervisors).

**For Intel CPUs:**
```bash
echo "options kvm-intel nested=Y" | sudo tee /etc/modprobe.d/kvm-intel.conf
```

**For AMD CPUs:**
```bash
echo "options kvm-amd nested=1" | sudo tee /etc/modprobe.d/kvm-amd.conf
```

Reload the KVM modules:

```bash
sudo modprobe -r kvm_intel kvm_amd 2>/dev/null
sudo modprobe kvm_intel || sudo modprobe kvm_amd
```

Check if nested virtualization is active:

```bash
cat /sys/module/kvm_intel/parameters/nested 2>/dev/null || cat /sys/module/kvm_amd/parameters/nested
```
It should print `1` or `Y`.

### Notes for Developers / Researchers

- KVM delivers **near‑native performance** (much faster than QEMU software emulation).
- Supports **GPU passthrough** (advanced, requires dedicated GPU and IOMMU).
- Perfect for:
  - Malware analysis (isolated snapshots)
  - Kernel development (easy crash recovery)
  - Testing multi‑node clusters on a single machine
  - Running Windows or other Linux distributions side‑by-side

---

## QEMU Installation

```bash
sudo apt update
sudo apt install -y qemu-system-x86 qemu-system-arm qemu-user-static
```

> **Explanation**: Installs QEMU emulators for x86 and ARM systems. `qemu-system-arm` also emulates Cortex-M boards (useful for testing embedded firmware without hardware), and `qemu-user-static` enables running/building foreign-architecture binaries and Docker images (e.g., `arm64` on `x86_64`). If you installed the KVM stack above, `qemu-system-x86` is already present.

---

## VirtualBox Installation

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt update
sudo apt install -y virtualbox-7.2

# Extension pack — version must match the installed VirtualBox version
VBOX_VERSION=$(VBoxManage --version | cut -dr -f1)
wget "https://download.virtualbox.org/virtualbox/${VBOX_VERSION}/Oracle_VirtualBox_Extension_Pack-${VBOX_VERSION}.vbox-extpack"
sudo VBoxManage extpack install "Oracle_VirtualBox_Extension_Pack-${VBOX_VERSION}.vbox-extpack"
sudo usermod -aG vboxusers $USER # Re-login to apply
```

> **Explanation**: Installs VirtualBox 7.2 (current stable branch) from Oracle's repository, with the extension pack version derived automatically from the installed build. Note: the extension pack is under Oracle's Personal Use license. Prefer KVM (above) for Linux-only workflows; VirtualBox remains convenient for portable, cross-platform VM setups.

---

## Rust GUI/Web Frameworks: Leptos and Tauri

### Leptos (Web)

```bash
rustup target add wasm32-unknown-unknown
cargo binstall -y cargo-leptos trunk leptosfmt
```

> **Note**: [Leptos](https://leptos.dev/) is a Rust framework for building reactive web applications. `cargo-leptos` builds full-stack (SSR) projects, `trunk` serves client-side (CSR) WASM apps, and `leptosfmt` formats the `view!` macros. The `wasm32-unknown-unknown` target is required for all Rust → WebAssembly work.

### Tauri (Desktop/Mobile)

```bash
# System dependencies (Ubuntu 24.04+)
sudo apt install -y libwebkit2gtk-4.1-dev build-essential curl wget file \
  libxdo-dev libssl-dev libayatana-appindicator3-dev librsvg2-dev

# Tauri CLI (choose one)
cargo binstall -y tauri-cli create-tauri-app
# or per-project via npm: npm install -D @tauri-apps/cli

# Scaffold a new app
cargo create-tauri-app
```

> **Explanation**: [Tauri 2](https://v2.tauri.app/) builds lightweight desktop (and mobile) applications with a Rust backend and web frontend. The WebKitGTK system libraries are mandatory on Linux — install them before the first build.

---

## SSH Server Configuration

```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

> **Note**: Installs and enables the SSH server for remote access. Recommended hardening in `/etc/ssh/sshd_config`: `PasswordAuthentication no` (after adding your public key to `~/.ssh/authorized_keys`) and `PermitRootLogin no`, then `sudo systemctl restart ssh`.

---

## nftables Firewall Configuration

> **Warning**: For most desktop users, the preinstalled **UFW** (`sudo ufw enable`) is sufficient and simpler. Configure nftables directly only if you want full control. Do **not** purge the `iptables` package: Docker and libvirt program their rules through the `iptables-nft` compatibility layer, and removing it breaks container networking.

```bash
# Disable UFW so it does not conflict with your own ruleset
sudo ufw disable

sudo apt update
sudo apt install -y nftables
sudo systemctl enable --now nftables
sudo nano /etc/nftables.conf
```

### Example config

```nftables
#!/usr/sbin/nft -f
flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        iifname "lo" accept
        ct state established,related accept
        tcp dport 22 accept
        icmp type echo-request accept
        icmpv6 type { echo-request, nd-neighbor-solicit, nd-neighbor-advert, nd-router-advert } accept
    }
    chain forward {
        type filter hook forward priority 0; policy drop;
    }
    chain output {
        type filter hook output priority 0; policy accept;
    }
}
```

```bash
sudo nft -c -f /etc/nftables.conf # Check syntax first
sudo nft -f /etc/nftables.conf
sudo systemctl restart nftables
```

> **Explanation**: A modern default-deny inbound firewall: allows SSH, ICMP ping, and established connections, while permitting all outbound traffic (a drop-by-default output chain silently breaks package managers, NTP, and updates — only lock down output on hardened servers). The `-c` flag validates the config before applying, so a typo cannot lock you out.

---

## System Maintenance and Automatic Updates

```bash
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

> **Explanation**: Enables automatic installation of security updates.

### Periodic Cleanup

```bash
sudo apt autoremove -y            # Remove packages no longer needed
sudo apt clean                    # Clear the local package cache
sudo journalctl --vacuum-time=7d  # Trim systemd logs older than 7 days
sudo snap set system refresh.retain=2 # Keep only 2 revisions of each snap
```

> **Comment**: Run these occasionally to free disk space. Keeping 7 days of journal logs (rather than 1) preserves enough history to debug recent issues.

---

Happy coding! 🚀
