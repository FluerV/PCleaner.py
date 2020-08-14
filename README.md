## PCleaner.py
PCleaner is the command-line script for editing files sorted by keywords.

#### Case insensitive searching
Uncomment lines if you want case insensitive searching.

#### Edit system files

1. Enter as administrator. 
2. After editing system files you need recompile the kernel: 

```
depmod -aeF /boot/System.map.... 
update-initramfs -u
```

