<img src="https://www.ansible.com/hubfs/2016_Images/Assets/Ansible-Mark-Large-RGB-Mango.png?hsLang=en-us" height=100>

# [Ansible for Network Automation](https://www.ansible.com)

#### Configuration used (as of Feb 2019)
- macOS 10.14.3
- Ansible 2.7.6

----
This directory contains various (sanitized) example **Ansible playbooks** I've written, along with other tidbits I've gleaned over time.  It is very much a work in progress.  I realize there are many ways to do things in Ansible, and the ways shown here are far from perfect.  But hopefully someone will find this information helpful.

The emphasis here is on using Ansible for network automation, which means making use of the [Ansible Network Modules](https://docs.ansible.com/ansible/latest/modules/list_of_network_modules.html).

All my work has been done mostly on **macOS**, but I've also been doing some work on **Linux** systems.  The only thing I would warn you about is not to try and run Ansible from **MS Windows**.  (You can run Ansible against Windows hosts, of course, but we're focusing here on network devices.)

If you're a Windows user, either remote into a Linux system to work with Ansible, or possibly run either a Virtual Machine (VM) of Linux using something like [VMware Workstation](https://www.vmware.com/products/workstation-pro.html) ($$$), [VMware Player](https://www.vmware.com/products/workstation-player.html) (free), or [Oracle VirtualBox](https://www.virtualbox.org/) (free, open-source).  Another option is to use [Docker](https://hub.docker.com/editions/community/docker-ce-desktop-windows), though if you're not familiar with any of these, I'd recommend starting with VirtualBox.  It's free, is a simple download/install, and once done, all you need is to download some version of Linux to install, typically [Ubuntu](https://www.ubuntu.com/download/server) or [CentOS](https://www.centos.org/download/).  As Ansible is a command line utility, I tend to run server versions that don't include a Graphical User Interface (GUI), but to each their own.

## Starting with Ansible

Note that Ansible doesn't require a heavy investment up front to begin being useful.  You can start small and build up your knowledge over time.

- To begin, install it, which could be as simple as doing a `sudo pip install ansible` on **macOS** or uing one of the package managers (e.g., apt, yum, etc.) on a Linux distro.

- Then put together a simple text inventory file and use Ansible to run ad-hoc commands that let you do things on multiple devices simultaneously, such as collecting basic information from routers. This alone can show you the power of Ansible, as you can collect, for example, the current NTP settings on all of your routers in one shot.

- Later, you could expand the inventory setup as shown here by using directories to hold host and group information, and write Ansible playbooks (the real workhorses of Ansible).  With playbooks, you effectively put your knowledge into a file, one that you or others, who may not even know how to do the work, can then execute the playbook.

- Later still you could work to develop full on Ansible roles if that makes sense. (I'm not there yet.)

If/when I have time, I will try to write up a tutorial to walk you through some basics of Ansible for network automation.  For now, please see the [presentation I put together for the Fall 2017 Internet2 Technology Exchange (TechEX)](http://frank.seesink.com/presentations/Ansible-Fall2017/)].  It's available in **Apple Keynote** (ideal), **MS PowerPoint** (ok), or simple **PDF** (but you miss all my awesome animation skills :-P ).  It's a little dated now, and apologies in advance that there's not much text. I'm a believer in using the visual medium properly (and I detest "death by PowerPoint" slide decks with 15 bullets/slide), so most of the content came from me talking and demoing.  But some slides, notably the second half, should give enough of the basics.

## FICTITIOUS SETUP

For the sake of giving context to the files contained herein, note I have created a fictitious (though relevant as it's kinda/sorta taken from my work) configuration to work with.  I'm not a big fan of example files without context, that don't explain why things are done.  And I work best with working examples myself.  The idea is that you can download this directory in its entirety, bring up a shell, navigate to this directory, and after making appropriate adjustments to the ansible.cfg, the inventory files, etc., you can execute the Ansible ad-hoc and playbook commands.

I have tried to comment the playbooks extensively for the purpose of explaining what each does, as well as the other files, but these comments are not needed for running the playbooks.  Do with them what you will.

## Network Configuration

The fictitious network in question is that of an ISP (hereafter called THE NETWORK) and involves two (2) Points of Presence (POPs) that we'll simply refer to as `north` and `south`.  The two POPs are connected to each other, and all customer circuits land at one of the two POPs.  (For a visual reference, imagine a bicycle, where the hubs of the wheels are the POPs and the spokes on the wheels fan out to the customers.  The frame of the bicycle connects the one hub to the other.)  And from each POP there are circuits out to the Internet.

We'll stick to basic RFC1918 private IPv4 addressing.  THE NETWORK owns the IPv4 10.0.0.0/8.  We'll imagine all customers are provided `10.x.x.x` subnets, and that each Customer Premise Equipment (CPE) router is configured with a loopback interface that is set to a `10.0.1.x/32` IP address.  All appropriate routing is assumed to be working.  It will also be assumed that all CPE gear is configured for Authentication, Authorization, and Accounting (AAA) using RADIUS or TACACS+.  (Hence the loopback IPs.)  That is, the idea here is that you, as network admin, are able to SSH into each and every piece of network equipment using a single username/password.  (I would go into setting up SSH keys, but for now let's keep things simple.)

Please note that as Ansible was originally intended for host management, the term they use for a device you manage is a `host`.  So we will use this term though we'll be talking about routers, switches, etc. as `hosts`.

While best practice would be to have DNS configured so that all hosts have Fully Qualified Domain Names (FQDNs) that resolve to their loopback 10.0.1.x IPs, this example will not assume DNS (again, taken from actual experience here).  Instead, we'll rely on the hostname defined in the Ansible inventory (e.g., `office1` having its loopback IP defined for the host so that the hostname can be used very much like an FQDN).  The nice part here is that this feature allows Ansible users to reference devices by name, which sure beats having to remember all those 10.x.x.x IPs.  If your environment has DNS, you can skip using the `ansible_host` variable as you'll see used here.

All CPE gear will be assumed to be running `Cisco IOS`.

The DNS server in the `north` POP has IP `10.1.0.1`.

The DNS server in the `south` POP has IP `10.255.0.1`.

Routers should have their primary DNS server set to the one in the POP where they land and their secondary DNS server set to the opposite (e.g., a router in the north would point to `10.1.0.1` first, then `10.255.0.1` second).

## Directory Structure

```
ansible.cfg               == Ansible configuration settings
group_vars/               == directory w/ group vars
    all                   == vars relevant to all hosts
    north                 == vars for hosts in north
    south                 == vars for hosts in south
host_vars/                == directory w/ host vars
    office1
    office2
    office3
    office4
    school1
    school2
    school3
    school4
inventory-singlefile.txt  == example single inventory file
inventory.txt             == inventory file used w/ dirs above
<name>.yml                == Ansible playbook file
```

## Ansible.cfg

There is a whole [precedence/pecking order](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) to how Ansible resolves settings, variables, etc.  Typically on *nix systems, you'd have things done at the system level under `/etc/ansible`.  This would be the "fall back" for all users if things weren't defined elsewhere.  But for these examples, I'm keeping everything to a local folder structure.  I'll leave it to the reader to move things as appropriate.

What this means is that if copy/clone this directory (e.g., you have these files/directories located at `/Users/you/ansible/`) and you make sure to make your current working directory where the **ansible.cfg** file is (e.g., `cd /Users/you/ansible`), then that config file should take precedence for setting up your environment.  The only thing you should need to set in that file is your username.

## Ansible Playbooks

I have tried to comment the Ansible playbooks so you can make sense of them.  Please note that I do make heavy use of [Jinja2](http://jinja.pocoo.org/) templating (what you'll see as `{{ var }}` and so forth in the playbooks), as that is a key part of how Ansible handles things, since it is written in Python.
