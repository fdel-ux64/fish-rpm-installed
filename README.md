# 🐟 fish-rpm-installed

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Fish Shell](https://img.shields.io/badge/fish-v3.0+-blue.svg)](https://fishshell.com)

A Fish shell function to **list and analyze RPM packages** by installation date, with caching for fast repeated queries and formatted output.

> 🚀 **Ever wondered what packages you installed last week?** Or need to audit recent system changes? This tool makes it effortless with a clean, date-grouped display.

---

## ✨ Features

- **⚡ Fast** - Cached queries return results instantly after the first run
- **📅 Date-Grouped** - Packages are grouped by installation date with per-group counts
- **📊 Analytics** - Built-in aggregation to see installation patterns (per-day/per-week)
- **🎨 Formatted Output** - Date headers with icons, package counts, and footer summary
- **🔀 Cache Control** - Enable, disable, or inspect the cache without restarting your shell
- **🎯 Simple** - No additional dependencies beyond standard RPM system tools
- **🔍 Smart** - Auto-detects RPM systems and uses consistent locale parsing

Perfect for system administrators, power users, and anyone managing RPM-based distributions like **Fedora**, **RHEL**, **CentOS**, **Rocky Linux**, **AlmaLinux**, and **openSUSE**.

---

## 📦 Installation

### Using [Fisher](https://github.com/jorgebucaran/fisher) (Recommended)

```fish
fisher install fdel-ux64/fish-rpm-installed
```

### Manual Installation

```fish
# Clone the repository
git clone https://github.com/fdel-ux64/fish-rpm-installed.git

# Copy to your Fish functions directory
cp fish-rpm-installed/functions/rpm_installed.fish ~/.config/fish/functions/
```

> **Note:** Tab completions are not yet available. This will be added in a future release.

---

## 🎯 Quick Start

```fish
# What did I install today?
rpm_installed today

# Show packages from last week
rpm_installed last-week

# Count installations this month
rpm_installed count this-month

# Custom date range
rpm_installed since 2025-01-01 until 2025-01-10

# See installation patterns
rpm_installed per-day
```

---

## 🎨 Example Output

```
    📦 Installed packages — last-week

 📆 Wed 2026-03-18  (5 packages)
    onnx-libs-1.17.0-12.fc43.x86_64
    zlib-ng-2.3.3-2.fc43.x86_64
    zlib-ng-compat-2.3.3-2.fc43.x86_64
    zlib-ng-compat-devel-2.3.3-2.fc43.x86_64
    perl-Module-CoreList-5.20260308-1.fc43.noarch
 📆 Thu 2026-03-19  (3 packages)
    firefox-148.0.2-2.fc43.x86_64
    firefox-langpacks-148.0.2-2.fc43.x86_64
    libtasn1-4.21.0-1.fc43.x86_64

 ────────────────────────────────────
 🔢 Total: 8 packages
```

When the result reaches or exceeds 75 packages, the filter criteria is repeated in the footer so context is preserved after scrolling:

```
 ────────────────────────────────────
 🔢 Total: 83 packages
 ↑  Showing 83 packages installed: last-month
```

The threshold is controlled by the global variable `__rpm_summary_threshold` (default: `75`).

---

## 📖 Usage

### Basic Syntax

```fish
rpm_installed [OPTION]
rpm_installed count [OPTION]
rpm_installed since DATE [until DATE]
rpm_installed --refresh
rpm_installed --cache on|off
rpm_installed --cache
rpm_installed --help
```

### Time-Based Shortcuts

| Shortcut | Alias | Description |
|----------|-------|-------------|
| `today` | `td` | Packages installed today |
| `yesterday` | `yd` | Packages installed yesterday |
| `last-week` | `lw` | Last 7 days |
| `this-month` | `tm` | Current calendar month |
| `last-month` | `lm` | Previous calendar month |

### Analytics Options

| Option | Description |
|--------|-------------|
| `per-day` | Count packages grouped by day |
| `per-week` | Count packages grouped by week |

### Special Flags

| Flag | Description |
|------|-------------|
| `--refresh` | Clear and rebuild the cache on next call (caching stays enabled) |
| `--cache on` | Enable caching (default) |
| `--cache off` | Disable caching — RPM is queried live on every call |
| `--cache` | Show current cache status (enabled/disabled, populated or empty) |
| `--help` | Show usage information |

---

## 💡 Examples

### Simple Queries with Formatted Output

```fish
# Using shortcuts - shows grouped output with date headers and count
rpm_installed td                    # Today's installations
rpm_installed yd                    # Yesterday's installations
rpm_installed lw                    # Last week

# With counts (no formatting, just statistics)
rpm_installed count today           # How many packages today?
rpm_installed count last-month      # Monthly installation count
```

### Date Range Queries

```fish
# Specific date range with formatted output
rpm_installed since 2025-12-01 until 2025-12-15

# Open-ended (everything since a date)
rpm_installed since 2025-01-01

# until-only (everything up to a date)
rpm_installed until 2025-06-01

# A single specific day (until is inclusive of the specified date)
rpm_installed since 2025-12-25 until 2025-12-25
```

### Analytics (Statistics Only)

```fish
# Daily installation pattern
rpm_installed per-day

# Output example:
# 2025-01-15  5
# 2025-01-16  12
# 2025-01-17  3

# Weekly trends
rpm_installed per-week

# Output example:
# 2025-W02  18
# 2025-W03  25
```

### Cache Management

```fish
# Check current cache status
rpm_installed --cache
# Cache: enabled (populated)

# Refresh the cache after installing packages mid-session
rpm_installed --refresh

# Disable caching entirely — every call queries RPM live
rpm_installed --cache off

# Re-enable caching
rpm_installed --cache on
```

---

## 🏗️ How It Works

1. **First Run**: Queries all installed RPM packages with installation timestamps using `rpm -qa`
2. **Caching**: Stores results in a session-scoped variable (`__rpm_instlist_cache`) for fast subsequent queries
3. **Locale Handling**: Forces US English locale (`LC_ALL=en_US.UTF-8`) for consistent date parsing across systems
4. **Smart Filtering**: Parses the cache efficiently for date-based queries
5. **Grouped Display**: Packages are grouped by installation date — date headers with per-group counts make long lists easy to scan

### ⚠️ Cache Behavior

The cache is built once per shell session and held in memory. If you install or remove packages during a session, results will not reflect those changes until you run `rpm_installed --refresh`.

`--refresh` clears the cache but keeps caching enabled — the cache rebuilds on the next call.
`--cache off` disables caching entirely so every call queries RPM live. Use this when you need always-current results without manual refreshing.

---

## 🎨 Output Formatting

The function provides two types of output:

### **Formatted Display** (default for package listings)
- 📦 Section header with filter label
- 📆 Date group headers with per-group package counts
- Clean package name list under each date group
- 🔢 Total package count footer with separator line
- ↑ Filter reminder when total exceeds threshold (default: 100)

### **Statistics Mode** (count/per-day/per-week)
- Plain text output for easy parsing
- No formatting, just data
- Perfect for scripting and analysis

---

## 🔧 Requirements

- **Fish Shell** v3.0 or later
- **RPM-based system** (Fedora, RHEL, CentOS, Rocky Linux, AlmaLinux, openSUSE, etc.)
- Standard system tools: `rpm`, `awk`, `date`

---

## 📁 Project Structure

```
fish-rpm-installed/
├── functions/
│   └── rpm_installed.fish    # Main function
├── LICENSE                    # MIT License
└── README.md                 # This file
```

---

## 🆕 Changelog

**v2.5 – Grouped Output & Cache Control**
- ✨ Packages now grouped by installation date with 📆 date headers and per-group counts
- ✨ Redundant timestamp removed from each package line — cleaner, easier to scan
- ✨ Added `--cache on/off` flag to enable or disable caching at runtime
- ✨ Added `--cache` (no argument) to inspect current cache status
- ✨ `--refresh` now explicitly distinct from `--cache off`: clears the cache but keeps caching enabled
- ⚙️ Summary threshold raised from 25 to 100 (`__rpm_summary_threshold`)
- 📝 Updated footer format: `🔢 Total: N packages`

**v2.1.1 – Footer Summary for Long Lists**
- ✨ Added filter criteria repeat in footer when package count exceeds threshold
- ⚙️ Threshold controlled by `__rpm_summary_threshold` variable (default: 25)

**v2.1.0 – Bug Fixes**
- 🐛 Fixed `until`-only queries being silently ignored (only worked when paired with `since`)
- 🐛 Fixed `until DATE` off-by-one: specified date is now inclusive
- 🐛 Fixed `last-week` and `this-month` having no upper time bound (future-dated packages could appear)
- 🐛 Fixed `count per-day` and `count per-week` silently ignoring the `count` prefix
- 🐛 Fixed alias shortcuts (e.g. `count td`) not resolving after `count` mode shift
- 🐛 Fixed cache variable renamed to `__rpm_instlist_cache` to prevent collision when multiple distro variants are loaded in the same session
- 🐛 Fixed inner helper functions being redefined in global scope on every call
- 🐛 Fixed missing bounds check when `since` or `until` is used without a following date argument

**v2.0.2 – Case-Insensitive Arguments & Consistency**
- ✨ Added case-insensitive argument handling (TODAY, today, Today all work)
- 🔧 Normalized all command arguments and keywords (count, since, until)
- 📝 Enhanced argument parsing for better user experience

**v2.0.1 – Improved Error Handling and Help Output**
- ✨ Show full help on invalid arguments
- ✨ Show full help when date parsing fails
- ✨ More self-explanatory CLI behavior

**v2.0 – Enhanced Visual Output**
- ✨ Added formatted headers with package icon (📦)
- ✨ Added total package count footer with counter icon (🔢)
- ✨ Clean underline separators for better readability
- ✨ Applied formatting to all time period options
- ✨ Maintained statistics mode for data analysis
- ✨ Improved distro detection with clear error messages

**v1.0.0 – Initial Release**
- 🚀 Initial release of `rpm-installed` to list installed RPM packages by install date
- 📦 Supports filtering by today, yesterday, last week, this month, last month
- ⚙️ Includes count/stats mode and alias shortcuts (td, yd, lw, tm, lm)

---

## 🔗 Related Projects

- [bash-rpm-installed](https://github.com/fdel-ux64/bash-rpm-installed) - Bash shell version of this tool — keep versions in sync, both share the same fix history
- [fish-config](https://github.com/fdel-ux64/fish-config) - Full Fish configuration with multiple utilities

---

## ⚠️ Known Limitations

- Cache is session-scoped and must be manually refreshed with `--refresh` after mid-session package changes
- `date -d` requires GNU date — standard on Linux, not available on macOS or BSD without `coreutils`
- Cache invalidation strategy may differ between distros in a future release

---

## 🤝 Contributing

Contributions are welcome! Feel free to:

- Report bugs
- Suggest new features
- Submit pull requests
- Improve documentation

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

Created for the Fish shell community and RPM-based distribution users who want better visibility into their system's package history.

---

## ⭐ Show Your Support

If you find this useful, please consider:
- ⭐ Starring this repository
- 🐛 Reporting issues you encounter
- 📢 Sharing it with others who might benefit

---

**Made with 🐟 and ❤️ for the Fish shell community**
