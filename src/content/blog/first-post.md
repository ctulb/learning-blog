---
title: 'BIG-IP NEXT Deployment'
description: 'Deployment of the Central Manager and appliance in VMWare'
pubDate: 'Jan 25 2024'
heroImage: 'https://imgur.com/18f9Fqt.png'
---

While previously it was possible to operate F5 VE appliances directly on-box, with BIG-IQ using more of a "pull" model for configuration, in BIG-IP NEXT deployment of the Central Manager appliance is required in order to administer appliances via a web UI.

I'll begin by deploying a single Next Central Manager and appliance - High Availability will be a topic I'll return to later. After lab deployment the topology should look like the below:

![Lab network diagram showing the CM and appliance connected to a management network with DHCP enabled](https://i.imgur.com/jbHbSat.png "Initial Topology")

First I'll boot the Central Manager, this lands at an Ubuntu CLI with the DHCP assigned address indicated. Log in using admin/admin, change the default password and the run the setup script using `setup`.

![Screenshot showing the Central Manager CLI setup prompt after initial login](https://imgur.com/UPr0Ca0.png)

- Hostname: `next-cm.test.lab`
- Do you want to configure a static IP address?: `N`
- Primary NTP Server Address: leave default
- Alternate NTP Server Address: leave default
- IPv4 network CIDR to use for service IPs \[100.75.0.0/16\]: left as default, must not overlap existing range
- IPv4 network CIDR to use for pod IPs \[100.76.0.0/16\]: as above
- Would you like to set up an external storage mount?: `N`
- Would you like to complete configuration with these parameters?: `Y`
- Would you like to start the BIG-IP Next Central Manager application installation (Y/n): `Y`

The installer will then run through setting up the Next Central Manager application, and eventually returns a "Setup completed successfully" message (hopefully!). The Web UI can then be accessed via the management IP address. Note that the WebUI credentials **don't** sync with the CLI credentials - use admin/admin again and change password as prompted again. It's possible to lockout the admin account by default, so be careful here.

![Screenshot showing the Central Manager WebUI login prompt](https://imgur.com/OOdpG4d.png)

The next job will be to get the Next instance up and running - again powering up after deployment will land on a Ubuntu CLI prompt with the DHCP assigned address indicated. Again login with admin/admin and run `setup`.

- Hostname: `next-1.test.lab`
- Do you want to configure a static IP addres?: `N`
- Primary NTP Server Address: leave default
- VLAN1 name (e.g. external): leave blank
- License JSON Web Token: leave blank, I'll apply this later
- Enter new admin password at prompt
- Would you like to complete configuration with these parameters?: `Y`

The installer will then run through setting up the Next instance, returning a "Setup completed successfully" message. Note no web UI is directly exposed - the API is available on port 5443, but there is no other on-box configuration method.

The instance can now be added to the Central Manager via Infrastructure > Instances > Start Adding Instances.

![Screenshot showing the Add Instance options](https://imgur.com/hX0ycG4.png)

A prompt will then appear to create a new set of instance credentials for management via Next Central Manager. Once this is done, Add Instance can be pressed to add the instance to the deployment. Now it's time to license the instance - select the instance from the list and select Actions > License.

![Screenshot showing License option under Actions for instance](https://imgur.com/ZmLVUmg.png)

Paste in the license JSON Web Token obtained from F5, and providing the Central Manager has Internet access, after a time the license will be activated.

![Screenshot showing license active on instance](https://imgur.com/18f9Fqt.png)

That'll complete my lab deployment for now - next time I'll look at building out the rest of the supporting topology and configuring interfaces on the Next instance.
