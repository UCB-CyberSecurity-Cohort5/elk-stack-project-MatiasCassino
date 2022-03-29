# ELK-Stack-Project
Project 1 - ELK Stack Project

## Fill in this as your project documentation

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.

What aspect of security do load balancers protect? What is the advantage of a jump box?

-Load Balancing plays an important security role as computing moves evermore to the cloud. The off-loading function of a load balancer defends an organization against distributed denial-of-service (DDoS) attacks.
It does this by shifting attack traffic from the corporate server to a public cloud provider. DDoS attacks represent a large portion of cybercrime as their number and size continues to rise.
Hardware defense, such as a perimeter firewall, can be costly and require significant maintenance. 
Software load balancers with cloud offload provide efficient and cost-effective protection.

-Over the last few years, with malicious hackers and malware infesting nearly every enterprise network at will,
security admins have been looking for a way to decrease the ability of hackers or their malware creations to steal admin credentials and
take over an environment and the concept of a traditional “jump box” has morphed into an even more comprehensive and locked-down “secure admin workstation” (or SAW).

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.

What does Filebeat watch for? 
-Filebeat will collect log events and gather data about the file system.

What does Metricbeat record? 
-Metricbeat will be used to monitor and collect metrics from the system and services running on each server.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| name            |function                                   |IP Add                       | system             |
|-----------------|-------------------------------------------|-----------------------------| ------------------ | 
|local workstation| configurating network externally          |108.228.25.229               | Windows home 10    |
|jump box         |  gateway                                  |10.0.0.4                     | Linux ubuntu 18.04 |
|elk-Net          | collect and process data from Web Vms's   |10.1.0.4                     | Linux ubuntu 18.04 |
| web1            | process and deliver web content to user   |10.0.0.15                    | Linux ubuntu 18.04 |
| web2            | process and deliver web content to user   |10.0.0.16                    | Linux ubuntu 18.04 |
| web3            | process and deliver web content to user   |10.0.0.9                     | Linux ubuntu 18.04 |
|load balancer    | distribute traffic to backened server pool|      104.209.38.126         |      N/A           | 
                                                              |    |          |       |     | 
                                                              |10.0.0.15/10.0.0.16/10.0.0.9/|

### Access Policies

The machines on the internal network are not exposed to the public Internet. 
Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

-The local workstation with the ip 108.228.25.229 has been whitelisted. 
This IP has permission to access the JumpBox through port 22 via ssh. 

Machines within the network can only be accessed by JumpBox.
Which machine did you allow to access your ELK VM? What was its IP address?

-JumpBox IP 10.0.0.4 can access Elk-Net 10.1.0.4 via ssh on port 22
The local workstation IP 108.228.25.229 can access Elk-Net via TCP on port5601 


A summary of the access policies in place can be found in the table below.

| Name              | Publicly Accessible             | Allowed IP Addresses                                     |
|-------------------|---------------------------------|----------------------------------------------------------|
| Jump Box          | No                              | 108.228.25.229 via SSH port 22                           |
| Elk-Net           | No                              | 10.0.0.4 via SSH port 22/108.228.25.229 via TCP port 5601|
| Web1              | No                              | 10.0.0.4 Docker container via SSH port 22                |
| Web2              | No                              | 10.0.0.4 Docker container via SSH port 22                |
| Web3              | No                              | 10.0.0.4 Docker container via SSH port 22                |   
| Load balancer     | No                              | 108.228.25.229 via TCP port 8080                         |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
What is the main advantage of automating configuration with Ansible?

-Ansible was used to automate configuration of the ELK machine.
No configuration was performed manually, which is advantageous because manual configuration is time consuming and can lead to human error.
Automated configuration through the Ansible provisioning tool provides greater efficiency, as several machines can be figured at the same time which will ensure that all machines deployed and running the provisional script will be identical to one another.


The playbook implements the following tasks:
In bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._

-The first step in the playbook specifies the remote user as RedAdmin and indicates that the hosts in the “elk” group of machines will be configured.
Previously, the IP address of the ELK Server was added in the Ansible hosts file under the “elk” group of machines.
  
- name: Configure Elk VM with Docker
    hosts: elk
    remote_user: RedAdmin
    become: true
    tasks:

The playbook then was configured to install the Docker engine used to run containers, called docker.io.

        Use apt module 
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present       

The python3-pip package was installed, which is used to run Python software.

         Use apt module
     - name: Install pip3
       apt:
         force_apt_get: yes
         name: python3-pip
         state: present

Ansible requires the Docker Python client to control Docker Containers.
In the next step the playbook will specify the installation of this Docker Python module.

         Use pip module
     - name: Install Docker python module
       pip:
         name: docker
         state: present

The next step in the playbook will help ensure the ELK server utilizes the proper amount of memory required to run.

         Use sysctl module
     - name: Use more memory
       sysctl:
         name: vm.max_map_count
         value: "262144"
         state: present
         reload: yes

The playbook will be configured to install the ELK Docker container called sebp/elk:761.

         Use docker container module
       name: download and launch a docker elk container
       docker_container:
         name: elk
         image: sebp/elk:761
         state: started
         restart_policy: always

In the final step of the playbook the port mappings will be defined.

        published_ports:
           - 5601:5601
           - 9200:9200
           - 5044:5044


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![dokcer_ps_outpost](https://user-images.githubusercontent.com/95328030/160609870-60bfe57a-18c8-47e5-8e21-4db9d9702fdc.PNG)


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
Web1 IP: 10.0.0.15
Web2 IP: 10.0.0.16
Web3 IP: 10.0.0.9 


We have installed the following Beats on these machines:
-We have successfully installed filebeat and metricbeat. 

These Beats allow us to collect the following information from each machine:
-Filebeat collects and aggregates various log events regarding the file systems on a host. For example, we can process logs for SSH logins to determine successful and failed login attempts into the system. 

-Metricbeat reports on the health of a system and collects various machine metrics such as uptime, memory usage, and Network. We can also specifically monitor CPU usage on a host and determine if it is being used as expected or is being exhausted with the potential to fail.


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yml file to /etc/ansible/files/filebeat-config.yml .
- Update the filebeat-config.yml file to include the installation path, username/password, the IP address of the ELK server under output.elasticsearch within the configuration file, and the IP address and port number under the setup.kibana field.
- Run the playbook, and navigate to http://[public IP address of ELK Server]:5601/app/kibana to check that the installation worked as expected.To confirm that the ELK server is receiving logs from Web1,Web2 and Web3 you will navigate from within the Kibana GUI to Add Log Data --> System Logs --> DEB tab --> Step 5: Module Status --> Check Data.



Answer the following questions to fill in the blanks:
Which file is the playbook? Where do you copy it?
- filebeat-playbook.yml. It will be copied in the /etc/ansible/roles/filebeat-playbook.yml directory.

Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?
- This can be defined within the Ansible Hosts file. Under the "webservers" grouping, we listed the VM's that will be configured with Filebeat. In addition, within the same file we created an "elk" group to specify the VM that will be configured with the ELK server.

Which URL do you navigate to in order to check that the ELK server is running?
- http://[public IP address of ELK Server]:5601/app/kibana
