This repository will provide solutions to the Red Hat Certified Systems Administrator (RHCSA EX200 ) exam preparation tasks. Tasks will be executed using Rocky Linux 9 systems in an Oracle VirtualBox environment.

![alt text](./assets/diagram1.png)  

# Question 1 

Renaming servers and assigning IP addresses:

Server A:
```bash
su -
hostnamectl set-hostname server-a
nmcli connection show
nmcli con modify "enp0s2" ipv4.method manual ipv4.addresses "192.168.0.50/24" ipv4.gateway 192.168.0.1 ipv4.dns 8.8.8.8
nmcli con up "enp0s2"
```
Server B:
```bash
su -
hostnamectl set-hostname server-b
nmcli connection show
nmcli con modify "enp0s2" ipv4.method manual ipv4.addresses "192.168.0.51/24" ipv4.gateway 192.168.0.1 ipv4.dns 8.8.8.8
nmcli con up "enp0s2"
```

# Question 2 
Configuring a local DNF repository on Server A using the Rocky-9.6-x86_64-dvd image.

Mount ISO on RHEL 9:
```bash
lsblk
```
![alt text](./assets/1.1.png)  

```bash
mkdir /localrepo
mount /dev/sr0 /localrepo/
```
Checking if ISO is available:
```bash
df -h
```
![alt text](./assets/1.2.png)  
![alt text](./assets/1.3.png)  

Repository file configuration:  
```bash
vi /etc/yum.repos.d/rhel9.repo 
```
![alt text](./assets/1.4.png)  

Clearing the cache and refreshing the repository list:
```bash
dnf clean
dnf repolist
```
Checking the repository's functionality by installing the package:
```bash
dnf search httpd
```
Automatically mount the ISO on system startup. Add in file /etc/fstab:  
/dev/sr0 /localrepo/ iso9660 ro,loop,user 0 0 

# Question 3 
Web server configuration with welcome message on Server A

Checking the current firewall configuration:

```bash
firewall-cmd --list-all
```

Apache installation:
```bash
dnf install httpd -y
```

Starting and enabling the service:
```bash
systemctl start httpd
systemctl enable httpd
```

Creating a welcome page:
Adding a greeting - Hello!  
`# vi /var/www/html/index.html`  
```bash
Hello!
```
Permanently opening HTTP and HTTPS ports in the firewall and reloading:
```bash
firewall-cmd --add-service=http --permanent
firewall-cmd --add-service=https --permanent
firewall-cmd --reload
```
Test:  
![alt text](./assets/2.1.png)  

# Question 4 
Set the time zone to "Europe/Copenhagen" on Server A

Checking the current time zone:
```bash
timedatectl status
timedatectl set-timezone "Europe/Copenhagen"
```
# Question 5 
Configuring NTP time synchronization on Server A.

Editing the Chrony configuration file:
`vi /etc/chrony.conf`
```bash
server 0.pool.ntp.org iburs
```
![alt text](./assets/5.1.png)  
Enable NTP synchronization:
```bash
timedatectl set-ntp true
```
Restarting the chronyd service:
```bash
systemctl restart chronyd
```
Checking the NTP source:
```bash
systemctl restart chronyd
```

# Question 6 
Running a Wordpress container with Podman on Server A.

 Run a WordPress container in detached mode with the name "hello-wordpress" using podman. Mount the
 “/home/wordpress/var/www/html/” directory in the host to the “/var/www/html” directory in the podman container. Map TCP port 80 in the container to port 80 on the host.  

```bash
 dnf install container-tools -y
 podman search wordpress
 podman pull docker.io/library/wordpress
 podman tag docker.io/library/wordpress localhost/wordpress
 systemctl stop httpd 
 mkdir -p /home/wordpress/var/www/html
 podman run --name hello-wordpress -d -v /home/wordpress/var/www/html/:/var/www/html:Z -p 80:80 localhost/wordpress
 podman ps
 sudo firewall-cmd --list-ports
```
If there is a port:  
80/tcp

The firewall allows network traffic to port 80. If there is no port, then:
```bash
 firewall-cmd --add-port=80/tcp --permanent
 firewall-cmd --reload
 firewall-cmd --list-all
```

Check in your browser:  
![alt text](./assets/6.1.png)  

# Question 7

Reset the root password on Server B. Change it to "newpassword" to gain access to the system.  

In the boot menu, select the second option and press "e" to edit boot options.
![alt text](./assets/1.01.png)  

Adding rd.break. Save and restart - Ctrl + X  
![alt text](./assets/1.02.png)  

