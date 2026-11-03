# Linux & Bash Notes

## Linux File System Hierarchy

Everything starts at the root `/`.

| Directory | Purpose |
|-----------|---------|
| `/bin` | Essential **user binaries** (`ls`, `cp`, `cat`) |
| `/sbin` | **System binaries** for administration (`fdisk`, `reboot`, `iptables`) |
| `/etc` | System-wide **configuration files** (`/etc/hosts`, `/etc/passwd`) |
| `/dev` | **Device files** (disks `/dev/sda`, `/dev/null`) |
| `/proc` | Virtual FS with **process and kernel information** (`/proc/cpuinfo`, `/proc/<pid>`) |
| `/var` | **Variable files** that grow: logs (`/var/log`), caches, spool, databases |
| `/tmp` | **Temporary files**, usually cleaned on reboot |
| `/usr` | **User programs**, libraries and docs (`/usr/bin`, `/usr/local`) |
| `/home` | **Home directories** of users (`/home/<user>`) |
| `/boot` | **Boot loader files** (kernel, initrd, GRUB) |
| `/lib` | **System libraries** needed by `/bin` and `/sbin` |
| `/opt` | **Optional / third-party applications** |
| `/mnt` | Temporary **mount directory** for file systems |
| `/media` | Mount point for **removable devices** (USB, CD) |
| `/srv` | **Service data** served by the system (web, FTP) |

## Linux Commands Cheat Sheet

### Navigation & files
| Command | Description |
|---------|-------------|
| `ls` | List directory contents (`ls -la` shows hidden files and details) |
| `pwd` | Print working directory |
| `cd` | Change directory (`cd ..`, `cd ~`, `cd -`) |
| `mkdir` | Create directories (`mkdir -p a/b/c`) |
| `mv` | Move or rename files |
| `cp` | Copy files (`cp -r` for directories) |
| `rm` | Delete files or directories (`rm -r`, careful with `-f`) |
| `touch` | Create empty files / update timestamps |
| `ln` | Create links (`ln -s target link` for symbolic links) |
| `tar` | Archive/compress (`tar -czvf out.tar.gz dir/`, extract `tar -xzvf file.tar.gz`) |
| `zip` / `unzip` | Zip and unzip files |
| `whereis` | Locate the binary, source and man pages of a command |
| `dd` | Low-level copy, mostly used for creating bootable USB sticks |

### Viewing & comparing text
| Command | Description |
|---------|-------------|
| `cat` | Display file contents |
| `less` | Paged output (scroll, `/` to search, `q` to quit) |
| `head` | First N lines (`head -n 20 file`) |
| `tail` | Last N lines (`tail -f file` to follow logs) |
| `echo` | Print text |
| `grep` | Search for a string/regex in output or files (`grep -rni "text" .`) |
| `sort` | Sort lines |
| `diff` | Show differences between two files |
| `cmp` | Check if two files are identical (byte by byte) |
| `comm` | Compare two **sorted** files line by line (common / unique lines) |

### System information & help
| Command | Description |
|---------|-------------|
| `man` | Manual pages for a command |
| `whatis` | One-line description of what a command does |
| `uname` | Basic system info (`uname -a`) |
| `whoami` | Current username |
| `cal` | Command-line calendar |
| `df` | Disk filesystem usage (`df -h`) |
| `mount` | Mount file systems |
| `clear` | Clear the terminal |
| `alias` | Create shortcuts (`alias ll='ls -la'`) |
| `export` | Export environment variables (`export PATH=$PATH:/opt/bin`) |

### Processes & services
| Command | Description |
|---------|-------------|
| `ps` | Display active processes (`ps aux`) |
| `top` | Live view of processes and resource usage (`htop` is a nicer alternative) |
| `kill` / `killall` | Kill processes by PID / by name (`kill -9 <pid>`) |
| `service` | Start/stop services (`systemctl` on systemd distros) |

