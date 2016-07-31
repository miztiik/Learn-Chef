# Setup Chef Server

## Pre-Requisites

 - Official Chef [Pre-Req](https://docs.chef.io/install_server_pre.html)

### Disable SELinux
The Chef server requires SELinux must be disabled or set to Permissive mode as Chef server does not have a profile available to run under SELinux

```sh
setenforce Permissive
```

### Disable Apache Qpid
The Chef server uses RabbitMQ for messaging. Apache Qpid and RabbitMQ share the same protocol, Apache Qpid must be disabled.

```sh
service qpidd stop
```

### Install Daemons
 - Crontabs - Periodic maintenance tasks are performed on the Chef server servers via cron and the /etc/cron.d directory.
 - NTP - The Chef server requires that the systems on which it is running be connected to Network Time Protocol (NTP), as the Chef server is particularly sensitive to clock drift

```sh
yum install crontabs ntp -y && yum update -y
chkconfig ntpd on

```
### FQDN

Ensure your Chef server is reachable through it FQDN


### User & Group Accounts
By default, accounts required by the Chef server are created during setup. If your environment has restrictions on the creation of local user and group accounts that will prevent these accounts from being created automatically during setup, you will need to create these accounts.

## Download and Install the Chef Server software

 - [Official Installation Instructions](https://docs.chef.io/install_server.html#standalone)
 
### 
	
```sh
curl -L https://omnitruck.chef.io/install.sh | sudo bash -s -- -P chef-server
```