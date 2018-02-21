---
layout: page
title: Getting Started
permalink: /gettingStarted/
---

1. Create the Data Capsule Image
Clone the Data Capsule Appliance Guest repo and follow the instructions to create a Sample Data Capsule Image. 
2. Set up the Data Capsule Host
To setup the DC Host we use Ansible Scripts. Please follow the instructions in the Data Capsule Appliance Host repo. 
3. Test creation of the Data Capsule
Now that you have a working DC host setup and a DC Image, we can begin to test if it all works.
The instructions below assume that the created DC image is at /home/dc-host/images/ubuntu1604 and the variables of the DC host setup are unchanged.  Then you can execute the following commands to test the setup: create DC, start DC, and stop DC.
Create the Data Capsule from Image
sh /home/dc-host-bin/createvm.sh --wdir <VM_working_dir> --image <path_to_image> --ncpu <#cpus> --mem <memory_in_GB> --vnc <vnc_port> --ssh <ssh_port> --volsize <size_of_mounted_volume> --loginid <vnc_login_id> --loginpwd <vnc_login_pwd>
Example:
sh /home/dc-host-bin/createvm.sh --wdir /home/dc-host/data --image /home/dc-host/images/ubuntuImage --ncpu 2 --mem 2G --vnc 16022 --ssh 16024 --volsize 10G --loginid test --loginpwd test
Start the Data Capsule
Data Capsule starts in maintenance mode (access to network is allowed) and can be switched to secure mode (when network access is restricted and the user is allowed to access several network addresses of data sources).  You also have SSH access to the Data Capsule in the maintenance mode. 
Starting the DC in the Maintenance Mode
sh /home/dc-host-bin/startvm.sh --wdir /home/dc-host/data --mode m --policy /home/dc-host-bin/maintenance_mode.fw
To connect to the DC in the maintenance mode, you can either use SSH or VNC. The connection details for these are provided in the DC creation step. 
ex:
ssh dcuser@<host_ip> -p <ssh_port>

Starting the DC in the Secure Mode
sh /home/dc-host-bin/startvm.sh --wdir /home/dc-host/data --mode s --policy /home/dc-host-bin/secure_mode.fw
Stop the Data Capsule
sh /home/dc-host-bin/stopvm.sh --wdir /home/dc-host/data 
4. Test restricted access
Google DNS server can serve as a secure service to test restrict access to. Hence, in the maintenance mode all calls to and from the IP 8.8.8.8 are blocked. In the secure mode, connection to it is enabled, but the rest of the internet is blocked. To test this,
Start the DC in the maintenance mode.
Then open a terminal and ping 8.8.8.8. You should not have access to that. 
You can also use telnet to access port 53. 
So the commands involving 8.8.8.8 server would fail in the maintenance mode. But you should be able to ping other services such as google.com and others.
ping 8.8.8.8
telnet 8.8.8.8 53
Stop the DC and start it in the secure mode. 
telnet 8.8.8.8 53
In this mode, the command would be a success because we have only allowed 8.8.8.8 port 53 to be connected in this mode. But you should not be able to connect to (ping/tcp/ssh ..etc) to other resources such as google.com.
ping google.com
