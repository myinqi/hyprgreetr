# HyprGreetr

A modern, configurable system information tool written in Rust, similar to neofetch/fastfetch, with support for PNG images and MOTD (Message of the Day). Originally forked from termgreet and enhanced with btrfs-aware disk reporting and automatic asset management.

## Features

- **Comprehensive System Info**: CPU, GPU, Memory, OS, Window Manager, Shell, Terminal, and more
- **Modern Image Support**: PNG images with Kitty Graphics Protocol for pixel-perfect rendering
- **TOML Configuration**: Fully configurable via `~/.config/hyprgreetr/config.toml`
- **Automatic Asset Management**: PNG files from assets directory are automatically copied to config on first run
- **Tilde Path Expansion**: Support for `~` in config paths (e.g., `~/config/hyprgreetr/pngs/logo.png`)
- **MOTD Support**: Display configurable welcome messages
- **Enhanced Disk Reporting**: btrfs-aware disk usage with consolidated subvolume display
- **Clean Layout**: Fastfetch-style output with customizable colors and borders
- **Modular Design**: Enable/disable individual modules as needed
- **Version Display**: Optional version information for Shell, Terminal, DE, and WM
- **GPU Driver Detection**: Shows driver type (open source/proprietary) with version
- **Enhanced Resolution**: Display resolution with refresh rate (e.g., 3440x1440 @ 165Hz)
- **Font Detection**: Accurate terminal font detection with size information
- **Universal Terminal Support**: Works in Kitty, Ghostty, Alacritty, GNOME Terminal, VSCode, and more
- **Configurable Borders**: Optional borders around the information area

## Installation

### Prerequisites

HyprGreet requires Rust to be installed on your system. If you don't have Rust installed:

```bash
# Install Rust via rustup (recommended)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
```

Alternatively, install Rust through your system package manager:
- **Arch Linux**: `sudo pacman -S rust cargo`
- **Ubuntu/Debian**: `sudo apt install rustc cargo`
- **Fedora**: `sudo dnf install rust cargo`
- **macOS**: `brew install rust`

### Building from Source

```bash
# Clone the repository
git clone https://github.com/myinqi/hyprgreetr.git
cd hyprgreetr

# Build and install
cargo build --release
cargo install --path .
```

## Usage

```bash
# Basic usage
hyprgreetr

# Use custom configuration file
hyprgreetr --config /path/to/config.toml

# Show only MOTD
hyprgreetr --motd

# Disable images
hyprgreetr --no-image

# Show help
hyprgreetr --help
```

## Autostart on Terminal Launch

To automatically run HyprGreet when opening a new terminal (similar to fastfetch), add one of these options to your shell configuration:

### Basic Autostart

```bash
# Add to ~/.zshrc (for zsh) or ~/.bashrc (for bash)
if [[ -o interactive ]]; then
    hyprgreetr
fi
```

### Autostart with Custom Configuration

```bash
# Add to ~/.zshrc (for zsh) or ~/.bashrc (for bash)
if [[ -o interactive ]]; then
    hyprgreetr --config ~/.config/hyprgreetr/config.toml
fi
```

After adding this to your shell configuration, restart your terminal or run `source ~/.zshrc` (or `source ~/.bashrc`) to apply the changes.

## Configuration

HyprGreet automatically creates a default configuration file at `~/.config/hyprgreetr/config.toml` on first run.

### Automatic Asset Management

On first run, HyprGreet automatically:
- Creates the `~/.config/hyprgreetr/pngs/` directory
- Copies all PNG files from the assets directory to your config directory
- This includes logos for various distributions (Arch, CachyOS, openSUSE Tumbleweed, Hyprland, etc.)

### Tilde Path Expansion

HyprGreet supports tilde (`~`) expansion in configuration paths:
```toml
# Both formats work:
image_path = "/home/username/.config/hyprgreetr/pngs/logo.png"  # Absolute path
image_path = "~/.config/hyprgreetr/pngs/logo.png"              # Tilde path (recommended)

motd_file = "~/.config/hyprgreetr/motd.toml"                   # Also works for MOTD
```

### Main Configuration (`~/.config/hyprgreetr/config.toml`)

