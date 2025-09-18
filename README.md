# ssm - Simple SSH Manager

A tiny, dependency‑light Bash utility to maintain a small list of SSH targets (group / name / host / user) and connect to them quickly. Stores data in a simple tab‑separated text file (~/.ssm/hosts.db) you can edit with any editor or manage through the built‑in commands.

## Features
- Add, edit, delete SSH host entries (interactive prompts)
- Grouping + optional group filter when listing
- Colored, aligned list output with live ping status indicator (● green = reachable, ● red = no reply)
- Quick single‑letter command aliases (a, e, d, c, l)
- Zero external runtime dependencies beyond standard macOS / typical Unix tools (bash, awk, sed, ping, ssh, tput)

## Installation
1. Copy the script somewhere on your PATH, e.g.
   cp ssm /usr/local/bin/ssm   # may require sudo
2. Make it executable:
   chmod +x /usr/local/bin/ssm
3. (Optional) Git track it in a dotfiles repo or put it in ~/bin and add that to PATH in your shell profile.

Verify:
  ssm --help

## Data File
Location: ~/.ssm/hosts.db
Format (tab‑separated, first non‑comment line added automatically):
  # group<TAB>name<TAB>host<TAB>user
  prod	api1	api1.example.com	deploy
  prod	api2	10.1.2.34	ubuntu

You can hand‑edit this file; lines beginning with # are ignored.

## Usage
List all hosts:
  ssm list
List only a specific group:
  ssm list prod
Add a host (prompts for each field):
  ssm add
Edit a host by its name field:
  ssm edit api1
Delete a host (asks for confirmation):
  ssm delete api2
Connect via SSH:
  ssm connect api1

Short forms:
  ssm l | a | e NAME | d NAME | c NAME

Examples:
  ssm a            # add new
  ssm l prod       # list only group "prod"
  ssm c api1       # ssh deploy@api1.example.com (after reachability check)

## How Ping Indicator Works
For each host in list mode a single ping (ping -c 1 -W 1) is attempted. Success prints a green dot, failure a red dot. Failures do not block connecting manually.

## Editing Logic
- Edit replaces the old line (matched by name) with the new one.
- Delete requires confirmation.
- Add refuses duplicates by name.

## Notes / Caveats
- No support (yet) for custom SSH ports or options; use your ~/.ssh/config for advanced settings (match by host alias) or extend the script.
- No password / key management is performed.
- Name must be unique (per entire file, not just group).
- A failed ping does not prevent SSH; some servers disable ICMP.
- Uses -tt when invoking ssh to force TTY allocation (adjust if you prefer not to).

## Extending
Ideas you can add:
- Port column or parsing host:port
- Automatic SSH config generation
- Fuzzy finder integration (fzf) for selection when no name provided
- Shell completion script for names/groups
- Export / import (JSON / YAML)
- Optional last-connected timestamp

## Troubleshooting
Ping always red: Host might block ICMP, or you are on a VPN / different network.
Weird colors: Terminal might not support tput colors; script falls back to plain text.
Permission denied on install: Re-run copy with sudo or choose a directory you own (e.g. ~/bin) and add it to PATH.

## Uninstall
  rm -f /usr/local/bin/ssm
  rm -rf ~/.ssm   # (Removes your saved hosts!)

## License
Choose a license (e.g. MIT) and state it here. (Placeholder.)

## Disclaimer
Provided as-is with no warranty. Keep backups of your hosts file if it matters.
