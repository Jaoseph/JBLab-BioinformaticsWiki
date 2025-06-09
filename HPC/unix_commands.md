# Unix Command Cheatsheet

##### _A beginner-friendly guide to basic Unix commands for bioinformatics_

--- 

## UNIX Command Syntax Structure

In UNIX, commands follow a standard and specific structure:

```bash
command [options] [arguments]
```

Where:

- ```command``` is the name of the program or tool you want to run aka. the **action** you want to do 
    - e.g., ```ls``` to list files, ```cd``` to change directories(folders)
- ```[options]``` these change how the command behaves, and are usually predixed with a dash (-) for short options or double dash (--) for long options. 
    - Some options have a short and long option which performs the same modification, e.g., ```ls``` ```-a```/```--all``` 
    - Options are **optional**, You can choose to use one, many, or none.
- ```[arguments]``` are the **targets** you want the command to work on (like a file or folder)

> ⚠️ Important rule:
    Put a space between the command, each option, and each argument. This is very strict with no commas, no equal signs but JUST SPACES.

Example:
```bash
ls -lh /scratchc/jblab/
```
This runs the `ls` command with `-l` (long listing) and `-h` (human-readable file sizes), applied to the `/scratchc/jblab/` folder.

---

## Common Unix / computation terms dictionary

| Term | Meaning / Equivalent | Example |
|------|-----------------------|---------|
| Terminal | The command-line interface where you type Unix commands | |
| Directory | Folder | `jblab/` |
| Path | Location of a file or folder | `scratchc/jblab/` |
| Absolute path | Full path from the root directory (starts with `/`) | `/scratchc/jblab/chong02/snakemake-illumina-alignment/` |
| Relative path | Path relative to your current location | If you're in `jblab/`, then `chong02/snakemake-illumina-alignment/` |
| Script | A file containing a list of Unix or R commands | `disk_usage.sh`, `run_pipeline.R` |
| Home directory | Your personal root folder | `~` or `/home/chong02/` |
| Parent directory | The folder above the current one | `..` |


---

## ⚠️ Common Beginner Mistakes

Understanding these common errors can save you hours of confusion!

| Mistake | Why it’s wrong | What to do |
|--------|----------------|----------------|
| Forgetting spaces between the command, options, and arguments | UNIX is very **strict** with spacing, and merging them together makes it think you're calling a different (invalid) command | `ls -l` not `ls-l` |
| Using commas or equal signs between arguments | UNIX doesn’t use `,` or `=` to separate inputs | `cp file1.txt file2.txt` not `cp=file1.txt,file2.txt` |
| Confusing relative vs absolute paths | UNIX commands need the correct location of the file. If it’s not in your current directory, you must give a path to it — UNIX won’t search for it automatically | Use a relative path like /scripts/script.sh, or an absolute path like /scratchc/jblab/chong02/scripts/script.sh
| Running `bash script.sh` from the wrong directory | The shell looks for `script.sh` in the current folder, and will return `No such file or directory` if it’s not there | Check if you are currently in the directory which contains the `script.sh` or provide the absolute path to the `bash /full/path/to/script.sh` |
| Typing `cd folder/file.txt` | `cd` is for directories only, not files | Use `cd folder/`, then `ls` to view files within your current directory |
| Using `rm` without checking | You can accidentally delete important files | Always use `ls` first, or `rm -i` to confirm |
| Using `rm` without checking | You can accidentally delete important files | Always use `ls` first, or `rm -i` to confirm |

---

## Understanding Slashes in Paths

In Unix, the slash `/` is used to separate folders and files in paths. But where you place it can change what the path means:

| Usage | Meaning | Example |
|-------|---------|---------|
|`/` at the start | Means you are using an absolute path, starting from the root of the system | `/scratchc/jblab/chong02/snakemake-illumina-alignment/workflow/scripts/download_SLX.sh` |
|`/` at the end | Optional, denotes a directory, but can help with clarity in tools like `cd`, `cp`, `rsync` | `cd mydirectory` (makes it clear `mydirectory/` is a directory) |
|No `/` at the start | A relative path, means "start from the directory i am in" | `scripts/download_SLX.sh` means go into scripts directory and run `download_SLX.sh` |


### Summary:

- Use a **Leading** `/` when you are giving the full absolute path startin from the root (`/`)
- Use a **Trailing** `/` when you want to make it extra clear that you are referring to a folder (Helps especially with `cd`, `cp`, and `rsync`). 
    - A good habit in my opinion.
- You don't need a trailing `/` for filenames
- Never use `/` inside a filename as it is a separator for directory structure, not part of the name


### When `/` does not matters.

```bash
cd mydirectory
cd mydirectory/
```

These two are effectively the same, they take you into `mydirectory`. The trailing slash `/` is optional and mostly stylistic for `cd`

### When `/` matters.

```bash
cp -r mydirectory backup/
```
→ Copies the entire `mydirectory` directory into `backup/`

```bash
cp -r mydirectory/ backup/
```
→ Copies only the contents of `mydirectory`, not the folder itself

### Example use cases

cd /home/user/project/        # absolute path to a directory
cd project/                   # relative path
ls /etc/passwd                # absolute path to a file
ls data/results.txt           # relative path to a file
bash scripts/run.sh           # run a script in a subdirectory
bash /home/user/scripts/run.sh  # run script using full path
cp -r mydirectory backup/             # copies the whole `mydirectory` directory into `backup/`
cp -r mydirectory/ backup/            # copies the **CONTENTS** of `mydirectory` into `backup/`, not the folder itself
rsync -av mydirectory backup/         # syncs the whole `mydirectory` directory
rsync -av mydirectory/ backup/        # syncs **CONTENTS** of `mydirectory`, not the folder itself

