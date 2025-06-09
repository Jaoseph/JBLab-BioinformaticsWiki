# Transferring Files for Collaborators (via CRUK FTP Server)

##### _Funny quote coming for something not funny_

---

Use the FTP server to share files **with external collaborators** (e.g. non-CRUK partners).

## ⚠️ Not Sure If You Should Use FTP?⚠️ 
Ask your supervisor or a senior lab member. 

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
lftp -u username ftp2.cruk.cam.ac.uk
```

→ Note: If you have set up a `~/.netrc` (see below), you do not need to enter the password

## Option 1: Quick Transfer via Command Line 

### 🔼 Upload a File 

```bash
lftp -e "put -O /target/folder /path/to/local_file.txt; bye" \
-u Request username from bioinformatician ftp2.cruk.cam.ac.uk
```

## Option 2: Auto-login Set-up and Upload Bam Files via Batch Script

### Create ftps login script

```bash
vim ~/.netrc
```

### Add login details

```bash
machine ftp2.cruk.cam.ac.uk
login Request username from bioinformatician
password Request password from bioinformatician]
```

### Secure the file (given that this is sensitive information)

```bash
chmod 600 ~/.netrc
```

## Sample Upload Script

```bash
#!/bin/bash

for dir in /mnt/nas-data/jblab/group_folders/bradle02/otta_tailor_bio_handover/{RAM-,SLX-}*; do
  pooled_library_id=$(basename "$dir")
  bamdir="$dir/downsampled_bams/curation"

  echo "Uploading from $bamdir..."

  lftp ftp2.cruk.cam.ac.uk <<EOF
  set ftp:ssl-force true
  set ftp:ssl-protect-data true
  set ftp:passive-mode true

  mkdir OTTA_Merridee/${pooled_library_id}
  cd OTTA_Merridee/${pooled_library_id}
  mkdir downsampled_bams_curation
  cd downsampled_bams_curation

  lcd $bamdir
  mput *.bam
  bye
EOF

done
```

### The script is:
- Looping through all RAM- and SLX- folders
- Entering the downsampled_bams/curation subfolder
- Uploading all .bam files to corresponding directories on the FTP server under OTTA_Merridee/

## Option 3: Accessing through FileZilla (Graphical User interface)

### Access details

| Field       | Value                                      |
|-------------|--------------------------------------------|
| **Host**    | `ftp2.cruk.cam.ac.uk`                      |
| **Username**| _Request username from bioinformatician_   |
| **Password**| _Request password from bioinformatician_   |
| **Port**    | Leave Blank                                |

Then drag and drop files to upload or download.
