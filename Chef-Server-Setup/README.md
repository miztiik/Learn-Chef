# Setup Chef Server


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

```sh
yum install crontabs ntp -y && yum update -y

```

### 