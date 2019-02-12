<img src="https://www.ansible.com/hubfs/2016_Images/Assets/Ansible-Mark-Large-RGB-Mango.png?hsLang=en-us" height=100>

# [Ansible](https://www.ansible.com)

For the sake of giving context to the files contained herein, note I have created a fictitious (though relevant) configuration to work with.  I'm not a big fan of example files that don't explain why things are done, and I work best with working examples myself.  The idea is that you can download this directory in its entirety, bring up a shell, navigate to this directory, and after making appropriate adjustments, execute the Ansible ad-hoc and playbook commands.

I have commented the playbooks extensively for the purpose of explaining what each does, but these comments are not needed for running the playbooks.  Do with them what you will. 

## Network Configuration

The fictitious network in question is that of an ISP and involves two (2) Points of Presence (POPs) that we'll simply refer to as `north` and `south`.  The two POPs are connected to each other, and all customer circuits land at one of the two POPs.  (For a visual reference, imagine a bicycle, where the hubs of the wheels are the POPs and the spokes on the wheels fan out to the customers.  The frame of the bicycle connects the one hub to the other.)  And from each POP there are circuits out to the Internet.

We'll stick to basic RFC1918 private IPv4 addressing.  We'll imagine all customers are provided `10.x.x.x` subnets, and that each Customer Premise Equipment (CPE) router is configured with a loopback interface that is set to a `10.0.1.x/24` IP address.  All appropriate routing is assumed to be working.  It will also be assumed that all CPE gear is configured for Authentication, Authorization, and Accounting (AAA) using RADIUS or TACACS+.  That is, the idea here is that you, as network admin, are able to SSH into each and every piece of network equipment using a single username/password.

Please note that as Ansible was originally intended for host management, the term they use for a device you manage is a `host`.  So we will use this term though we'll be talking about routers, switches, etc. as `hosts`.

While best practice would be to have DNS configured so that all hosts have Fully Qualified Domain Names (FQDNs) that resolve to their loopback 10.0.1.x IPs, this example will not assume DNS (again, taken from actual experience here).  Instead, we'll rely on the hostnames defined in the Ansible inventory (e.g., `site1` having their loopback IPs defined for the host so that the hostname can be used very much like an FQDN).

## Directory Structure

```
ansible.cfg               == Ansible configuration settings
group_vars/               == directory w/ group vars
    all                   == vars relevant to all hosts
    north                 == vars for hosts in north
    south                 == vars for hosts in south
host_vars/                == directory w/ host vars
    site1
    site2
    site3
    site4
inventory-singlefile.txt  == example single inventory file
inventory.txt             == inventory file used w/ dirs above
<name>.yml                == Ansible playbook file
```
