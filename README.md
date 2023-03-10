Ansible 
=========
- Tools to automate IT tasks like 
    - Installing Software on multiple servers
    - Downloading Application dependencies to servers
    - etc..
- Ansible supporting all infra whether os level or cloud level.
- Ansible is agentless - Install ansible once on a single machine and through that machine you can configure multiple server without install required software agent on those machines.

- Ansible works with modules - modules are small programs that do the actual work and these are granular and specific.
- Ansible has lots of modules which helps the developer to automate things.
- Ansible uses YAML - Yet Another MarkUp Lang
- Ansible Playbook - Playbook describes How, Where, which modules and In which order plays (tasks/multiple tasks) to be executed. 
- A single playbook yaml file can have 1 or Multiple plays.

- Ansible Inventory List/ Hosts File - Inventory List or Hosts File is a file where we maintained all the machines data like IP address So that In plays we can defines on which machines the play should be executed.
- This file can be maintained in diff ways like - present in - /etc/ansible/hosts
    ```yaml
    # -------- Only Ip Addresss --------
        10.24.0.100
        20.0.40.12
    # -------- Ip Addresss with groups --------
        [webserver]
        10.24.0.100
        20.0.40.12
    # -------- hostname with groups --------
        [webserver]
        web1.server.com
        web2.server.com
    # -------- hostname with groups and associated group variable --------
        [webserver]
        web1.server.com
        web2.server.com
        [webserver:vars]
        ansible_port=8020
        ansible_user=root
    ```
Ansible Use-Cases
===================
- Ansible for Docker -
    - When we creating Docker Container for an Application - Dockerfile is used to preparing the application environment like app.jar, config file, dir creation and etc..
    - Like Dockerfile, We can create an ansible playbook which will use the docker module to create same functionality for an application but here we can deploy this application on any type of environment whether its docker container, vagrant container, cloud instance or bare metal.
    - Through the ansible playbook we can manage the docker container and the host as well where docker container is deployed, which gives more power than just deploy application through dockerfile.

Competitors of Ansible
=======================
- Chef and Puppet are the other one which perform the same work but ansible has better reach among them.
- Here is the diff -
    1. Ansible works with simple yaml but Chef and Puppet use Ruby which is another lang to learn.
    2. Ansible is agentless and Chef and Puppet use agent to perform managing updates on target servers.
    3. Ansible use push base mechanism - apply the required configuration directly on target server without knowing the current config
        whereas Chef and Puppet are Pull based mechanish - To managed servers they required an agent to pull the configuration first of the target server then compare, if desired state != current state. It apply the configuration.

Ansible Configuration for test practice
========================================
1. Create 5 ec2-servers 
    - 1 is Ansible Controller
    - 2 is Ansible Node/Host - Ubuntu
    - 2 is Ansible Node/Host - Amazon-Linux

2. Allowing passwordless ssh connection to all Nodes/host. Follow these steps on all nodes.
    ```
        ------- Generate Password for ubuntu user --------
        sudo passwd ubuntu => Give password as same "ubuntu"
        -------- Allow Password Base authentication --------
        Open file - "sudo vim /etc/ssh/sshd_config"
        Search and Change line "PasswordAuthentication yes"
        Save & Quit
        ------ Restart the ssh ------
        for ubuntu - sudo service ssh restart
        for amazon-linux - sudo systemctl restart sshd
    ```

3. Go To Controller Machine, Generate ssh key and add it to the multiple nodes/host.
    ```
        --------- Generate SSH Key ----------
        ssh-keygen
        --------- Copy controller ssh public key to other nodes ------
        ssh-copy-id <user@private-ip> -> ssh-copy-id ubuntu@172.31.11.38
    ```

4. Install Ansible on Controller.
5. Add the IP address and ansible default vars to overwrite variable value of ansible nodes to ansible controller in default inventory file or new host file 
    ```
        ---------- Create a file "hosts" ---------
        [ubuntu-server]
        3.84.24.76
        [ubuntu-server:vars]
        ansible_user=ubuntu
        ansible_python_interpreter=/usr/bin/python3
        [amazon-server]
        3.84.24.76
        [amazon-server:vars]
        ansible_user=ec2-user
        ansible_python_interpreter=/usr/bin/python3
    ```
6. Ansible adhoc command to check the server is up
    ```
        ------ Adhoc command pattern ----------
        Pattern - ansible [all/ip/group from inventory file] -i [inventory file name other than default] -m [module] -a [arguement]
        ansible all -i hosts -m ping
    ```

Ansible Playbook
=================
- playbook-1.yaml 
- playbook-2.yaml

- Ref Folder - Ansible-Learning