```toml
[general]
title = "System Information"

# Configurable separator with spacing and alignment
[general.separator]
symbol = "<>"
space_before = 2
space_after = 6
align_separator = true  # Align all separators in a column

# Separate colors for different elements
[general.colors]
title = "bright_cyan"
module = "bright_blue"     # Module names (OS, CPU, etc.)
info = "bright_white"      # System information values
separator = "bright_blue"

[display]
show_image = true
image_path = "/path/to/your/image.png"
prefer_kitty_graphics = true  # Use Kitty Graphics Protocol when available
padding = 2

# Fine-tune image scaling for perfect aspect ratio
[display.image_size]
width = 35
height = 15
cell_width = 17   # Pixels per terminal character (width)
cell_height = 24  # Pixels per terminal character (height)

[modules]
show_versions = true  # Show version info for Shell, Terminal, DE, WM
show_motd = true     # Enable/disable MOTD display

# Optional border configuration
[display.border]
show_border = true
border_top = "┌─────────────────────────────────────────────────────────────────────────────┐"
border_bottom = "└─────────────────────────────────────────────────────────────────────────────┘"
border_color = "bright_green"
os = true
kernel = true
uptime = true
os_age = true        # Days since OS installation
packages = true
shell = true
resolution = true    # Now includes refresh rate
de = true
wm = true
theme = false
icons = false
terminal = true
font = true          # Terminal font with size
user = true          # Current username
hostname = true      # Computer name
cpu = true
gpu = true
gpu_driver = true    # GPU driver with open source/proprietary label
memory = true
disk = true
battery = false
locale = false

# Path to MOTD configuration file
motd_file = "~/.config/termgreet/motd.toml"
```

### MOTD Configuration (`~/.config/termgreet/motd.toml`)

```toml
enabled = true
random = true
color = "bright_green"

messages = [
    "Welcome to your system!",
    "Have a great day!",
    "Ready to code!",
    "System is running smoothly!",
    "Time to be productive!"
]
```

## Available Modules

### System Information
- **user_at_host**: Current username and hostname (e.g., `khrom@workstation`)
- **os**: Operating system information
- **kernel**: Kernel version
- **linux**: Linux distribution information
- **uptime**: System uptime
- **os_age**: Days since OS installation
- **packages**: Number of installed packages (pacman, apt, dnf, etc.)
- **flatpak_packages**: Number of Flatpak packages
- **packages_combined**: Combined package count from all package managers
- **locale**: System locale

### Environment
- **shell**: Shell with version (e.g., `zsh 5.9`)
- **terminal**: Terminal emulator with version (e.g., `ghostty 1.0.0`)
- **terminal_shell_combined**: Combined terminal and shell information
- **font**: Terminal font with size (e.g., `JetBrainsMono Nerd Font (13pt)`)
- **user**: Current username
- **hostname**: Computer name
- **de**: Desktop environment with version
- **wm**: Window manager with version
- **theme**: System theme (if available)
- **icons**: Icon theme (if available)
- **resolution**: Display resolution with refresh rate (e.g., `3440x1440 @ 165Hz`)

### Hardware
- **cpu**: CPU information with core count
- **cpu_temp**: CPU temperature
- **gpu**: GPU information (cleaned, Fastfetch-style)
- **gpu_temp**: GPU temperature
- **temp_combined**: Combined CPU and GPU temperatures
- **gpu_driver**: GPU driver with type and version (e.g., `NVIDIA (proprietary) 575.64.05`)
- **memory**: Memory usage
- **battery**: Battery status (if available)

### Storage & Network
- **disk**: Disk usage (traditional display)
- **dysk**: Enhanced disk usage display with multiple drives
- **network**: Network interface information
- **public_ip**: Public IP address

## 🎨 Color Configuration

HyprGreetr supports flexible color configuration with multiple formats:

### Hex Colors (Recommended)
```toml
[general.colors]
title = "#00FFFF"     # Cyan title
module = "#89dceb"    # Light blue for module names
info = "#cdd6f4"      # Light gray for information
separator = "#cba6f7" # Purple for separators
```

**Supported Hex Formats:**
- 6-digit: `#RRGGBB` (e.g., `#FF0000` for red)
- 3-digit: `#RGB` (e.g., `#F00` for red, automatically expanded to `#FF0000`)

