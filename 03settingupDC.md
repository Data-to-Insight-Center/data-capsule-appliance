---
layout: page
title: Customizing
permalink: /customizing/
---

## Customizing for your collection 

With a verified setup done using the [Getting Started Guide](/data-capsule-appliance/gettingStarted/), these are the minimal steps required to deploy the system to be used
with your collection. 
- Install the required tools to the DC image
- Set up the host firewall rules 

Further customizations are available in the DC Guest and Host repos. 

#### Install Tools into DC image

There are two ways of installing the required tools to the DC Image:

1. Use the `vmadmin.sh` script available in the `/home/dc-host-bin/` directory to startup the image you have and install the required tools. 
```shell
vmadmin.sh <DC_image> --vnc <vnc_port> --cdrom <ubuntu_vanilla_image>
```
This will startup a qemu VM where you can install the required tools in the DC image itself and you can use this as the base image for your DC setup. 

2. To have persistent configurations, we recommend changing the scripts as required in the [Data Capsule Appliance Guest repo](https://github.com/Data-to-Insight-Center/Data-Capsule-Appliance-Guest "Data Capsule Appliance Guest repo"){:target="_blank"} - change/add scripts to the downloaded code and customize the configurations in the `ubuntu.json` file to get a customized version of the DC image that caters to your needs. 

#### Setting up Data Connection
You need to setup the host so that it knows your secure data source location. 
In the out of the box example in the getting started guide, the secure API we’ve used for testing is the Google DNS IP with the port 53. 

This port and the IP can be configured in the [Data-Capsule-Appliance-Host/group_vars/all](https://github.com/Data-to-Insight-Center/Data-Capsule-Appliance-Host/blob/master/group_vars/all "groupvars-all"){:target="_blank"} file, in the secure API section. 

If you have only a single IP and port combination as your restricted data source location, you can modify the Data-Capsule-Appliance-Host/group_vars/all replacing the secure API details.  
Then you need to re-run Ansible scripts for these changes to take effect. 

If you need more flexibility, e.g., multiple data sources to support, we recommend modifying the firewall rules as required in the [Data-Capsule-Appliance-Host/roles/common/templates](https://github.com/Data-to-Insight-Center/Data-Capsule-Appliance-Host/tree/master/roles/common/templates "iptablerule_templates"){:target="_blank"} directory.

