# Project 2: Linux User and Permission Management

## 📌 Project Overview

This project demonstrates Linux user and group administration, password management, file ownership, permissions, and access control using Linux commands on an Amazon Linux server.

## 🎯 Objectives

- Create and manage Linux users.
- Create and manage groups.
- Assign users to groups.
- Configure file and directory permissions.
- Change file ownership.
- Understand Linux permission notation.
- Practice secure access management.

## 🛠️ Environment

- Operating System: Amazon Linux
- Platform: AWS EC2
- Access: SSH Terminal
- Shell: Bash
- Privileges: Root or sudo access

---

## 1. Check the Current User

**Command:**

```bash
whoami
```

**Sample Output:**

```text
root
```

**Explanation:** Displays the username of the currently logged-in user.

## 2. Display User ID and Group Information

**Command:**

```bash
id
```

**Sample Output:**

```text
uid=0(root) gid=0(root) groups=0(root)
```

**Explanation:** Displays the current user's UID, primary group ID, and supplementary groups. Output varies by account.

## 3. View Existing Users

**Command:**

```bash
cat /etc/passwd
```

To display only usernames:

```bash
cut -d: -f1 /etc/passwd
```

**Sample Output:**

```text
root
bin
daemon
ec2-user
```

**Explanation:** The `/etc/passwd` file contains account information. Modern Linux systems include both human users and service accounts.

## 4. Create a New User

Create a practice user named `devuser`.

**Command:**

```bash
useradd -m devuser
```

**Explanation:** Creates a user account and creates a home directory with `-m`.

Verify:

```bash
id devuser
```

**Sample Output:**

```text
uid=1001(devuser) gid=1001(devuser) groups=1001(devuser)
```

The UID and group IDs may differ on your server.

## 5. Set a User Password

**Command:**

```bash
passwd devuser
```

**Expected Interaction:**

