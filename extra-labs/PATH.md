# PATH
The objective of the lab was to configure `UMASK` and `PATH` for security.

## Extra Lab #1: The Current Directory in `PATH`
In this lab, we will study why the current directory (`.`) in `PATH` should be removed for security.

1. Make `PATH` starts with the current directory (`.`) temporarily.
```
# export PATH=.:$PATH
```

2. Let's make a *fake* `ls` file. It executes `/usr/bin/ls --color=auto`, which we use all the time, then prints out a message. Make it executable.
```
# echo -e "/bin/ls --color=auto\necho Hi!" > ls
# chmod +x ls
```

3. Run `ls`. 'Hi!' from the fake `ls` would appear.
```
# ls
```

In the lab, the fake `ls` was executed due to `.` in `PATH`. Suppose that the fake `ls` was made by an attacker. Then, its execution may result in a serious problem. Of course, the attack would make it do malicious behavior, rather than producing a message. So, system administrators need to check if `.` is included in `PATH`.
