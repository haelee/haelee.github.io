# Checking the UID of a User
The objective of problem #1 is to find the UID of a user using the `id` command. In Unix-like systems, the privileges of a user is basically determined based on the UID stored in the `/etc/passwd` file.

## Extra Lab #1: Users with the Same UID
Then, what if there are two or more users who have the same UID in `/etc/passwd`? Let’s check this! Note that you should be the root user or have the root privileges (i.e., using the `sudo` command).

1. Add two users `ntu2023` and `cju2023`.
```
# adduser ntu2023
# adduser cju2023
```

2. Switch to `ntu2023` then create a file in the home directory.
```
# su ntu2023
$ echo "Hello" > ~/ntu
```

3. Make only `ntu2023` (and `root`) can read and write the file we've just created (i.e., set its permissions to `600`).
```
$ chmod 600 ~/ntu
```

4. Switch back to `root` (or simply open a new terminal emulator) then switch to `cju2023`. Try to read the file we've created. Your access to the file would be denied since it can be accessed *only by* `ntu2023` and `root` (permission `600`).
```
$ exit
# su cju2023
$ cat ~ntu2023/ntu
cat: /home/ntu2023/ntu: Permission denied
```

5. Now let’s modify the UID of `cju2023`. To this end, first switch back to `root` then open `/etc/passwd` with text editor `vi` (or `nano`). 
```
$ exit
# vi /etc/passwd
```

6. Change the UID (and GID optionally) of `cju2023` to that of ntu2023. Save the file then quit the editor.
```
ntu2023:x:515:515::/home/ntu2023:/bin/bash
cju2023:x:515:515::/home/ntu2023:/bin/bash
```

7. Switch to `cju2023` then try to read the file again. You’re now able to access the file! This is because the UID of `cju2023` stored in `/etc/passwd` is equal to that of `ntu2023`. That is, you’re now virtually `ntu2023` although you’ve logged in with `cju2023`. You would face an error while switching the user. But it's OK. The error is due to other users, except `cju2023` and `root`, are not allowed to execute the `/home/cju2023/.bashrc` file (You can check the file permissions with `ls -l /home/cju2023/.bashrc`).
```
# su cju2023
bash: /home/cju2023/.bashrc: Permission denied
$ cat ~ntu2023/ntu
Hello
```

This lab confirms that the privileges of a user is *solely* determined based on the UID stored in `/etc/passwd`. So, system administrators should check if users with the same UID exist in `/etc/passwd`.

## Extra Lab #2: Non-Root User with UID `0`
Then, what if there's a non-root user whose UID is `0` (zero)? Let’s check this! Note that you should be `root` or have the root privileges (i.e., using `sudo`).

1. Switch to `ntu2023` then try to read file `/etc/shadow`. Your access to the file would be denied. This is because `/etc/shadow` can be accessed *only by* `root`, and `ntu2023` doesn't have the root privileges.
```
# su ntu2023
$ cat /etc/shadow
cat: /etc/shadow: Permission denied
```

2. Switch back to `root` then open `/etc/passwd` with text editor `vi` (or `nano`). 
```
$ exit
# vi /etc/passwd
```

3. Set the UID (and GID optionally) of `ntu2023` to `0` (zero). Save the file then quit the editor.
```
ntu2023:x:0:0:/home/ntu2023:/bin/bash
```

4. Switch to `ntu2023` again then you would see the root prompt (`#`). Now you’re `root`! Of course, you are now able to freely access `/etc/shadow`.
```
# su ntu2023
# cat /etc/shadow
```

This lab also confirms that the privileges of a user is solely determined based on the UID in `/etc/passwd`. So, system administrators should check if a non-root user whose UID is `0` exists in `/etc/passwd`.

## Extra Lab #3: Password-less User Account with UID `0`
In this lab, we will add a *password-less* user account whose UID is `0`. Note that you should be `root` or have the root privileges.

1. Type as follows. The command creates password-less user account `dudu2023` whose UID is `0`.
```
# echo "dudu2023:U6aMy0wojraho:0:0::/root:/bin/bash" >> /etc/passwd
```

2. Log out then log in with `dudu2023`. You don’t need a password to log in (just press `Enter`). By opening a terminal emulator, you would see `#`. Now you’re `root`!

By exploiting a race condition (or similar) vulnerability, even an attacker without the root privilege may be able to add such a user account in `/etc/passwd`. Once the attacker has successfully added, the attacker may be able to freely access the system as `root`. So, system administrators should check if such a user exists in `/etc/shadow`.