Ansible Modules
=================
- Ref - https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html
- But In ansible version 2.10 or later, there is no concept of modules they are divided in 2 cases -
    - ansible base - this repo contains the core ansible programs
    - collection - modules and plugins move into various collections
- Collection 
    - A packaging format for bundling and distributing Ansible content.
    - It can be released and installed independent of other collections.
    - All modules are part of collections.
- Collection Ref - https://docs.ansible.com/ansible/latest/collections/index.html

- To work with ansible 2.10 or later version
    - By default, it comes with some default collections.
    - we can able to import modules and plugin to the playbooks as it comes with some collection.
    - List the collection `ansible-galaxy collection list`
    - If need to update the collection use `ansible-galaxy collection install <collection-name>`

- We can create our own collections 
    - For Bigger Ansible projects
    - Collections follow a simple ata structure.
    - Required - A galaxy.yml file (containing metadata) at the root level of collection

Ansible Projects
=================

Download acl package on remote server using `apt install acl`
which allows the priviliged become permission to created user in ansible playbook

## Automate Node App 
1. Create a server and do ssh configuration
2. Write Ansible Playbook
    - Install node and npm on server
    - Copy node artifact and unpack
    - Start Application
    - Verify App Running succesfully
3. 3 Ways we can provide the value to variable in the playbook
    - Inside the Playbook
    - Refrence the outside variable file to the playbook
    - Using ansible cli `ansible-playbook playbook-3.yaml -e "version=1.0.0 location=/home/ubuntu"`
4. project-vars file used to provide variable value from outside the playbook

- Ref - nodejs-app-1.0.0.tgz as zip file to deploy node app
- Ref - Ansible-Learning/playbook-3.yaml

## Automate Nexus Deployment 
1. Create a server and do ssh configuration
2. Write Ansible Playbook
    - Download Nexus Binary and unpack
    - Run Nexus application using Nexus user
    - Start Nexus Application
    - Verify Nexus Running succesfully
3. Use the register module to store the data of a task and use it in another task.
4. Use the find module to find the nexus folder and use Shell module to rename.
5. Use the when moduel to apply condition on renaming folder.
6. Use user and group module to create nexus user and group.
7. Use blockinfile module to add multiple lines in a file. It won't replace the existing same line, it will just add the lines.
8. Use lineinfile module to replace the existing line.

- Ref - nexus.sh file is consider for creating playbook-4.yaml to deploy nexus using ansible
- Ref - Ansible-Learning/playbook-4.yaml

## Automate Docker Deployment with Ansible
1. Create a server with amazon linux and do ssh configuration
2. Write Ansible Playbook
    - Download Python3, Docker and Docker-Compose
    - Start Docker Container to Run Applications

- Ref - Ansible-Learning/playbook-5.yaml

## Automate Docker Deployment with Ansible integrated with terraform (SELF)
1. Create a server with amazon linux using terraform. Pick One from below - provisioner/null_resource
    - Using Provisioner
    ```yaml
        # Inside the ec2-creation resource block add the provisioner block.
        # Add private_ssh_key variable in the variable.tf file. and pass the value of private ssh key location
        provisioner "local_exec" {
            working_dir = "directory where ansible playbook present"
            command: "ansible playbook --inventory ${self.public_ip}, --private-key ${var.private_ssh_key} --user ec2-user <playbook name>"
        }
    ```
    - Using null resource
    ```yaml
        # After ec2-creation resource block use null resource.
        # Change the Reference of inventory with your ec2-server resource reference.
        # Add private_ssh_key variable in the variable.tf file. and pass the value of private ssh key location.
        resource "null_resource" "configure_server" {
            trigger = {
                trigger = aws_instance.myapp-server.public_ip
            }
            provisioner "local-exec" {
                working_dir = "directory where ansible playbook present"
                command: "ansible playbook --inventory ${aws_instance.myapp-server.public_ip}, --private-key ${var.private_ssh_key} --user ec2-user <playbook name>"
            }
        }
    ```
2. Write Ansible Playbook
    - Download Python3, Docker and Docker-Compose
    - Start Docker Container to Run Applications
3. Change the hosts value to all inside the playbook-5.yaml
4. At the begining of playbook-5.yaml, Add a play to check if ec2-server is up and running 
    ```yaml
    - name: Wait for ssh connection
      hosts: all
      gather_facts: False
      tasks:
        - name: Ensure ssh port open
          vars:
            ansible_connection: local
            ansible_python_interpreter: /usr/bin/python
          wait_for:
            port: 22
            delay: 10
            timeout: 100
            search_regex: OpenSSH
            host: {{ (ansible_ssh_host|default(ansible_host))|default(inventory_hostname) }}
    ```

