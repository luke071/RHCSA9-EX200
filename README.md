This repository will provide solutions to the Red Hat Certified Systems Administrator (RHCSA EX200 ) exam preparation tasks. Tasks will be executed using Rocky Linux 9 systems in an Oracle VirtualBox environment.

# Task 1 Configuring a local DNF repository on the Server using the Rocky-9.6-x86_64-dvd image.

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
dnf install nano 
```