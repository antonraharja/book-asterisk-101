# Basic Linux Console Commands

File and directory operations
-----------------------------

Commands | Functions
-------- | ---------
ls       | list file or directory
pwd      | show the present working Directory
cd       | change Directory
mkdir    | make directory 
touch    | create an empty file
rmdir    | remove empty directory
rm       | remove file or empty directory
rm -rf   | remove non-empty directory
cp       | copy files 
cp -rR   | copy directory and its content
mv       | move files 
cat      | display the content of file to output
chmod    | change file permissions
chown    | change file ownerships

Filters and tools
-----------------

Tools     | Descriptions
--------- | ------------
more      | filter the output at one page at a time, can only use `enter` to scroll down
less      | a better ```more```, use `enter` `Pg Up` `Pg Down` to scroll up and down
tail      | open a file and display the bottom part, can be use to follow file updates
tee       | receive input from file and save it on another
grep      | filter the output based on search criteria
cut       | cut the output based on delimeters
awk / sed | advance string manipulation
clear     | clear the console
history   | get the list of commands you have typed before

Example of filters:

Following updates on /var/log/syslog and save them in a file:

```
tail -f /var/log/syslog -n 0 | tee debug1.txt
```

Following updates, filter it and save them in a file:

```
tail -f /var/log/syslog | grep NetworkManager | tee debug2.txt
```

Get IP address of extension 20001:

```
asterisk -rx 'sip show peers' | grep 20001 | sed 's/  */\ /g' | cut -d' ' -f2
```

Get IP address of wlan0 (our network device):

```
ip addr | grep wlan0 | grep inet | cut -d'/' -f1 | sed 's/  */#/g' | cut -d'#' -f3
```

Another examples of getting IP address of wlan0 by using awk:

```
ip addr show dev wlan0 | awk '$1=="inet" {print $2}' | cut -d'/' -f1
ip addr show dev wlan0 | awk '$1=="inet" {print $2}' | awk -F'/' '{print $1}'
```

Example of find and replace the content of a file, in this example to change the repository URL from id.archive.ubuntu.com to kambing.ui.ac.id:

```
sed -i 's/id.archive.ubuntu.com/kambing.ui.ac.id/g' /etc/apt/sources.list
```

User management tools
---------------------

Tools               | Descriptions
------------------- | ------------
passwd              | change password
useradd / adduser   | add new user
userdel             | delete user
usermod             | modify user
groupadd            | add group
groupdel            | delete group
groupmod            | modify group

Text editors
------------

Editors      | Descriptions
------------ | ------------
vi / vim     | Linux native text editor
nano         | Ubuntu text editor
mc / mcedit  | Midnight Commander text editor

vi or vim commands
------------------

Commands       | Descriptions
-------------- | ------------
```<INSERT>``` | start editing by pressing INSERT button
i              | just like ```<INSERT>```
:w             | write to file or save
:wq            | save and quit
:q!            | quit and discard
dd             | delete entire line