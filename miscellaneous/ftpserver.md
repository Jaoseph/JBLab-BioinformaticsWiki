# Transferring Files for Collaborators (via CRUK FTP Server)

##### _Funny quote coming for something not funny_

---

Use the FTP server to share files **with external collaborators** (e.g. non-CRUK partners).

## ⚠️ Not Sure If You Should Use FTP?⚠️ 
Ask your supervisor or a senior bioinformatican lab member. 

---

## 📁 FTP Server Details

| Field       | Value                                      |
|-------------|--------------------------------------------|
| **Host**    | `ftp2.cruk.cam.ac.uk`                      |
| **Protocol**| FTPS (Explicit SSL/TLS)                    |
| **Username**| _Request username from bioinformatician_   |
| **Password**| _Request password from bioinformatician_   |

---

## Quick assess to the FTP server command:

```bash
lftp -u <username> ftp2.cruk.cam.ac.uk
```

→ Note: If you have set up a `~/.netrc` (see below), you do not need to enter the password

## Option 1: Quick Transfer via Command Line 

### 🔼 Upload a File 

```bash
lftp -e "put -O /target/ftp_folder /path/to/local_file.txt; bye" \
-u <username> ftp2.cruk.cam.ac.uk
```

## Option 2: Auto-login Set-up and Upload Bam Files via Batch Script

### Create ftps login script

```bash
vim ~/.netrc
```

### Add login details

```bash
machine ftp2.cruk.cam.ac.uk
login <username>
password <password>
```

### Secure the file (given that this is sensitive information)

```bash
chmod 600 ~/.netrc
```
This allows lftp to log in automatically.

## Sample Batch Upload Script

This is useful when sharing sequencing data such as:

- BAM files
- Bai index files 
- Any directory containing multiple samples or files

Below is a template script for uploading multiple sample directories from the local directory to a directory on the FTP server, as well as producing `md5sums.txt` which is used to verify the integrity of a file (This is required by the reciever to be able to use and open the file).

```bash
#!/bin/bash

########################################################################################
# Template Script for Uploading Multiple Files/Directories to an FTPS Server Using lftp
########################################################################################

# Local base directory containing the folders to upload
base="/path/to/local/base_directory"

# Remote FTPS directory where files will be uploaded
ftp_base="remote_target_directory"

# Loop through each project folder (replace with your own folders)
for project in project1 project2; do
  
  # Loop through each subdirectory within the project folder
  for dir in "$base/$project"/*; do

    # Skip if not a directory
    [ -d "$dir" ] || continue

    # The name of the directory being uploaded (e.g., RAM-1, SLX-12345)
    directory_id=$(basename "$dir")
    echo "Uploading: $directory_id"

    # Generate MD5 checksums
    md5file="$dir/md5sums.txt"
    echo "Generating md5 checksums for $directory_id..."

    files=( "$dir"/*.bam "$dir"/*.bai )
    md5sum "${files[@]}" > "$md5file"

    # Upload using lftp
    lftp ftp2.cruk.cam.ac.uk <<EOF
set ftp:ssl-force true
set ftp:ssl-protect-data true
set ftp:passive-mode true

# Navigate to target remote folder
cd $ftp_base

# Create remote directory if it doesn't already exist
mkdir $directory_id || true
cd $directory_id

# Upload all files in this folder
lcd $dir
mput *.bam
mput *.bai
put md5sums.txt

bye
EOF

  done
done

```

### Running the Script with Logging

Once you have written that into a `.sh` file, you can now run the following, which will 

- Run the script in the background (even after you have closed the terminal or HPC server connection)
- Generate a log file for monitoring progress:

```bash
nohup bash FTPS_upload.sh > FTPS_upload.log 2>&1 &
```

## Option 3: Accessing through FileZilla (Graphical User interface)

### Access details

| Field       | Value                                      |
|-------------|--------------------------------------------|
| **Host**    | `ftp2.cruk.cam.ac.uk`                      |
| **Username**| _Request username from bioinformatician_   |
| **Password**| _Request password from bioinformatician_   |
| **Port**    | Leave Blank                                |

Then drag and drop files to upload or download.


## Configure Default Directories for connection and download

https://filezillapro.com/docs/v3/faq/how-to-configure-default-directories-for-a-connection/
