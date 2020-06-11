# Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

- [Network Diagram](Images/diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

---
## List of Files

### Base Files
- [Ansible Configuration File]()
- [Hosts File]()

### Playbook (.yml) Files

- [DVWA Playbook](ansible/roles/playbook-dvwa.yml)
- [ELK Playbook](ansible/roles/playbook-elk.yml)
- [Filebeat Playbook](ansible/roles/playbook-filebeat.yml)
- [Metricbeat Playbook](ansible/roles/playbook-metricbeat.yml)

### Configuration Files

- [Filebeat Configuration](ansible/roles/files/filebeat-configuration.yml)
- [Metricbeat Configuration](ansible/roles/files/metricbeat-configuration.yml)

## List of Screenshots
- [`curl localhost` output](Images/curl_output.png)
- [`docker ps` output](Images/docker_ps_output.png)
- [Editing Host File](Images/edit_hosts.png)
- [Kibana Dashboard](Images/kibana_dashboard.png)
- [Filebeat Dashboard](Images/filebeat_dashboard.png)
- [Filebeat Data Successfully Received](Images/filebeat_success.png)
- [Metricbeat Dashboard](Images/metricbeat_dashboard.png)
- [Metricbeat Data Successfully Received](Images/metricbeat_success.png)

---
### Tree:
```
ELK Stack Project
|   .DS_Store
|   output.txt
|   README.md
|   
+---ansible
|   |   ansible.cfg
|   |   hosts
|   |   
|   \---roles
|       |   playbook-dvwa.yml
|       |   playbook-elk.yml
|       |   playbook-filebeat.yml
|       |   playbook-metricbeat.yml
|       |   
|       \---files
|               filebeat-configuration.yml
|               metricbeat-configuration.yml
|               
\---Images
        curl_output.png
        diagram.png
        docker_ps_output.png
        edit_hosts.png
        filebeat_dashboard.png
        filebeat_success.png
        kibana_dashboard.png
        metricbeat_dashboard.png
        metricbeat_success.png
```
---

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application.

Load balancing ensures that the application will be highly redundant/available, in addition to restricting access to the network.

_What aspect of security do load balancers protect? What is the advantage of a jump box?_

- Load balancers protect against attacks on availability (e.g. DDoS attacks) as it distributes incoming traffic across multiple servers (located in its backend pool).

- The advantage of a jump box is to allow a single point of access for the network, so backend servers can't be accessed by any other computer other than this jump box. Furthermore since our jump box is also a provisioner, we are using it to automatically create our docker containers, all from one place.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the webservers and system files.

_What does Filebeat watch for?_
- Filebeat watches for specified log files and collects log events.

_What does Metricbeat record?_
- Metricbeat in our case will be recording the Docker container (dvwa) metrics, such as CPU/memory usage, network load, storage, and overall health.

The configuration details of each machine may be found below.

<---! _Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_. -->

| Name                 | Function  | Private IP Address | Public IP Address | Operating System | Container |
|----------------------|-----------|--------------------|-------------------|------------------|-----------|
| Jump-Box-Provisioner | Gateway   | 10.0.0.4           | 20.185.79.14      | Linux            | ansible   |
| DVWA-VM1             | Webserver | 10.0.0.6           | -                 | Linux            | dvwa      |
| DVWA-VM2             | Webserver | 10.0.0.5           | -                 | Linux            | dvwa      |
| ELK                  | Webserver | 10.1.0.4           | 52.156.152.234    | Linux            | sebp/elk  |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump-Box-Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 73.54.178.218

Machines within the network can only be accessed by the Jump-Box-Provisioner VM.

 _Which machine did you allow to access your ELK VM? What was its IP address?_

- The ELK VM was allowed access by the Jump-Box-Provisioner.
- It has an IP address of 10.0.0.4.

A summary of the access policies in place can be found in the table below.

| Name                 | Publicly Accessible       | Allowed IP Addresses                                    |
|----------------------|---------------------------|---------------------------------------------------------|
| Jump-Box-Provisioner | Yes (Only to 1 Public IP) | 73.54.178.218 (ubuntu-vm)                               |
| DVWA-VM1             | No                        | 10.0.0.4, 10.1.0.4, through Load Balancer.              |
| DVWA-VM2             | No                        | 10.0.0.4, 10.1.0.4, through Load Balancer.              |
| ELK                  | Yes                       | 10.0.0.4, 10.0.0.5, 10.0.0.6, 73.54.178.218 (ubuntu-vm) |

- (ELK Kibana Port 5601 is only available through the Public IP address).

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it allows the entire configuration to be rebuilt straight from the playbooks if any part needed to be rebuilt, or if the same configuration needed to be made in a different place, without having to manually perform any tasks.

The playbook implements the following tasks:

_In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Docker is installed using apt-get
- Python-pip is installed using apt-get
- Docker module is installed using pip
- Virtual memory is increased (and this change is made persistent)
- The sebp/elk image is downloaded, and a container is created and launched named "elk", with published ports 5044 (Logstash), 5061 (Kibana), and 9200 (Elasticsearch).

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

- [SCREENSHOT: docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:

_List the IP addresses of the machines you are monitoring_

- 10.0.0.5 - DVWA-VM2
- 10.0.0.6 - DVWA-VM1

We have installed the following Beats on these machines:

_Specify which Beats you successfully installed_

- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:

_In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

- Filebeat collects logdata from our DVWA VMs, for example the log files found in /var/log/*.log would be sent to Elasticsearch.

- Metricbeat collects in our case, Docker metrics from our dvwa containers, such as CPU usage, network load, disk usage and memory usage. 


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook files to /etc/ansible/roles, and the configuration files to /etc/ansible/roles/files.
- Update the hosts file to include the hosts you require, placing them under the correct headings e.g. [webservers] or [elkservers]. Update the ansible.cfg file to change the "remote_user" to the username you have chosen for all your VMs.
- Run the playbook you require (in this case your ELK VM playbook), and navigate to either localhost:5601 or 10.1.0.4:5601 (from inside the virtual network using curl), or 52.156.152.234:5601 to check that the installation worked as expected.

_Answer the following questions to fill in the blanks:_

_Which file is the playbook? Where do you copy it?_

- The playbooks are located inside ansible/roles. They should be copied to /etc/ansible/roles in the ansible container you are using to run the playbooks.

_Which file do you update to make Ansible run the playbook on a specific machine?_

- You need to update the hosts file, adding the correct IPs of your webservers (under the header [webserver], which must be uncommented), as well as the correct IPs of your ELK servers (under the header [elkservers] that must be created beneath webservers, with the IPs typed out underneath it).

_How do I specify which machine to install the ELK server on versus which to install Filebeat on?_

- If you wish to run the ELK playbook on the ELK servers, you must change the "hosts: " option in the ELK playbook to "elkservers" (or the heading you put in the hosts file for your elk server list). Similarly, Filebeat must be installed on your endpoints, so ensure that the "hosts: " option in the Filebeat playbook is edited with the correct heading for your webservres list written in the hosts file, default being "webservers".

 _Which URL do you navigate to in order to check that the ELK server is running?_

- Navigate to `[ELK-VM-public-ip]:5601`. Alternatively, if you SSH into the ELK VM, run `curl localhost:5601`, if it comes back blank it is running. Running `curl localhost:9200` will show if Elasticsearch is up.

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

---
### Download + Install ELK Container

- Start the Ansible container:

```bash
$ sudo docker start [ansible_container_name]
```
EXAMPLE: `sudo docker start mystifying_swirles`

- Assuming you have the files on your jump box provisioner (if its on your local, use scp to copy them to your jump box provisioner), use:
```bash
$ sudo docker cp playbook-elk.yml [containerID]:/etc/ansible/roles/playbook-elk.yml
```
- Once the files are copied, use:
```bash
$ sudo docker attach [ansible_container_name]
```
- Navigate to the ansible directory, edit the ansible.cfg file:

```bash
$ cd /etc/ansible/
$ nano ansible.cfg
```
- Uncomment "remote_user" option, and change it to:
```bash
...
remote_user = [username]
...
```
EXAMPLE: `remote_user = redadmin`

- Edit the hosts file:
```bash
$ nano hosts
```
- Assuming you have already uncommented the [webservers] header and added your own webserver IP addresses, add a new header called [elkservers] and add your ELK server IPs underneath like so:

  - [See ELK Server heading](Images/edit_hosts.png)

- Finally navigate to the "roles" directory and run:
```bash
$ cd /etc/ansible/roles
$ ansible-playbook playbook-elk.yml
```
- This will install the ELK container on your ELK server specified in the hosts file.

- Check to see if its up by navigating to `[ELK-VM-PublicIP]:5601`(where the text is replaced with the actual IP address), or SSH into the ELK server VM and execute the following command:

```bash
$ curl localhost:9200
$ curl localhost:5601
```
- If Kibana is not up and running you'll get a message. If it is running, you won't get an output. Elasticsearch (9200) on the other hand will give an output if it is running and reachable.

- Sometimes if you turn the ELK server off, it may not start back up when you start up the VM. The current workaround is to SSH into the ELK server and run `sudo docker ps`, at which point it'll kick itself awake.

  - [Curl Output](Images/curl_output.png)

---
### Download and Install Filebeat/Metricbeat

- Start the Ansible container:

```bash
$ sudo docker start [ansible_container_name]
```

- Copy the filebeats/metricbeat configuration files from Jump Box Provisioner into `/etc/ansible/roles/files` directory, and the playbook files into `/etc/ansible/roles`:

```bash
$ sudo docker cp playbook-filebeat.yml [containerID]:/etc/ansible/roles/playbook-filebeat.yml
$ sudo docker cp playbook-metricbeat.yml [containerID]:/etc/ansible/roles/playbook-metricbeat.yml
$ sudo docker cp filebeat-configuration.yml [containerID]:/etc/ansible/roles/files/filebeat-configuration.yml
$ sudo docker cp metricbeat-configuration.yml [containerID]:/etc/ansible/roles/files/metricbeat-configuration.yml
```

- Jump into the ansible container and navigate to /etc/ansible, check the hosts file to see if the webservers are correct.

```bash
$ sudo docker attach [ansible_container_name]
$ cd /etc/ansible
$ nano hosts
```

- Check under [webservers], add IP addresses if not seen.

  - [See Webserver Heading](Images/edit_hosts.png)

- Edit the filebeat and metricbeat configuration files to reflect the IP address of the ELK server.

  - In this case, ELK-IP = IP address of your ELK VM.

```bash
$ cd /etc/ansible/roles/files
$ nano filebeat-configuration.yml
...
Navigate to line 1106 using Ctrl+Shift+-
Change:
hosts: ["IP:9200"] to ["ELK-IP:9200"]

Navigate to line 1806
Change:
host: "IP:5601" to "ELK-IP:5601"
...

$ nano metricbeat-configuration.yml

...
Navigate to line 62
Change:
host: "IP:5601" to "ELK-IP:5601"

Navigate to line 96
Change:
hosts: ["IP:9200"] to ["ELK-IP:9200"]
...
```

- Navigate to roles, edit the playbook to download the latest .deb files for Filebeat and metricbeat:

```bash
$ cd /etc/ansible/roles
$ nano playbook-filebeat.yml

...
Line 8:
Change file version at end of URL to latest version, e.g.:
.../filebeat-7.4.0-amd64.deb
to
.../filebeat-7.7.1-amd64.deb

Line 11:
Change file version similar to above.
...

and do the same with the metricbeat playbook.

```

- Run `ansible-playbook` with the filebeats playbook first, then check Kibana to see if the files are coming in by navigating to "Home --> Add Log Data --> System Logs --> Scroll Down, click on "Check Data" button to validate.

  - [Filebeat Dashboard](Images/filebeat_dashboard.png)
  - [Filebeat Data Successfully Received](Images/filebeat_success.png)

- Run `ansible-playbook` with the metricbeats playbook second, then check Kibana to see if the data is coming in by navigating to "Home --> Add Metric Data --> Docker metrics --> Scroll Down, click on "Check Data" button to validate.

  - [Metricbeat Dashboard](Images/metricbeat_dashboard.png)
  - [Metricbeat Data Successfully Received](Images/metricbeat_success.png)

```bash
$ ansible-playbook playbook-filebeat.yml
$ ansible-playbook playbook-metricbeat.yml
```




