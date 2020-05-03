# PCleaner.py
PCleaner is the command-line script for editing files sorted by keywords.
Uncomment lines if you want case insensitive searching.
Enter as administrator if you want to edit system files. After editing system files you need recompile the kernel: depmod -aeF /boot/System.map.... and update-initramfs -u

#!/usr/bin/python
#
import sys
import os
import tempfile
from time import gmtime, strftime

class SkipThis(Exception):
    pass

class Keyboard(Exception):
    pass

class Remove_file(Exception):
    pass

def fun(*files):
    keyword = input("\nSearch for?: ")
    key_change = input("Change to: ")
   #keyword = keyword.casefold()
    sf = []
    for file in files:
        for filename in file: 
            d = os.getcwd()
            while d != "/" and filename not in os.listdir(d):
                d = os.path.abspath(d + "/../")
            filename = os.path.join(d, filename)
            print("\n\nFile path is: " +  filename + "\n")
            if os.path.isfile(filename):
                sf.append(filename)
            elif os.path.isdir(filename):
                for folder, subs, files in os.walk(filename):
                    for fl in files:
                        filepath = os.path.join(folder, fl)
                        sf.append(filepath)
            else:
                print("Try again")
    for i in sf:
        tmp = tempfile.mkstemp()
        try:
            with open(i, 'r', encoding='utf-8', errors='ignore') as fd1, open(tmp[1], "w", encoding='utf-8', errors='ignore') as fd2:
                for ln in fd1:
                   #ln = ln.casefold()
                    if keyword in ln:
                        if key_change:
                            ln = ln.replace(keyword, key_change)
                        print("File name is: ", i)
                        dpath = os.path.join(os.path.expanduser("~"), "Cleaner")
                        if not os.path.exists(dpath):
                            os.makedirs(dpath)
                        fstr = "cleaner.txt"
                        fpath = os.path.join(dpath, fstr)
                        f = open(fpath, "a+")
                        f.write(strftime("%Y-%m-%d %H:%M:%S", gmtime()) + "\n")
                        f.write("Change " + keyword + " " + "to: " + key_change + "\n")
                        f.write(i + "\n")
                        f.write(ln + "\n\n")
                        f.close()
                        yield ln.rstrip('\n')
                    fd2.write(ln)
            os.rename(tmp[1], i)
        except Remove_file:
            os.remove(i)
            os.remove(tmp[1])
            continue
        except SkipThis:
            yield 'dummy'
            continue
        except Keyboard:
            sys.exit()
        except FileNotFoundError:
            continue

def display_files(*files):
    source = fun(*files)
    for line in source:
        print("\nLine content is: " + line, end = '')
        inp = input("\n\nContinue this file press 'Enter'\nDelete this file press 'd'\nJump to the next file press 'n'\nClose programm press 's'")
        if inp == 'd':
            source.throw(Remove_file)
        if inp == 'n':
            source.throw(SkipThis)
        if inp == 's':
            source.throw(Keyboard)
            
display_files(sys.argv[1:])