We mount and run the main partition. We set a new password. Create a hidden file named .autorelabel in the root directory. Exit and restart.  

```bash
mount -o remount rw /sysroot
chroot /sysroot
passwd root
touch /.autorelabel
exit
reboot
```


# Question 8 

On ServerB, do the following:
1. Create users Luke, Andrew, Mark and John.
    Luke and Andrew are members of the Innovation group.
    Mark and John are members of the Expert group.
2. Create shared group directories /groups/Innovation and /groups/Expert
3. Make the Innovation group the owner group of the /groups/Innovation directory, and the Expert group the owner group of the /groups/Expert directory.
4. Grant the groups that own the Innovation and Expert directories full access to these directories.
5. Others don't have access to the Innovation and Expert directories
6. New files created in Innovation and Expert directories belong to the group of which the directory is a member.
7. Members of The Expert group have read and execute permissions on the /groups/Innovation directory and all of its sub-directories and files.

```bash
useradd Luke && useradd Andrew && useradd Mark && useradd John
groupadd Innovation && groupadd Expert
usermod -aG Innovation Luke && usermod -aG Innovation Andrew
usermod -aG Expert Mark && usermod -aG Expert John
mkdir -p /groups/Innovation
mkdir -p /groups/Expert
chgrp Innovation /groups/Innovation
chgrp Expert /groups/Expert
chmod g+rwx /groups/Innovation && chmod g+rwx /groups/Expert
chmod o-x /groups/Innovation && chmod o-x /groups/Expert
chmod g+s /groups/Innovation && chmod g+s /groups/Expert
setfacl -Rm g:Expert:r-x /groups/Innovation
getfacl /groups/Innovation
```

# Question 9

On ServerB, set SELinux to "enforcing" mode.
```bash
getenforce
setenforce 1
```

# Question 10

Change the serverB hostname to rhel.server.com and make it persistent.

`vi /etc/hostname`  
and change line to: rhel.server.com

# Question 11

On rhel.server.com, add a new enviroment variable "VAR" with the value "RHCSA Prac Five" which will be available throughout the system, on both remote login session as well as local sessions for all users.

`vi /etc/bashrc`  
and add the line: export VAR="RHCSA Prac Five"  
```bash
source /etc/bashrc  
echo $VAR  
```

# Question 12

On rhel.server.com, configure a permanent storage for "journald" logs with 100 max use.

`vi /etc/systemd/journald.conf`  
uncomment and change line to: Storage=persistent  
and SystemMaxUse=100M
```bash
systemctl restart systemd-journald
reboot
ls /var/log/journal
```

# Question 13

On rhel.server.com, find all regular files in the "/etc" directory that were modified more than 90 days ago and redirect the output to /modified_etc_files.
```bash
find /etc -type f -mtime +90 > /modified_etc_files
```

# Question 14

On rhel.server.com, create a script named "/usr/local/bin/find.sh" to find all files under the directory "/etc" that are less than 4M and have SGID permissions set. Then save the list of found files to /root/4Mfiles.  
vi /usr/local/bin/find.sh

#!/bin/bash  
find /etc -size -4M -perm -g=s > /root/4Mfiles  

```bash
chmod a + x /usr/local/bin/find.sh
/usr/local/bin/find.sh
```
# Question 15

On rhel.server.com, find the word "error" in all files in the current directory and redirect the result to the file "/root.erros"

```bash
grep -r 'error' > /root/errors
```

# Question 16

On ServerA run the PostgreSQL database in a container with Podman and connect remotely.

Container launch:

```bash
podman run -d -p 5432:5432 -e POSTGRES_PASSWORD=postgres postgres
```
-d → works in the background  
-p 5432:5432 → exposes the PostgreSQL port to the system  
-e POSTGRES_PASSWORD=postgres → sets the password for the postgres user  
postgres → official database image  

You check:
```bash
firewall-cmd --list-ports
```
If there is a port:  
5432/tcp

The firewall allows network traffic to port 5432. If there is no port, then:  

```bash
firewall-cmd --add-port=5432/tcp --permanent
firewall-cmd --reload
```
--add-port → opens the port  
--permanent → saves it permanently    
--reload → activates the change immediately    

Remote connection to PostgreSQL database:

![alt text](./assets/16-1.png)  

# Question 17

 On ServerB, as root create a cron job that deletes empty files from /tmp at 12:30 am daily.  
 ```bash
crontab -e
```
Add the line:   
30 0 * * * find /tmp/ -type f -empty -delete // 0 is 12 am

# Question 18

