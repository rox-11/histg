# histg

> Search your shell history with readable, highlighted output.

`histg` is a small command-line tool that searches your shell history and prints the results in a clean format: numbered, with human-readable dates, matches highlighted, and duplicates removed.

```
  1  2026-08-03 14:20  docker run -p 8081:80 vulnerables/web-dvwa
  2  2026-08-03 15:30  docker stop ba50d73e573a
  3  2026-08-03 15:41  docker ps
```

## Features

- Readable dates instead of raw epoch timestamps
- Numbered results, newest at the bottom (closest to your prompt)
- Matched text highlighted in color
- Duplicates removed (the most recent run is kept)
- Result limit (default: 20)
- Plain mode for piping into other tools (`fzf`, `xclip`, ...)
- Supports zsh (extended format) and bash history, including `#timestamp` lines
- Extended regular expressions as patterns

## Requirements

- `bash`
- `gawk` (GNU awk). `mawk`, the default on some Debian/Ubuntu systems, will not work because it lacks `strftime`.

## Installation

### Manual

```bash
git clone https://github.com/rox-11/histg.git
cd histg
sudo install -m 755 histg /usr/local/bin/histg
```

Or without root:

```bash
install -m 755 histg ~/.local/bin/histg
```

Make sure `~/.local/bin` is in your `PATH`.

### Debian / Ubuntu (.deb)

Download the latest `.deb` from the [Releases](https://github.com/rox-11/histg/histg_1.0.0.deb) page:

```bash
sudo dpkg -i histg_1.1.0_all.deb
```


## Usage

```
histg [options] PATTERN
```

### Options

| Option | Description |
| --- | --- |
| `-i`, `--ignore-case` | Case-insensitive search |
| `-n`, `--limit N` | Show the last N results (default: 20) |
| `-a`, `--all` | Show all results |
| `-p`, `--plain` | Print only the commands (good for piping) |
| `--no-color` | Disable colors |
| `-f`, `--file FILE` | Use a specific history file |
| `-h`, `--help` | Show help |
| `-v`, `--version` | Show version |

`PATTERN` is an extended regular expression.

### Examples

```bash
histg docker                 # last 20 commands containing "docker"
histg -i DOCKER -n 5         # case-insensitive, last 5 results
histg -a "git (push|pull)"   # regex, all results
histg -p docker | fzf        # plain output piped into fzf
histg -f ~/old_history ssh   # search a specific history file
```

Copy a command to the clipboard:

```bash
histg -p -n 1 "docker run" | xclip -selection clipboard
```

## How it finds your history

`histg` looks for a history file in this order:

1. The file given with `-f`
2. `$HISTFILE` (if set and exported)
3. `~/.zsh_history`
4. `~/.bash_history`

## Tips

**Bash** only writes history to disk when the shell exits. To save each command immediately, add this to `~/.bashrc`:

```bash
PROMPT_COMMAND="history -a; $PROMPT_COMMAND"
```

**Zsh** also writes lazily by default. Add this to `~/.zshrc`:

```zsh
setopt INC_APPEND_HISTORY
setopt EXTENDED_HISTORY   # store timestamps
```

**Timestamps in bash:** to get dates in bash results, add `export HISTTIMEFORMAT="%F %T "` to `~/.bashrc`.


