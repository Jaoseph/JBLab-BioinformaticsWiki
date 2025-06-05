# Unix Command Cheatsheet

_A beginner-friendly guide to basic Unix commands for bioinformatics_

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


--- 

## Navigation commands

| Task | Command | Options/Notes |
|------|---------|-------|
| Print current directory within the file system | `pwd` | Shows where you are in the file system |
| List files | `ls` | Common options: `-l` (long format), `-a` (show hidden), `-h` (human-readable sizes), `-r` (reverse order), `-t` (sort by modified time) |
| Change directory | `cd directoryname/` | Use `cd ..` to move up one level |
| Autocomplete paths | Press `Tab` | Autocompletes directory or file names based on what you've typed so far. If multiple matches exist, pressing Tab twice will show you all options. Keeps autocompleting until ambiguity is reached |

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
