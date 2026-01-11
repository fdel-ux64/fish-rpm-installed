# fish-rpm-installed

A Fish shell function to list installed RPM packages by installation date,
with caching for faster repeated queries.

This function supports RPM-based distributions such as **Fedora, RHEL, and CentOS**.
It ensures consistent date parsing by using the **US English locale**.

---

## Features

- Distro check for RPM-based systems 
- List installed RPM packages by date range
- Common time-based shortcuts (today, last-week, etc.)
- Aggregated statistics (per-day / per-week)
- Cached results for fast repeated queries
- Simple, dependency-free Fish implementation

---

## Installation

Using [Fisher](https://github.com/jorgebucaran/fisher):

```fish
fisher install fdel-ux64/fish-rpm-installed
```

## Project Structure

```
├── completions
│   └── rpm_installed.fish
├── functions
│   └── rpm_installed.fish
├── LICENSE
└── README.md
```

## Usage

- rpm_installed [OPTION]
- rpm_installed count [OPTION]
- rpm_installed since DATE [until DATE]
- rpm_installed --refresh
- rpm_installed --help


## Options

| Option       | Description                              |
| ------------ | ---------------------------------------- |
| `today`      | Packages installed today                 |
| `yesterday`  | Packages installed yesterday             |
| `last-week`  | Packages installed in the last 7 days    |
| `this-month` | Packages installed this calendar month   |
| `last-month` | Packages installed in the previous month |
| `per-day`    | Count packages per day                   |
| `per-week`   | Count packages per week                  |


## Aliases

| Alias | Expands to |
| ----- | ---------- |
| `td`  | today      |
| `yd`  | yesterday  |
| `lw`  | last-week  |
| `tm`  | this-month |
| `lm`  | last-month |


## Examples

```fish
rpm_installed td
rpm_installed last-week
rpm_installed count this-month
rpm_installed since 2025-12-16 until 2025-12-22
rpm_installed --refresh
rpm_installed --help
```

## Notes

- This tool only works on RPM-based systems.
- The cache can be rebuilt at any time using `--refresh`.
- Designed to be fast, simple, and predictable.

  
## Related Projects

- [bash-rpm-installed](https://github.com/fdel-ux64/bash-rpm-installed) - bash shell version


## License

MIT