- Ref - Terraform Repository On GitHub
- Ref - Ansible-Learning/playbook-5.yaml

## Automate Dynamic Inventory 
- Managing an inventory, which fluctuates over time.
- Hosts creation and deletion all the time.
- eg - auto-scaling to accomodate for business demands
So, Hard-Coding the IP addresses isn't good idea. We wanna dynamically set them in the ansible host file.

- Project Workflow 
    - Create 3 EC2 Instance with Terraform 
    - Connect to these servers with Ansible without hardcoding the IP addresses
        ```yaml
        # To get the ip of recently created servers, we need to connect the aws using ansible and fetch the ip.
        # Inventory Script/Inventory Plugins - Inventory Plugins are the recommeded options.
        - Enable Inventory Plugin in ansible.cfg "enable_plugins = amazon.aws.aws_ec2".
        - Create a "dynamic_inventory_aws_ec2.yaml" file to fetch hosts dynamically.
        - Add this code inside that "dynamic_inventory_aws_ec2.yaml" file.
            ---
            plugin: aws_ec2
            regions: 
            - us-east-1
            keyed_groups:
            - key: tags
                prefix: tag
            - key: instance_type
                prefix: instance_type
        - To check this plugin working, run command "ansible-inventory -i <file name> --graph"
        - It will return the public dns to connect from anywhere.
        - If you getting private dns then you need to run ansible automation inside the same vpc.
        ```
    - Use the dynamic invetory ansible host file for ansible automation by ref in ansible-playbook
        ```
        - Replace hosts in playbook-6 with "@aws_ec2" this group is by default getting through plugins. 
        ```
    - Configure private ssh key and user globally.
        ```
        # In ansible.cfg - add these params also
        remote_user = ec2-user
        private_key_file = ~/.ssh/id_rsa
        ```
    - Now Run the ansible playbook `ansible-playbook -i dynamic_inventory_aws_ec2.yaml playbook-6.yaml`
    - or replace the inventory file inside ansible.cfg from hosts to dynamic_inventory_aws_ec2.yaml

- Ref - Create your own tf file to create ec2 
- Ref - playbook-6.yaml
- Ref - ansible.cfg
- Ref - dynamic_inventory_aws_ec2.yaml

## Automate K8s Deployment 
- Create K8s Cluster on AWS using TF file
- Configure Ansible to connect to EKS Cluster
    ```yaml
    - Create a new yaml file "deploy-to-k8s.yaml"
    - Add this code 
    ---
        - name: Deploy app in new namespace
        hosts: localhost
        tasks:
            - name: Create a k8s namespace
            community.kubernetes.k8s:
                name: my-app
                api_version: v1
                kind: Namespace
                state: present
            - name: Deploy nginx app 
            community.kubernetes.k8s:
                src: 
                state: present
                namespace: my-app
    ```
- Deploy Deployment and Service Component
    ```
    - Create a new nginx conf file "nginx.k8.yaml"
    - Add kubernetes conf in that file
    - Ref to "nginx.k8.yaml"
    ```
- Run the ansible playbook

- Ref - Terraform-Learning/eks-deployment
- Ref   playbook-7.yaml
- Ref - kubeconfig file if another location then "~/.kube/config"
- Ref - nginx.k8.yaml

## Ansible Roles
- Ansible Roles helps in grouping your content in roles
- It Break Up large playbooks into smaller manageable files
- Ansible Roles Break Up in 
    - Tasks that the role executes - tasks/main.yaml
    - Static File that the role deploys - files/my-file.yaml
    - Default Variable for tasks - default/main.yaml , vars/main.yaml
    - Custom Modules which are used within role - library/my_module.py
- You can get predefined roles from community like -
    - Ansible Galaxy
    - Git Repository

### How to Create and Use Ansible roles by restructuring the "playbook-5.yaml"

1. Create a folder roles and inside this folder create two another folders with name create_user, start_containers
2. Inside these folders (create_user, start_containers), there is a min a single folder required with name tasks.
3. Put the tasks inside these main.yaml file respectively.
4. Ref the folder name to the playbook you want to use the role. 'deploy-docker-with-roles.yaml'
5. Create a folder name "files" inside "start_containers" and put docker-compose.yaml file there.
6. Create two folder defaults and vars inside "start_containers"
7. Inside Vars/main.yaml add key pair which you want to parameterized. and its default value should be in default/main.yaml
8. Parameterized the key value inside tasks/main.yaml by ref their key


Take Ref - Ansible-Learning/roles
         - deploy-docker-with-roles.yaml

### ansible.cfg file can be helpful in multiple projects where you can define your default behaviour to use by ansible.
