# UMASK and PATH
The objective of this lab is was configure UMASK and PATH for security.

## Extra Lab #1: The Current Directory in PATH
In this lab, we will study why the current directory (`.`) in PATH should be removed for security.

1. Make PATH starts with the current directory (`.`).
```
PATH=.:$PATH:$HOME/bin:/export/home/bin
```

2. Let's make a *fake* `ls` file. It runs `ls` then prints out a message. Make it executable.
```
echo -e "ls --color=auto\necho Hi!" > ls
chmod +x ls
```

3. Run `ls`. The message from the fake `ls` would appear.
```
ls
```

If the fake `ls` was made by an attacker, its execution may result in a serious problem. So, system administrators need to check if `.` is included in `PATH`.