### Named Colors (Legacy Support)

**Standard Colors:**
- `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`

**Bright Colors:**
- `bright_black`, `bright_red`, `bright_green`, `bright_yellow`, `bright_blue`, `bright_magenta`, `bright_cyan`, `bright_white`

### Color Usage
- **title**: Color for the main title (e.g., "System Information")
- **module**: Color for module names (e.g., "OS", "Kernel", "CPU")
- **info**: Color for the information values
- **separator**: Color for the separator between module and value

## Image Rendering

HyprGreetr supports multiple image rendering methods:

**Kitty Graphics Protocol**
- Pixel-perfect rendering in Kitty, Ghostty, iTerm2, WezTerm
- Automatic terminal detection with graceful fallback
- True side-by-side layout with ANSI cursor positioning

**Universal Compatibility**
- Block graphics fallback for unsupported terminals (VSCode, etc.)
- Consistent scaling across all terminal types
- Info-only mode if image loading fails

**Configuration Tips**
1. Set `prefer_kitty_graphics = true` for modern terminals
2. Adjust `cell_width` and `cell_height` for perfect aspect ratio
3. Use `width` and `height` to control overall image size

## Advanced Features

### GPU Driver Detection
Automatically detects and categorizes GPU drivers:
- **Proprietary**: `NVIDIA (proprietary) 575.64.05`
- **Open Source**: `AMDGPU (open source)`, `Intel i915 (open source)`, `Nouveau (open source)`

### Font Detection
Accurate font detection across terminals:
- **Kitty**: Reads from `kitty.conf`
- **Ghostty**: Reads from `ghostty/config`
- **Alacritty**: Supports both YAML and TOML configs
- **GNOME Terminal**: Uses gsettings and dconf
- **VSCode**: Reads from settings.json

### Resolution with Refresh Rate
Enhanced resolution detection:
- **X11**: Parses `xrandr` output for active modes
- **Wayland**: Supports `wlr-randr` and `swaymsg`
- **Format**: `3440x1440 @ 165Hz`

### Version Information
Optional version display for:
- **Shell**: `bash 5.2.21`, `zsh 5.9`, `fish 3.6.1`
- **Terminal**: `kitty 0.32.2`, `alacritty 0.13.2`
- **DE/WM**: Detected when available

## Window Manager Detection

Reliable detection for various environments:

**Wayland Compositors:**
- KWin (Wayland), GNOME Shell, Sway, Hyprland
- River, Wayfire, Weston, Labwc

**X11 Window Managers:**
- KWin (X11), GNOME Shell, Xfwm4, Openbox
- i3, bspwm, dwm, awesome, xmonad
- Fluxbox, Blackbox, IceWM, JWM, herbstluftwm

## Example Output

![HyprGreetr Example Output](https://github.com/myinqi/hyprgreetr/blob/main/screenshots/example1.png)

*HyprGreetr displaying system information with a custom logo and color scheme*

## Development

```bash
# Run development version
cargo run

# Run tests
cargo test

# Format code
cargo fmt

# Linting
cargo clippy

# Build release
cargo build --release
```

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

## Acknowledgments

HyprGreet was inspired by and builds upon the excellent work of several projects:

- **[TermGreet](https://github.com/myinqi/termgreet)** - The original project that this fork is based on
- **[Neofetch](https://github.com/dylanaraps/neofetch)** and **[Fastfetch](https://github.com/fastfetch-cli/fastfetch)** for pioneering the system information display concept and providing the foundation for modern fetch tools
- **[dysk](https://github.com/Canop/dysk)** by [Canop](https://github.com/Canop) for the inspiration behind our enhanced disk usage display module

### Enhanced Features in HyprGreet

This fork adds several enhancements over the original TermGreet:
- **btrfs-aware disk reporting** - Consolidates btrfs subvolumes for cleaner output
- **Automatic asset management** - PNG files are automatically copied to config directory on first run
- **Tilde path expansion** - Support for `~` in configuration file paths
- **Enhanced error handling** - Better handling of missing assets and configuration files

We're grateful to these projects and their maintainers for their contributions to the open source community.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
