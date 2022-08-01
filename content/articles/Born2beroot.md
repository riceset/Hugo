---
title: "Born2beRoot Project"
date:   2022-07-17
tags: [42Tokyo]
---

Written by: 米野チアゴ (tkomeno) with the help of 飯島康太 (kiijima) from 42Tokyo.

### How a virtual machine works:

Virtual Machines take advantage of a technology called **Virtualization** which takes some portion of the hardware resources and gives it to the guest OS.

### Why Debian?

At first, Debian is easier to use than CentOS. We’ve also heard that CentOS will be discontinued so we thought Debian would be the smarter choice.

### Some of the differences between CentOS and Debian

| CentOS | Debian |
| --- | --- |
| Is supported by Red Hat | Is supported by individuals |
| Does not have multiple architectures | Has multiple architectures |
| Stable updates but that take a lot of time | Has a release cycle of 2 years |
| Not that user-friendly | Is user-friendly |
| Uses YUM as their package manager which has some limited packages | Uses apt as their package manager which has a vast amount of packages on their built-in repository |

### The purpose of virtual machines

The main purpose differentiates between personal use and enterprise use.

If you’re a regular user, you can use a virtual machine to try things out like a program or new versions of a certain OS. You can even use it as a quarantined OS as it is separated from the host OS by nature (in most cases)

If you’re a company that offers servers, for example, you can save up a lot of money by getting a mainframe-like computer and separating it virtually into many computers. In this way, you can save not only money but also energy and physical space.

### The difference between aptitude and apt

Aptitude is a high-level package manager compared to apt.

![aptget-aptitude](/aptget-aptitude.png)

### What is AppArmor in short?

AppArmor is a Linux security module that allows the system admin to restrict programs with per-program profiles. For example, restricting network access and rwx permissions.

## Simple Setup

### Checking UFW status:

```bash
$ systemctl status ufw
```

### Checking SSH status:

```bash
$ systemctl status ssh
```

### Checking the user groups:

```bash
$ groups USERNAME
```

### Checking all of the groups:

```jsx
$ cat /etc/groups
```

### Checking the user:

```jsx
$ cat -d: -f1 /etc/passwd
```

### Adding a user:

```bash
$ sudo adduser USERNAME
```

### Adding groups:

```jsx
$ sudo groupadd GROUPNAME USERNAME
```

### Adding a user to a group:

```jsx
$ sudo usermod -aG GROUPNAME cut -d
```

## Hostname and **partition**

### Changing the hostname:

```jsx
$ sudo hostnamectl set-hostname HOSTNAME
```

### Changing the /etc/hosts file:

```jsx
$ sudo vim /etc/hosts
```

### Changing the old_hostname with the new_hostname:

```jsx
127.0.0.1       localhost
127.0.0.1       NEW_HOSTNAME
```

### What is LVM?

The official name is Logical Volume management multiple partitioned logical volumes to be treated as one.

### Checking expired passwords:

```jsx
sudo chage -l USERNAME
```

### Checking the details of the password:

```jsx
sudo vim /etc/pam.d/common-password
```

### Checking cron information:

```jsx
sudo crontab -u root -e
```
