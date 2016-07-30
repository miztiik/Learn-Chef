# Hosted Chef-Server Preparation
 - Create account in chef from manage.chef.io website. 
 - Create a organization

# Create EC2 Instances to act as nodes
- Create two instances,
 - One for workstation and 
 - Another one to act as the remote node on AWS account
- Enable rootlogin and password authentication in /etc/ssh/sshd_config file.
	```sh
	sed -ri 's/#   PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/ssh_config
	echo "PermitRootLogin yes" >> /etc/ssh/ssh_config
	yum install unzip -y
	```
- The security groups should have the following ports open, `22, 80, 443`
- Download Chef starter kit from Administration-starter kit
- Copy starter kit into workstation and unzip in /root directory. It generates chef-repo
- Download and install chef software with below command
	```sh
	curl -L https://www.opscode.com/chef/install.sh | bash
	```
- As of now we have configured Chef Server(hosted in chef site) and Chef workstation. Authentication files between chef server and chef workstation copied through starter ket.  Now we should copies these authentication files from chef workstation to chef node. It helps chef node to communicate with chef-server. It should run from /root/chef-repo path. 
	```sh
	knife bootstrap <server IP/ Hostname> -x root -P ravis829 -N <Tag>
	```
- Login to chef node and edit /etc/chef/client.rb file add below line
	```sh log_level	:info ```


# Creating apache cookbook
- Knife cookbook create apache
- Go inside directory cookbooks/apache/recipes 
```sh	
- cd cookbooks/apache/recipes
```
- Edit default.rb file add below code to install httpd package.
```sh
package "httpd" do
 action :install
end
```
- Upload this new code to your chef server by using below command
```sh
Knife cookbook upload apache
```
- Now go to node and run chef-client to get latest cookbooks from servers.
```sh
Chef-client
```
_Note: even though our code is currect. It cant install apache. Because we haven’t set this node to run recipe. To do that we should run below command._
```sh
knife node run_list add valaxynode "recipe[apache]"
```