```text
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

**Explanation:** Sets a password for `devuser`. Enter a strong password when prompted. The password characters will not appear on screen.

## 6. Verify the User's Home Directory

**Command:**

```bash
ls -ld /home/devuser
```

**Sample Output:**

```text
drwx------ 2 devuser devuser 62 Oct 10 10:00 /home/devuser
```

**Explanation:** Displays the directory permissions, owner, and group. Actual permissions may vary depending on system configuration.

## 7. Create a Linux Group

Create a group named `developers`.

**Command:**

```bash
groupadd developers
```

Verify:

```bash
getent group developers
```

**Sample Output:**

```text
developers:x:1002:
```

**Explanation:** Creates a new group for organizing users who need similar access.

## 8. Add a User to a Group

**Command:**

```bash
usermod -aG developers devuser
```

Verify:

```bash
id devuser
```

**Sample Output:**

```text
uid=1001(devuser) gid=1001(devuser) groups=1001(devuser),1002(developers)
```

**Explanation:** Adds `devuser` to the `developers` supplementary group.

**Important:** Use `-aG`, not just `-G`. The `-a` option appends the group without unintentionally removing existing supplementary group memberships.

## 9. View Group Information

**Command:**

```bash
cat /etc/group
```

To view only the developers group:

```bash
getent group developers
```

**Explanation:** Displays group definitions and associated members.

## 10. Create a File for Permission Testing

**Command:**

```bash
mkdir -p /opt/linux-permission-lab
touch /opt/linux-permission-lab/test.txt
echo "Linux permission management lab" > /opt/linux-permission-lab/test.txt
```

Verify:

```bash
cat /opt/linux-permission-lab/test.txt
```

**Output:**

```text
Linux permission management lab
```

**Explanation:** Creates a dedicated practice directory and a text file for permission experiments.

## 11. View File Permissions

**Command:**

```bash
ls -l /opt/linux-permission-lab/test.txt
```

**Sample Output:**

```text
-rw-r--r-- 1 root root 32 Oct 10 10:00 /opt/linux-permission-lab/test.txt
```

**Explanation:** The first ten characters represent the file type and permissions.

- `-` — regular file.
- `rw-` — owner can read and write.
- `r--` — group can read.
- `r--` — others can read.

The owner, group, file size, and timestamp are also displayed.

## 12. Understand Linux Permission Numbers

Linux uses three basic permission values:

| Permission | Symbol | Numeric value |
|---|---|---:|
| Read | `r` | 4 |
| Write | `w` | 2 |
| Execute | `x` | 1 |
| No permission | `---` | 0 |

Each permission digit represents owner, group, or others.

| Mode | Meaning |
|---|---|
| `777` | Everyone can read, write, and execute |
| `755` | Owner has full permissions; group and others can read and execute |
| `700` | Only owner has permissions |
| `644` | Owner can read/write; group and others can read |
| `600` | Owner can read/write; group and others have no access |

**Security note:** Avoid `777` on sensitive files because it grants write access to everyone.

## 13. Change File Permissions Using chmod

Give the owner read/write access and everyone else read access.

**Command:**

```bash
chmod 644 /opt/linux-permission-lab/test.txt
```

Verify:

```bash
ls -l /opt/linux-permission-lab/test.txt
```

**Expected permission string:**

```text
-rw-r--r--
```

**Explanation:** `chmod` changes the permissions of files and directories.

## 14. Restrict File Access to the Owner

**Command:**

```bash
chmod 600 /opt/linux-permission-lab/test.txt
```

Verify:

```bash
stat -c "%A %a %U %G %n" /opt/linux-permission-lab/test.txt
```

**Sample Output:**

```text
-rw------- 600 root root /opt/linux-permission-lab/test.txt
```

**Explanation:** Only the file owner can read or write the file under these permission settings.

## 15. Change Permissions Using Symbols

**Command:**

```bash
chmod u+x /opt/linux-permission-lab/test.txt
```

Verify:

```bash
ls -l /opt/linux-permission-lab/test.txt
```

**Explanation:** Adds execute permission for the owner.

Other examples:

```bash
chmod g+r /opt/linux-permission-lab/test.txt
chmod o-r /opt/linux-permission-lab/test.txt
chmod u-x /opt/linux-permission-lab/test.txt
```

- `g+r` adds read permission for the group.
- `o-r` removes read permission for others.
- `u-x` removes execute permission from the owner.

## 16. Change File Ownership

Change the owner of the practice file to `devuser`.

**Command:**

```bash
chown devuser /opt/linux-permission-lab/test.txt
```

Verify:

```bash
ls -l /opt/linux-permission-lab/test.txt
```

**Expected result:** The owner should be `devuser`.

**Explanation:** `chown` changes the owner of a file or directory. Root or an appropriately privileged account is generally required.

## 17. Change Group Ownership

Assign the file to the `developers` group.

**Command:**

```bash
chown devuser:developers /opt/linux-permission-lab/test.txt
```

Verify:

```bash
stat -c "%U %G %A" /opt/linux-permission-lab/test.txt
```

**Sample Output:**

```text
devuser developers -rw-r--r--
```

**Explanation:** Changes the file owner and group ownership.

## 18. Change Directory Permissions

**Command:**

```bash
mkdir -p /opt/linux-permission-lab/shared
chown root:developers /opt/linux-permission-lab/shared
chmod 770 /opt/linux-permission-lab/shared
```

Verify:

```bash
ls -ld /opt/linux-permission-lab/shared
```

**Expected permission string:**

```text
drwxrwx---
```

**Explanation:** The owner and group have full directory permissions; other users have no permissions. Directory execute permission allows traversal, while read permission allows listing entries.

## 19. Switch to Another User

**Command:**

```bash
su - devuser
```

Verify:

```bash
whoami
pwd
```

**Sample Output:**

```text
devuser
/home/devuser
```

Return to the previous shell:

```bash
exit
```

**Explanation:** Switches to the specified user. Depending on configuration, authentication may be required.

## 20. Test File Access

As `devuser`, test reading the practice file:

```bash
cat /opt/linux-permission-lab/test.txt
```

**Expected result:** The file content is displayed if the user has read access and can traverse the parent directories.

Test writing:

```bash
echo "Permission test" >> /opt/linux-permission-lab/test.txt
```

**Expected result:** The command succeeds only if the user has write access to the file and traversal access to its parent directories.

**Explanation:** Permission tests help confirm that the access rules work as intended. Root can bypass many ordinary discretionary permission checks, so test with an unprivileged account.

## 21. Lock a User Account

**Command:**

```bash
passwd -l devuser
```

Verify:

```bash
passwd -S devuser
```

**Explanation:** Locks the account password to prevent normal password authentication. This does not necessarily disable every possible authentication method.

Unlock the account when appropriate:

```bash
passwd -u devuser
```

**Warning:** Do not lock your own administrative account or a production account without confirming an alternative access method.

## 22. Delete a User Account

For this practice environment only, delete the test user after completing the lab.

**Command:**

```bash
userdel -r devuser
```

**Explanation:** Deletes the user and attempts to remove their home directory and mail spool.

**Warning:** This is destructive. Confirm that `devuser` is only your test account and that no required data remains before running it.

## 23. Remove the Practice Group

If the group is no longer required and has no remaining members or dependencies:

```bash
groupdel developers
```

Verify:

```bash
getent group developers
```

**Expected result:** No output if the group was successfully removed.

## 📚 Important Commands Learned

| Command | Purpose |
|---|---|
| `whoami` | Display current username |
| `id` | Display UID and group memberships |
| `useradd` | Create a user |
| `passwd` | Set, lock, or unlock a password |
| `groupadd` | Create a group |
| `usermod -aG` | Add a user to a supplementary group |
| `getent` | Query user/group databases |
| `chmod` | Change file permissions |
| `chown` | Change file ownership |
| `stat` | Display detailed file information |
| `su -` | Switch users with a login shell |
| `userdel` | Delete a user |
| `groupdel` | Delete a group |

## 🔐 Security Best Practices

- Follow the principle of least privilege.
- Avoid unnecessary root access.
- Use `sudo` for administrative tasks when appropriate.
- Do not use `chmod 777` as a routine fix for permission errors.
- Verify file ownership and permissions before changing them.
- Never share passwords or private SSH keys.
- Test destructive commands on practice resources before using them in production.

## ✅ Project Outcome

This project demonstrates practical Linux administration skills, including user creation, group management, file permissions, ownership changes, and access-control testing on Amazon Linux.

## 👨‍💻 Technologies Used

- Linux
- Amazon Linux
- Bash
- User and Group Administration
- File Permissions and Ownership
- AWS EC2