---

## File and Directory Management

| Task | Command | Notes |
|------|---------|-------|
| Create a directory | `mkdir mydirectory` | Creates a new empty directory |
| Delete a file | `rm file.txt` | Permanently removes the file (⚠️ NO CONFIRMATION ⚠️) |
| Delete a directory | `rm -r mydirectory/` | Permanently removes the directory and everything inside (⚠️ CAUTION MAKE SURE YOU WANT TO DELETE ⚠️)|
| Rename a file | `mv oldname.txt newname.txt` | Keeps the file in the **same directory**, but changes its name |
| Move a file to a directory | `mv file.txt /path/to/destinationfolder/` | Moves the file to a different directory, keeping the same name |
| Move a file to a directory (Full Path) | `mv /path/to/source/file.txt /path/to/destinationfolder/` | Safer and more explicit by providing the absolute file of both source and destination (if you are unsure about paths and your current location within the file system) |
| Move and rename a file | `mv file.txt /path/to/destinationfolder/newname.txt` | Creates a new empty directory | 
| Move and rename a file (Full Path) | `mv /path/to/source/file.txt /path/to/destinationfolder/newname.txt` | Safer and more explicit by providing the absolute file of both source and destination (if you are unsure about paths and your current location within the file system) |
---

## Copying Files and directories

| Task | Command | Notes |
|------|---------|-------|
| Copy general syntax | `cp [source] [destination]` | Works for files and paths (See below) | 
| Copy a file to a directory | `cp file.txt` `/path/to/distination/directory/file.txt` | Copies file into the directory, assuming that you are in the directory where file.txt is stored. Otherwise, `cp /path/to/source/directory/file.txt /path/to/distination/directory/file.txt`
| Copy a file to a directory (Full Path) | `cp /path/to/source.txt /path/to/destination.txt` | Safer and more explicit by providing the absolute file of both source and destination (if you are unsure about paths and your current location within the file system)
| Copy the whole directory | `cp -r sourcedir/ /path/to/destination/` | Use `-r` to copy directory and its contents
| Copy the whole directory  (Full Path) | `cp -r /path/to/sourcedir/ /path/to/destination/` | Safer and more explicit by providing the absolute file of both source and destination (if you are unsure about paths and your current location within the file system)

---

## Running Scripts with Software

| Task | Command | Notes |
|------|---------|-------|
| Run software on a file/script | `[software] [script_name]` | Runs the given file using the specified software. For example, `python run.py` runs the Python script `run.py`, and `R run.R` runs the file `run.R` using R. |
| Run a Python script | `python script.py` | Runs the script `script.py` using Python. |
| Run an R script | `R script.R` | Runs the script `script.R` in the R terminal. You can also use `Rscript script.R` for command-line output only. |
| Run a Bash script | `bash script.sh` | Runs the script `script.sh` using Bash. This works even if the script is not executable. |
Make sure you're in the correct directory or provide the full path to the script.
> ⚠️ Important :
    Make sure you're in the correct directory or provide the full path to the script.**

---

## Utility Commands

| Task | Command | Notes |
|------|---------|-------|
| View the manual/help for a command | `man [command]` | Opens the manual page for the command, including all optuons and examples. Press q to quit.
| View the manual/help for a command | `command --help` | Displays a short help message explaining how to use the command, its options, and arguments. Often quicker and easier to read than `man`. Not available for every command, but works for most modern tools. |
| Find the full path of a command | `which [command]` | Shows the location of the executable that will run when you type the command. E.g., `which python` might return `/usr/bin/python`. |

---


## File Permissions

| Task | Command | Options/Notes |
|------|---------|---------------|
| View file permissions | `ls -l` | Displays permissions, ownership, size, and timestamp. The string on the left (e.g. `-rw-r--r--`) shows who can read/write/execute. |
| Make a file executable | `chmod +x filename`| Adds executable permissions for the user (you). Required to run .sh scripts |
| Set exact permissions numerically | `chmod 755 filename` | 7 = read+write+execute, 5 = read+execute. (4 = read) So this makes it executable for all but only writable by you. |


### Quick Reference: Permission String and Codes

When you run:

```bash
ls -l filename
```

You will see an output as such:

`-rw-r--r--`

The permission string is interpreted in **triplets** as follows:

| Position | Meaning | Example:
|------------|--------|--------|
| 1st char | File type (`-` = file, `d` = directory) | `-` means it's a regular file |
| 2-4 | **Owner (you)** permissions | rwx → read, write, and execute |
| 5-7 | **Group (Brenton Group)** permissions | r-x → read and execute |
| 8-10 | **Others (everyone else)** permissions | r-- → read only |

Each permission type is represented by a corresponding letter and number:

| Permission | Value |
|------------|--------|
| `r` (read) | 4 |
| `w` (write) | 2 |
| `x` (execute) | 1 |
| `-` (none) | 0 |

To convert a permission string to number (for `chmod`), simply sum the values in each group

Example: 

 `-rw-r--r--` 

| Who | Symbol |
|------------|--------|
| User | rw- | 4 + 2 = 6
| Group | r-- | 4 + 0 = 4
| Others | r-- | 4 + 0 = 4

So, `-rw-r--r--` → 

```bash
chmod 644 filename
```

This means that:
- You can read and write
- Everyone else can only read

---