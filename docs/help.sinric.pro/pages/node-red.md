---
title: Node Red
layout: post
---

With our Node-RED plugin, now you can receive commands from Amazon Alexa, Google Home or SmartThings in Node-RED.

**Installtion**

Click on the hamburger menu in the top-right corner and select Manage palette, select Install, type [**@sinricpro/node-red-contrib-sinric-pro**](https://flows.nodered.org/node/@sinricpro/node-red-contrib-sinric-pro) and click the install button.

Please make sure Git command line tools are installed.  If you are on a Debian-based distribution, such as Ubuntu, try apt:

```

sudo apt install git-all

```

Otherwise please refer Git documentation

https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

After the installation is complete a new set of nodes shoud appear under **sinricpro** category

**How to use**

* Go to Sinric Pro [**here**](https://sinric.pro)

* Create an account or login using your email address.

* Create a new device and click Save. 

Copy the Device Id, App Key and App Secret from the last page. 

* Create a new flow or use existing one and add a device node.

* Edit the device node. Enter the app credential and enter the device details.

Once the device node is configured, voice commands related to above device in SinricPro will be forward to this Node-RED node. 

* You can add another node (function) to process this output. 

* Add a **response** node to send the response back to Sinric Pro server.

 
Checkout the  [**example**](https://github.com/sinricpro/node-red-contrib-sinric-pro/tree/main/examples) repo for more examples.
