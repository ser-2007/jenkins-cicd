# Ansible ELK Playbook
 
With this playbooks we could install the ELK tools to the ubuntu based machines.


The Ansible control node is the machine we’ll use to connect to and control the Ansible hosts over SSH. Your Ansible control node can either be your local machine or a server dedicated to running Ansible, though this guide assumes your control node is an Ubuntu 20.04 system

# Step 1 — Installing Ansible

First you must be sure there is an Ansible is installed on your system.

first you must install ansible to your control node - manager node - control machine 


$ sudo apt-add-repository ppa:ansible/ansible

$ sudo apt update

$ sudo apt-get install ansible

ansible -version

# Step 2 — Setting Up the Inventory File

The inventory file contains information about the hosts you’ll manage with Ansible. You can include anywhere from one to several hundred servers in your inventory file, and hosts can be organized into groups and subgroups.

sudo nano /etc/ansible/hosts

/etc/ansible/hosts

[webservers]
server1 ansible_host=203.0.113.111
server2 ansible_host=203.0.113.112
server3 ansible_host=203.0.113.113

[all:vars]
ansible_python_interpreter=/usr/bin/python3



# SSH Connection to the other machines
first you must be sure ssh is installed on your machine

sudo apt install openssh-server

Once the installation is complete, the SSH service will start automatically. You can verify that SSH is running by typing:

sudo systemctl status ssh

Ubuntu ships with a firewall configuration tool called UFW. If the firewall is enabled on your system, make sure to open the SSH port:

sudo ufw allow ssh

After allowing SSH, it is time to enable the firewall as well. To check if it is enabled or not, the command for checking the status of UFW is given below

sudo ufw status

If it is inactive and the case is the same for you, then you need to enable it by typing the command 

sudo ufw enable


After enabling the UFW, check the status again


sudo ufw status

if it is active,  SSH port 22 is opened.

After this configuration, you can log in to this machine from any remote machine via SSH.


first you create a private key or if you have you address it in the ansible configuration

And You might add this to ansible.conf file
sudo nano /etc/ansible/ansible.conf

[defaults]
private_key_file=/path/to/keyfile.pem

# Creating the RSA Key Pair


first step is to create a key pair on the client machine
ssh-keygen

After entering the command, you should see the following output:

Output
Generating public/private rsa key pair.
Enter file in which to save the key (/your_home/.ssh/id_rsa):
Press ENTER to save the key pair into the .ssh/ subdirectory in your home directory, or specify an alternate path.

If you had previously generated an SSH key pair, you may see the following prompt:

Output
/home/your_home/.ssh/id_rsa already exists.
Overwrite (y/n)?
If you choose to overwrite the key on disk, you will not be able to authenticate using the previous key anymore. Be very careful when selecting yes, as this is a destructive process that cannot be reversed.

You should then see the following prompt:

# Output
Enter passphrase (empty for no passphrase):


* Copying the Public Key to Your Ubuntu Server

ssh-copy-id username@remote_host


after that you can connect to your worker node with ssh connect

ssh username@remote_host



NOW TESTING ANSIBLE

ansible -i hosts (specifize the host path if it is not the same directory) -m ping all (or one server name how you deined in hosts)

In our issue; 
[webservers]
server1 ansible_host=203.0.113.111
server2 ansible_host=203.0.113.112
server3 ansible_host=203.0.113.113

we can do this : 

ansible webservers -m ping

or 

ansible -i hosts -m ping webservers


## Notes and requirements

 - The playbook was built and tested on Ubuntu 16.04 VMs, for ELK versions 5.x 
 - You will need Ansible installed and running
 - Playbook is currently configured to set up the ELK stack together with Metricbeat for server perf monitoring. There is a role for Filebeat as well. You just need to add the Filebeat role to your [site.yml] file.
 
 ## Instructions
 
 1. Edit your Ansible hosts file ('/etc/ansible/hosts') and add an 'elkservers' entry for the server you wish to install ELK on. 
 
 2. Verify connectivity to the ELK server.
 
 3. In the terminal on the machine hosting Ansible, clone this repo.
 
 4. Cd into the directory, and run:
 `ansible-playbook site.yml`
 
 The plays in the playbook will run on the target server, installing ELK and the specified beats shippers. 
 

