# How-To setup Chef to manage your servers.
This tutorial will assist you in setting up a `Chef Server` - hosted at Chef.io , `Chef Workstation` & `Chef Nodes` running on AWS as EC2 Instances. 

# Hosted Chef Server Preparation
 - Create account in chef from manage.chef.io website. 
 - Create a organization

## Create EC2 Instances to act as nodes
- Create two instances,
 - One for workstation and 
 - Another one to act as the remote node on AWS account
- The security groups should have the following ports open, `22, 80, 443`
 
# Configure Chef Workstation
- Download Chef starter kit from hosted Chef Server
 - SCP the starter kit into your workstation and unzip in /root directory. It generates `/chef-repo`.
 
	```sh
	yum install unzip -y
	cd /
	unzip chef-starter.zip
	cd /chef-repo
	curl -L https://www.opscode.com/chef/install.sh | bash
	```
 - Validate the SSL certificate & authenticate calls to Chef Server `knife ssl check`
 - Validate your connection to the Chef server, One way to do that is to run the `knife client list` command. 
 
- We have configured our Chef Server(hosted in chef site) and Chef workstation, Authentication is established by the `Starter-Kit`. 

# Chef Node[s] `bootstrapping` 
> We call the process that installs `chef-client` on a node and the initial checkin to the Chef server _bootstrapping_ the node.

#### Bootstrap using key-based authentication
- Lets tag our node as `chef-node01`. _I used the same tag in EC2 as well to remember it easily_.
- Copy the EC2 key to your workstation to `./chef/chef-node-key.pem`

```sh
knife bootstrap 54.211.19.162 \
                --ssh-user ec2-user --sudo \
				--identity-file /chef-repo/.chef/chef-node-key.pem \
				--node-name chef-node01
```

- To confirm the list of nodes registered to the Chef-Server

```sh
knife node list
or
knife node show chef-node01
```
	
- Login to chef node and edit `/etc/chef/client.rb` file add below line
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

 - _If you want to double-check your cook books `knife cookbook list`_

- Now go to your chef node and run chef-client to get latest cookbooks from servers.
```sh
chef-client
```
 - _Note: even though our code is currect. It cant install apache. Because we haven’t set this node to run recipes. To do that we should run below command._

```sh
knife node run_list add chef-node01 "recipe[learn-httpd]"
```
 
 - Run `chef-client` from your node to run the recipe.

- Load the webserver page
```sh
curl localhost
or
curl <your_public_ip>
```

# Clean up your environment

### Delete the node from the Chef server

```sh
knife node delete chef-node01 --yes
```
