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

## Install & Configure the Chef Server

 - [Official Installation Instructions](https://docs.chef.io/install_server.html#standalone)
 
### Download & Install Chef server.
Run this command to install Chef server. This will install the base Chef system onto the server. If you have selected a server with less powerful hardware than the recommended amount, this step may fail.

```sh
curl -L https://omnitruck.chef.io/install.sh | sudo bash -s -- -P chef-server
```


_Alternatively, you can [download and install the package manually](https://downloads.chef.io/chef-server/)._


### Configure & Start Chef Services
Call the `reconfigure` command, which configures the components that make up the server to work together in your specific environment & start all of the services. This step may take a few minutes to complete.

```sh
chef-server-ctl reconfigure
```

### Create an Admin User and Organization
|               Item | Description                             | Notes and examples                 |
|:------------------:|:---------------------------------------:|:----------------------------------:|
|  `ADMIN_USER_NAME` | user name for the administrator account | `mystique`, `admin`                |
|   `ADMIN_PASSWORD` | password for the administrator account  | `ExampleTempPassKey`                           |
| `ADMIN_FIRST_NAME` | administrator's first name              | `Poseidon`                         |
|  `ADMIN_LAST_NAME` | administrator's last name               | `Trident`                         |
|      `ADMIN_EMAIL` | administrator's email address           | `13oct08@quantumfoam.uni.me`       |
|    `ORG_FULL_NAME` | your organization's full name           | `Mystique Corp.`                   |
|   `ORG_SHORT_NAME` | your organization's abbreviated name    | `miztiik`                         |


```sh
chef-server-ctl user-create mystique Poseidon Trident 13oct08@quantumfoam.uni.me ExampleTempPassKey --filename mystique.pem
```

### Create the organization

```sh
chef-server-ctl org-create miztiik "Mystique Corp." --association_user mystique
```

### Install the management console and reporting feature

Run these commands on your Chef server to install the management console,

```sh
chef-server-ctl install chef-manage
chef-server-ctl reconfigure
chef-manage-ctl reconfigure
```

Then run these commands to install the reporting feature,

```sh
sudo chef-server-ctl install opscode-reporting
sudo chef-server-ctl reconfigure
sudo opscode-reporting-ctl reconfigure
```


























