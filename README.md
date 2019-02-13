# Network Automation

This repo is simply a collection of information I have put together for my own use, sanitized/adjusted as needed to protect the guilty, and comes with the usual "if you break it, you get to keep both pieces" policy. :-)  But hopefully something in here may be of use to others as well.  Constructive feedback is always welcome.

Note I have organized information into directories according to the tool used.


## Other Sources

* [NetworktoCode's curated awesome-network-automation list](https://github.com/networktocode/awesome-network-automation)
  * Hands down the most complete list of links/etc. regarding network automation.  Bookmark this.

<img src="https://www.ansible.com/hubfs/2016_Images/Assets/Ansible-Mark-Large-RGB-Mango.png?hsLang=en-us" height=100>


## [Ansible](https://www.ansible.com)

Ansible, and agentless configuration management tool, is where I have been spending the majority of my time as I write this, and specifically around network automation.  Please note the information provided in the `Ansible` folder is a work in progress and likely always will be.

GitHub (latest release):  https://github.com/ansible/ansible/releases


<img src="https://upload.wikimedia.org/wikipedia/commons/7/76/Saltstack_logo.png" height=100>

## [SaltStack](https://www.saltstack.com/)

As everything other than Ansible is pretty much agent-based, of those I would choose SaltStack (also written in Python) for projects where that made sense.  With its persistent, event-based bus built on [ZeroMQ](http://zeromq.org/), I just find its design better than some alternatives.  It's also lighter weight, meaning where other agents falter, Salt minions can easily be run on lower power devices like [Raspberry Pis](https://www.raspberrypi.org/).

Binaries:  https://repo.saltstack.com/

GitHub (latest release):  https://github.com/saltstack/salt/releases


<img src="https://raw.githubusercontent.com/napalm-automation/napalm/develop/static/logo.png" height=100>

## [NAPALM](https://napalm-automation.net/)

NAPALM (Network Automation and Programmability Abstraction Layer with Multivendor support) is a vendor neutral, cross-platform open source project that provides a unified API to network devices.

GitHub (latest release):  https://github.com/napalm-automation/napalm/releases

## [Netmiko](https://github.com/ktbyers/netmiko)

Multi-vendor library to simplify Paramiko SSH connections to network devices
