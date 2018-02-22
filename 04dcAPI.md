---
layout: page
title: DC API
permalink: /dcapi/
---

## Data Capsule API Deployment 

The DC API is a dockerized http API developed to manage the Data Capsules. 
This documentation walks you through installing and testing the DC API deployment. 

Steps to deploy the Data Capsule API
- [Download](#download)
- [Pre-requisites](#pre-requisites)
- [How to Run](#how-to-run)
- [Configure](#configurations)
- [Test the deployment](#testing)

### Download

Download the dockerized DC-API [dockerized DC-API.](https://github.com/htrc/HTRC-DataCapsules/tree/IMSL/docker "dockerized DC API."){:target="_blank"}

### Pre-requisites
You need to download and install [Docker.](https://docs.docker.com/install "Docker."){:target="_blank"}

### How to Run
Before running the DC API, first refer to the [configurations section](#configurations) and do the necessary changes. 

Then to run the docker containers, run the following command from inside the downloaded directory. 
```shell
./set_api.sh <MYSQL_ROOT_PASSWORD>
```
Replace the `<MYSQL_ROOT_PASSWORD>` with the mysql password you want to set for DC API setup. 

If everything works fine, you should see *htrc-dcapi* and *htrc-mysql* docker containers created.
Use the following command to verify that. A sample output is shown below. 
```shell
$docker ps
                  
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS                   PORTS                                              NAMES
9cd1c1c4868a        htrc/dcapi               "catalina.sh run"        4 minutes ago       Up 4 minutes             0.0.0.0:8080->8080/tcp                             htrc-dcapi
590cca371eed        mysql/mysql-server:5.7   "/entrypoint.sh my..."   4 minutes ago       Up 4 minutes (healthy)   0.0.0.0:3306->3306/tcp, 0.0.0.0:33060->33060/tcp   htrc-mysql
```

To test the DC API, please go to [testing section](#testing).


### Configurations

Do the required configuration changes to the following files. 

**[sites.xml](https://github.com/htrc/HTRC-DataCapsules/blob/IMSL/docker/dc-api/config/sites.xml "view in git"){:target="_blank"} (dc_api/config/sites.xml)**: *This file contains Data Capsule hosting related  information*

- Change the `DC_HOST_SCRIPT_DIR` to the script directory location in the host `/home/dc-host-bin`

- Change `DC_API_ENDPOINT` with the IP of the machine in which the DC API would be deployed in

- Add host user login details at the end (user who would be running the scripts ex: dc-host user). You can either use password authentication or ssh key authentication. 
{% highlight xml %}
<name>host.ssh.username</name>
     <value>xxxxx</value>
</property>
<!--
<property>
      <name>host.ssh.passwd</name>
      <value>XXX</value>
 </property>
    -->
 <property>
      <name>host.ssh.private.key.path</name>
      <value>xxxxx</value>
 </property>
{% endhighlight %}

- Comment AlwaysSuccessHypervisor and uncomment CapsuleHypervisor. The change should look as follows
{% highlight xml %}
<property>
    <name>hypervisor.full.class.name</name>                
    <!--value>edu.indiana.d2i.sloan.hyper.AlwaysSuccessHypervisor</value-->              
    <value>edu.indiana.d2i.sloan.hyper.CapsuleHypervisor</value>
</property>
{% endhighlight %}

- Add sloan.ws.vm.workdir.prefix to the preferred working directory location
          ex:
          {% highlight xml %}
<property>
	<name>sloan.ws.vm.workdir.prefix</name>
    <value>/home/dc-host/vm-workingdir</value>
</property>
{% endhighlight %}

- Change the value of sloan.ws.hosts to the hosts that you would run the Data Capsules on (Data Capsule hosts list)
{% highlight xml %}
<property>
    <name>sloan.ws.hosts</name>
    <value>oilpalm2.pti.indiana.edu</value>
</property>         
{% endhighlight %}

- Specify the port range that’s open for SSH and VNC connections to the outside
{% highlight xml %}
<property>
	<name>sloan.ws.port.range.min</name>
	<value>16000</value>
</property>
<property>
	<name>sloan.ws.port.range.max</name>
	<value>16100</value>
</property>
{% endhighlight %}

- Change Email settings according to your email host. This is used to send result release notifications.
{% highlight xml %}
<property>
	<name>email.sendername</name>
	<value>xxx</value>
</property>
<property>
	<name>email.sender.addr</name>
	<value>xxx@xxx.xxx</value>
</property>
<property>
	<name>email.password</name>
	<value>xxxxxx</value>
</property>
<property>
	<name>email.smtp.host</name>
	<value>xxxxxx</value>
</property>
<property>
	<name>email.smtp.port</name>
	<value>xxxx</value>
</property>
 {% endhighlight %}

- Add your result reviewers emails. Separate values by ‘;’
{% highlight xml %}  
<property>
	<name>result.review.email</name>
	<value>reviewer1@xxx.edu;reviewer2@xxx.edu</value>
</property>
{% endhighlight %}  

- Change these values according to the default capsule resources that you are willing to provide
{% highlight xml %}  	
<!-- volume size, user quota -->
<property>
	<name>sloan.ws.volume.size.gb</name>
	<value>10</value>
</property>
<property>
	<name>user.disk.quota.in.gb</name>
	<value>100</value>
</property>
<property>
	<name>user.cpu.quota.in.num</name>
	<value>10</value>
</property>
<property>
	<name>user.memory.quota.in.mb</name>
	<value>20480</value>
</property>
{% endhighlight %}  

**[dcapi_loaddata.sql](https://github.com/htrc/HTRC-DataCapsules/blob/IMSL/docker/mysql/scripts/mysql-scripts/dcapi_loaddata.sql "view in git"){:target="_blank"}(mysql/scripts/mysql-scripts)**


- Change the images line to add the details on the DC image
```sql
INSERT INTO images VALUES ('<image_name>', "ACTIVE",'<image_description>', '<image_location>', '<image_login_username>', '<image_login_password>');
```
- Change the vmhosts line to add your hosts
```sql
INSERT INTO vmhosts VALUES ('<host_url>','<number_of_cpus>','<memory_in_gb>');
```



### Testing

This section describes how to test DC API. Here we are going to test the API using a Python script which run in the Python 2.7 environment.

- `$ cd HTRC-DataCapsules/webservice/tools` [view in git](https://github.com/htrc/HTRC-DataCapsules/tree/IMSL/webservice/tools "view in git"){:target="_blank"}

- Change the values in `dccli.py` script, if you are running DC API in different host and port.
Default values:
`DC_API = 'localhost'
PORT = '8080'`

- To create a capsule via the DC API, you can run the following command
```shell 
python dccli.py create <user_id> <user_email> <image_name> <vnc_username> <vnc_password> <memory_in_MB> <number_of_cpus>
```
Ex: 
```shell
python dccli.py create samitha34 samitha.liyanage@gmail.com ubuntu-16-04 123 123 2048 2
```
*For the `<image_name>`, use the same image name you entered in the dcapi_loaddata.sql script*

If this works you should see,
- the vmid as the output. Ex: 
```shell
{"vmid":"1b7b43e2-b09c-40b1-a04d-54d8791f0f66"}
```
- the vm directory in the DC host server Ex: 
```shell
$ls /home/dc-host/vm-workingdir/1b7b43e2-b09c-40b1-a04d-54d8791f0f66
```


- To start capsule
```shell
python dccli.py start <vm_id> <user_id> <user_email>
```
Ex: 
```shell
python dccli.py start 1b7b43e2-b09c-40b1-a04d-54d8791f0f66 samitha34 samitha.liyanage@gmail.com
```

If this works you should see `Starting  VM <vm_id>....` as the output.
Ex: 
```shell
Starting  VM 1b7b43e2-b09c-40b1-a04d-54d8791f0f66....
- Qemu process started in the DC host server 
```


- To switch modes
```shell
python dccli.py switch <vm_id> <user_id> <user_email> <mode(secure/maintenance)>
```
Ex:
```shell
python dccli.py switch 1b7b43e2-b09c-40b1-a04d-54d8791f0f66 samitha34 samitha.liyanage@gmail.com secure
```

- To stop capsules
```shell
python dccli.py stop <vm_id> <user_id> <user_email>
```
Ex: 
```shell
python dccli.py stop 1b7b43e2-b09c-40b1-a04d-54d8791f0f66 samitha34 samitha.liyanage@gmail.com
```

- To delete capsules
```shell
python dccli.py delete <vm_id> <user_id> <user_email>
```
Ex: 
```shell
python dccli.py delete 1b7b43e2-b09c-40b1-a04d-54d8791f0f66 samitha34 samitha.liyanage@gmail.com
```

- To check all the capsules created using curl
```shell
curl -i -k -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:8080/sloan-ws/listvms
```
         
