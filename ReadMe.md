# Hosted Chef-Server Preparation
 - Create account in chef from manage.chef.io website. 
 - Create a organization

## Create EC2 Instances to act as nodes
- Create two instances,
 - One for workstation and 
 - Another one to act as the remote node on AWS account
- The security groups should have the following ports open, `22, 80, 443`
 
## Configure Chef Workstation
- Enable rootlogin and password authentication in /etc/ssh/sshd_config file.

	```sh
	sed -ri 's/#   PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/ssh_config
	echo "PermitRootLogin yes" >> /etc/ssh/ssh_config
	yum install unzip -y
	```
- Download Chef starter kit from hosted Chef-Server
 - Copy starter kit into workstation and unzip in /root directory. It generates chef-repo
 
	```sh
	cd /chef-repo
	curl -L https://www.opscode.com/chef/install.sh | bash
	```
 - Validate your connection to the Chef server, One way to do that is to run the `knife ssl check` command. 
 
- We have configured our Chef Server(hosted in chef site) and Chef workstation, Authentication is established by the `Starter-Kit`. 

## `bootstrap` the Chef node[s]
> We call the process that installs `chef-client` on a node and the initial checkin to the Chef server _bootstrapping_ the node.

#### Bootstrap using key-based authentication
- Lets tag our node as `chef-node01`. _I used the same tag in EC2 as well to remember it easily_.
- Copy the EC2 key to your workstation to `./chef/chef-node-key.pem`

```sh
knife bootstrap 54.211.19.162 --ssh-user ec2-user --sudo --identity-file /chef-repo/.chef/chef-node-key.pem --node-name chef-node01
```

- Confirm the list of nodes

```sh
knife node list
or
knife node show chef-node01
```
	
- Login to chef node and edit /etc/chef/client.rb file add below line
	```log_level	:info ```


# Create cookbook `learn-httpd`

```sh
knife cookbook create learn-httpd
```
- Go inside directory cookbooks/learn-httpd/recipes 
```sh	
- cd cookbooks/learn-httpd/recipes
```
- Edit default.rb file add below code to install httpd package.
```sh
package "httpd"

service "httpd" do
 action [:enable, :start]
end

file '/var/www/html/index.html' do
 content '<h1> I am on top of the world</h1>'
end
```

- Upload this new code to your chef server by using below command

```sh
knife cookbook upload learn-httpd
```

 - _If you want to double-check your cook books `knife cookbook list`

- Now go to your chef node and run chef-client to get latest cookbooks from servers.
```sh
chef-client
```
_Note: even though our code is currect. It cant install apache. Because we haven’t set this node to run recipe. To do that we should run below command._

```sh
knife node run_list add chef-node01 "recipe[learn-httpd]"
```

# Clean up your environment

### Delete the node from the Chef server

```sh
knife node delete chef-node01 --yes
```
