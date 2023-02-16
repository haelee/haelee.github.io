# UMASK and PATH
The objective of this lab is was configure UMASK and PATH for security.

## Extra Lab #2: The Current Directory in PATH
In this lab, we will study why the current directory (`.`) in PATH should be removed for security.

1. Make PATH starts with the current directory (`.`).

2. Let's make a *fake* `ls` file in the current directory. It will run `ls` then print out *Hi!* message. Make it executable.
```
echo -e "ls --color=auto\necho Hi!" > ls
chmod +x ls
```

3. Try to run `ls`.
```
ls
```

