## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

Images/diagram_filename.png

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. 

Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

filebeat-playbook.yml
metricbeat-playbook.yml
pentest.yml
filebeat-config.yml
install-elk.yml
metricbeat-config.yml

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

The load balancer restricts high traffic and ensures functionality. It helps prevent overloading servers as well as optimizes productivity and maximizes uptime.
It adds resiliency by rerouting live traffic from one server to another causing it to eliminate single points of failure from attacks such as DDoS attack.

Jump boxes are highly secured computers that are primarily used for admin tasks only. They are comprehensive/lock-down secure admin workstations so as to decrease the
chances of hackers/malware infection.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log files and to monitor system resources

- Filebeat monitors log files and forwards the specified logs to Elasticsearch.

- Metricbeat collects and transports metrics to Elasticsearch.

The configuration details of each machine may be found below.

| Name             | Function| Priv IP//Public IP      | Operating System |
|------------------|---------|------------------------ |------------------|
| Red-Team-Jumpbox | Gateway | 10.0.0.4//137.117.22.72 | Linux            |
| Red-Team-Web1    | Server  | 10.0.0.7                | Linux            |
| Red-Team-Web2    | Server  | 10.0.0.6                | Linux            |
| ELK-VM           | Server  | 10.1.0.4//20.120.14.143 | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Red-Team-Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 76.99.252.190

Machines within the network can only be accessed by Red-Team-Jumpbox.

The machine below has access to the ELK VM

Red-Team-Jumpbox
Private IP: 10.0.0.4 

A summary of the access policies in place can be found in the table below.

| Name             | Publicly Accessible | Allowed IP Addresses |
|------------------|---------------------|----------------------|
| Red-Team-Jumpbox | Yes                 | 76.99.252.190        |
| Red-Team-Web1    | No                  | 10.0.0.4             |
| Red-Team-Web2    | No                  | 10.0.0.4             |
| ELK-VM           | No                  | 10.0.0.4             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because this provides the ability to configure machines in a consistent, repeatable, and scalable way.

The playbook implements the following tasks:
- Installs Docker
- Installs Python
- Installs pip3
- Configures the target machine to use more memory
- Installs the Docker Python module
- Downloads and launches Docker container sebp/elk:761
- Configures the Container start port mappings 
- Enables Docker

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

Images/docker_ps.png

### Target Machines & Beats
This ELK server is configured to monitor the following machines:

| Red-Team-Web1    | Web Server          | 10.0.0.7
| Red-Team-Web2    | Web Server          | 10.0.0.6 

We have installed the following Beats on these machines:
- Filebeat
- MetricBeat

These Beats allow us to collect the following information from each machine:

- Filebeat provides logs:
			 -syslogs (server and time included)
 			 -processes
                         -messages 

-Metricbeat provides metrics:
                        -CPU usage,
      			-memory
                        -containers (number, status, etc)

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

- Copy the three playbooks install-elk.yml, metricbeat-playbook.yml, and filebeat-playbook.yml file to /etc/ansible/roles/install-elk.yml.

-Update the hosts file to include your destination IP of the elk server.

-Run the playbook, and navigate to http://20.120.14.143:5601/app/kibana to check that the installation worked as expected.

-Copy filebeat-playbook.yml and metricbeat-playbook.yml from /etc/ansible/roles.

-Update the hosts file under the appropriate server with the machine's internal IP address to make Ansible run the playbook on a specific machine.

-Navigate to the following URL to check that the ELK server is running: http:/20.120.14.143:5601/app/kibana

Groups in the host file specify which machines to install file beat versus elk on. The group for File beat is webservers the elk group is ELK.


Specific Commands Bonus

 -------Filebeat---------

- To create the filebeat-configuration.yml file: nano filebeat-configuration.yml. For this, I used the filebeat configuration file template.

- To create the playbook: nano filebeat-playbook.yml

  ---
 - name: installing and launching filebeat
	   hosts: webservers
       become: true
       tasks:

	   - name: download filebeat deb
  	     command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.1-amd64.deb

	   - name: install filebeat deb
  	     command: dpkg -i filebeat-7.7.1-amd64.deb

	   - name: drop in filebeat.yml
  	     copy:
   	       src: ./files/filebeat-configuration.yml
   	       dest: /etc/filebeat/filebeat.yml

	   - name: enable and configure system module
  	     command: filebeat modules enable system

	   - name: setup filebeat
  	     command: filebeat setup

	   - name: start filebeat service
  	    command: service filebeat start
---
-To run the playbook: ansible-playbook filebeat-playbook.yml

* In order to run the playbook, you have to be in the directory of the playbook, orspecify the path to it (ansible-playbook /etc/ansible/roles/filebeat-playbook.yml


-------Metricbeat-------

- To create the metricbeat-configuration.yml file: nano metricbeat-configuration.yml. For this, I used the metricbeat configuration file template.

- To create the playbool: nano metricbeat-playbook.yml

---
  - name: installing and lunching metricbeat
    hosts: webservers
    become: true
    tasks:
    
  - name: download metricbeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.7.1-amd64.deb
    
  - name: install metricbeat deb
    command: sudo dpkg -i metricbeat-7.7.1-amd64.deb
    
  - name: drop in metricbeat.yml
    copy:
      src: /etc/ansible/roles/files/metricbeat-configuration.yml
      dest: /etc/metricbeat/metricbeat.yml
      
   - name: enable and configure system module
     command: metricbeat modules enable system
     
   - name: setup metricbeat
     command: metricbeat setup
     
   - name: start metricbeat service
     command: service metricbeat start
     
   ---
   
   - To run the playbook: ansible-playbook metricbeat-playbook.yml
   
   * To order to run the playbook, you have to be in the directory of the playbook, or specify the path to it (ansible-playbook /etc/ansible/roles/metricbeat-playbook.yml