### Users & permissions
| Command | Description |
|---------|-------------|
| `sudo` | Run a command with elevated privileges |
| `chmod` | Change file permissions (`chmod 755 script.sh`, `chmod +x script.sh`) |
| `chown` | Change file owner (`chown user:group file`) |
| `useradd` / `usermod` | Add a new user / modify an existing one |
| `passwd` | Update passwords |

### Networking & packages
| Command | Description |
|---------|-------------|
| `ssh` | Secure shell to a remote host (`ssh user@host`) |
| `wget` | Download files from the internet (also `curl`) |
| `ifconfig` | Network interfaces and IP addresses (modern: `ip a`) |
| `traceroute` | Trace the network hops to a destination |
| `ufw` | Uncomplicated firewall (Ubuntu) |
| `iptables` | Base firewall that other firewall tools interface with |
| `apt` | Package manager on Debian/Ubuntu (`dnf`/`yum` on RHEL, `brew` on macOS) |

## Bash Scripting Basics

Run with `bash script.sh` or `chmod +x script.sh && ./script.sh`.

```bash
#!/bin/bash                                   # Shebang: which interpreter runs the script

# --- Variables (no spaces around =) ---
username="Jay"
filename=$3                                   # third command-line argument

# --- User input ---
read -p "Enter your username: " user
echo "Username: $user"

# --- Conditional if ---
if [ "$EUID" -ne 0 ]; then
    echo "You are not running this script as the root user."
else
    echo "You are running this script as the root user."
fi

# --- For loop ---
for i in {1..5}; do
    echo "$i"
done

# --- Functions ($1 = first argument of the function) ---
function greet() {
    echo "Hello, $1!"
}
greet "Alice"

# --- Case statement ---
read -p "Enter a number between 1 and 2: " num
case $num in
    1) echo "You chose one." ;;
    2) echo "You chose two." ;;
    *) echo "Invalid choice." ;;
esac

# --- File operations (-e exists, -d directory, -f regular file) ---
if [ -e "$filename" ] && [ -d "$filename" ]; then
    echo "File exists and is a directory."
else
    echo "File does not exist or is not a directory."
fi

# --- Command-line arguments ($0 script, $1..$9 args, $# count, $@ all) ---
echo "First argument: $1"
echo "Second argument: $2"

# --- Exit status codes ($? = exit code of last command, 0 = success) ---
cat nonexistent-file.txt 2> /dev/null
echo "Exit status: $?"

# --- Indexed arrays ---
fruits=("Apple" "Orange" "Banana")
echo "Fruits: ${fruits[0]}"                   # ${fruits[@]} = all elements

# --- Associative arrays (bash 4+) ---
declare -A capitals
capitals[USA]="Washington D.C."
capitals[France]="Paris"
echo "Capital of France: ${capitals[France]}"

# --- Command substitution ---
current_date=$(date)
echo "Today's date is: $current_date"

# --- Redirections: > overwrite, >> append, 2> stderr, &> stdout+stderr ---
echo "This is a sample text." > example.txt
find / -name hello.txt &> /dev/null

# --- Arithmetic ---
result=$(expr 15 - 2)                         # modern: result=$((15 - 2))
echo $result

# --- Parameter expansion: ## removes the longest prefix matching the pattern ---
SRC="/path/to/foo.cpp"
BASEPATH=${SRC##*/}                           # -> foo.cpp
echo $BASEPATH

# --- Signal handling ---
trap 'echo "Received SIGTERM signal. Cleaning up..."; exit' SIGTERM

# This is a single line comment
: '
This is a
multiline comment
'
```

**Tips**
- Always quote variables: `"$var"` (avoids word splitting with spaces).
- Add `set -euo pipefail` at the top of scripts: exit on error, on undefined variables, and on failures inside pipes.
- Use `[[ ... ]]` instead of `[ ... ]` in bash for safer tests and regex support (`[[ $x =~ ^[0-9]+$ ]]`).
- Check scripts with `shellcheck script.sh`.

See also: `zsh_setup_guide.md` for shell setup.