On ServerB, optimize the system to run in a virtual machine for the best performance and concurrently tunes it for low
power consumption. Low power consumption is the priority.
 ```bash
dnf install tuned
systemctl start tuned
tuned-adm list
tuned-adm active
tuned-adm profile virtual-guest powersave
```
# Question 19

On ServerB, all new users should have a file name “YouDidIt” in their home folder after account creation.

 ```bash
echo "Congratulations!" > /etc/skel/YouDidIt
chmod 644 /etc/skel/YouDidIt
```
# Question 20

Schedule the task to run at 08:00 time on ServerA using the "at" command. You want to create a file named "example.sh" containing a command to create a file "example.txt" in your home directory.

Checking if at is installed:
 ```bash
rpm -q at
```
Install at:
 ```bash
sudo dnf install -y at
systemctl start atd
systemctl enable atd
```
Solving the task:
 ```bash
echo -e '#!/bin/bash\ntouch ~/example.txt' > ~/example.sh
chmod +x ~/example.sh
at 08:00
~/example.sh
# Ctrl + D
```

# Question 21

On ServerB, create a user peter with UID 1450 and expiry date 2026-12-31.  

 ```bash
useradd -u 1450 -e 2026-12-31 peter
chage -l peter
```

# Question 22

On ServerB, copy “/etc/hosts” file to the “/var/” directory with the name "nhosts", then do the following:  
User andrew can read, write, and execute the "nhosts" file.
User john can only read the "nhosts" file.

```bash
cp /etc/hosts /var/nhosts
```

Check if root is the owner:  
```bash
ls -l /var/nhosts
```
We set the basic settings:  
```bash
chmod 640 /var/nhosts
```
root: read/write  
group: read  
others: no access  

But this does NOT meet the requirements yet (because andrew and john are not included) → we will use ACL.  
  
user andrew: full rights (rwx)  
```bash
setfacl -m u:andrew:rwx /var/nhosts
```
user john: read only  
```bash
setfacl -m u:john:r-- /var/nhosts
```
ACL Explained:  
setfacl → sets Access Control List  
-m → modification  
u:andrews:rwx → user andrew has read/write/execute  
u:john:r-- → user john only has read

# Question 23

 On ServerB, write a script "/sum.sh" that can do the arithmetical operation by giving the sum of two integers entered by any user.

 ```bash
#!/bin/bash

read -p "Enter number 1: " num1
read -p "Enter number 2: " num2

if ! [[ "$num1" =~ ^-?[0-9]+$ && "$num2" =~ ^-?[0-9]+$ ]]; then
    echo "Error: Please enter valid integers"
    exit 1
fi

sum=$((num1 + num2))
echo "Suma: $sum"
 ```

Granting permissions:
```bash
chmod +x /sum.sh
```
Loading data from the user and displaying a message:  
read -p "Enter number 1: " num1  

Checking if these are integers:    

=~ ^-?[0-9]+$  

Script exits with error code:  
exit 1 

# Question 24

On ServerB, search the user frodo data in the “/etc/passwd” file and append the output in “/users/data”.

```bash
mkdir -p /users
grep "^frodo:" /etc/passwd | sudo tee -a /users/data
```

grep → searches for user frodo    
| → forwards the result  
sudo tee -a → writes to file as root  
-a → append (writing)

# Question 25

On ServerB, write a script named “/find_rf.sh” that prints out a list of files owned by root and with the SUID bit set in /usr.

```bash
#!/bin/bash

find /usr -type f -user root -perm -4000 2>/dev/null
```

-type f → only regular files (not directories)    
-user root → files belonging to the root user  
-perm -4000 → files with SUID - 4000 set can be executed as root even if run by a regular user  
2>/dev/null → hides errors  


# Question 26

On ServerB, perform the following tasks:  

1. Grant execute permission to the file owner.
2. Revoke read and write permissions for both group and other users.

Use the file /home/$USER/myFile, which currently has the permissions rw-r--r--.

Execute the following commands:

```bash
touch /home/$USER/myFile
ls -l /home/$USER
stat -c %a /home/$USER/myFile
chmod u+x /home/$USER/myFile
chmod go-rw /home/$USER/myFile
ls -l /home/$USER
```


touch /home/$USER/myFile → Creates a file.  
ls -l /home/$USER → Displays detailed information about files, including their permissions.  
stat -c %a /home/$USER/myFile → Shows permissions in numeric (octal) format, e.g. 644.  
chmod u+x → Adds execute permission (x) for owner (u).  
chmod go-rw → Removes (-) read (r) and write (w) permissions.  


The end result

Permissions will change from:  
```bash
rw-r--r--  (644)
```
to:  
```bash
rwx------  (700)
```