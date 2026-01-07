Utilities for handling some mainframe operations on local machine.

First one is ftp-backup. I needed to backup my datasets from different mainframes and put them into local git.  Since zOSMF is not available for these installations I can't use zowe-cli for backup automation. So I used command line ftp client on Linux to do it. Should work on mac also, though wasn't tested yet.

ASCII transfer mode is on by default.

## ftp-backup

A bash script utility to backup datasets from mainframe using FTP. The script connects to a mainframe via FTP, downloads all members from specified datasets, and optionally commits them to a git repository.

### Configuration File

The script uses a configuration file with mainframe credentials and backup settings. The config file can be placed in:
- Current directory as `mf_backup_config`
- Home directory as `mf_backup_config`
- Or specified with `-f` option and have any name, for ex. you may want to have different configs for different setups. 

**You can't use ascii and binary modes for single config at the moment.**

#### Example Configuration File

```bash
# Mainframe's address and user's credentials
MF_HOST="mainframe1.example.com"
MF_USER="user001"
MF_PASS="123"

# Local directory on the machine where you are backing your datasets to. 
# It will be created if not present. Make sure you have permissions to create it.
BACKUP_DIR="/my/backup/mainframe1"

# This is datasets array. 
DATASETS=(
    "YOUR.DATASET1.NAME"
    "YOUR.DATASET2.NAME"
)

# If your backup directory is a git repo, you may enable git for automated commit and push
# your freshly downloaded datasets.
ENABLE_GIT=false
```

### Options

| Option | Description | Default |
|--------|-------------|---------|
| `-f <configfile>` | Path to config file containing credentials | Looks for `mf_backup_config` in current or home directory |
| `-l` | Enable logging to file | `false` (no logging) |
| `-g` | Enable git commit and push operations | `false` (no git operations) |
| `-a` | Use ASCII mode for file transfers | `ascii` (default) |
| `-b` | Use binary mode for file transfers | - |
| `-h` | Show help message and exit | - |

**Note:** Command line options override config file settings. For example, `-g` will enable git operations even if `ENABLE_GIT=false` in the config file.

### Usage Examples

#### Basic Usage (ASCII mode, no git)

```bash
./ftp-backup
```

This will:
- Use ASCII transfer mode (default)
- Look for config file in current or home directory
- Download datasets without committing to git
- No logging

#### With Git Operations

```bash
./ftp-backup -g
```

This will:
- Use ASCII transfer mode (default)
- Download datasets and automatically commit them to git
- Push to remote repository if configured

#### Binary Mode Transfer

```bash
./ftp-backup -b
```

This will:
- Use binary transfer mode 
- Download datasets without committing to git

#### Binary Mode with Git and Logging

```bash
./ftp-backup -b -g -l
```

This will:
- Use binary transfer mode
- Enable git commit and push operations
- Create a log file with timestamp in the backup directory

#### Custom Config File

```bash
./ftp-backup -f /path/to/my/config
```

#### ASCII Mode Explicitly (same as default)

```bash
./ftp-backup -a
```

### Requirements

- Bash shell
- FTP client (standard on Linux/Mac)
- Git (optional, only if using `-g` option)

### Notes

- Each dataset gets its own subdirectory in `BACKUP_DIR` (which also can be a git repo)
- Log files (when enabled) are created with timestamp: `backup_YYYYMMDD_HHMMSS.log`

