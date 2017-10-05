# Setting up the Ansible host

These playbooks have been tested with Ansible 2.3.1.0 on macOS 10.12.6 but should work on any platform supported by Ansible.

In addition to Ansible, please install the following packages (using the equivalent package manager for your environment). I am using MacPorts to install these packages (including Ansible) and I am using Python 2.7 from MacPorts as my Python environment, hence these are my supplemental install commands:
```
sudo port install py27-dnspython
sudo port install py27-boto
sudo port select --set python python27
```

You will also need a copy of the ec2 dynamic inventory script and configuration file, which should be downloaded from here:

[ec2.py](https://raw.github.com/ansible/ansible/devel/contrib/inventory/ec2.py)
[ec2.ini](https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.ini)

# Setting up the EC2 instances

1. Set up your `group_vars/all` file using `group_vars/all.example` as a template. At the minimum you need to set:

* ec2_access_key:
* ec2_secret_key:
* ec2_vpc_id:
* ec2_keypair:

These will all be unique to your AWS environment. 

**TO DO:** Move at least `ec2_secret_key` to a vault

2. Set up redirection variables as required:

**TO DO:** Complete this...

3. Spin up the instances as follows:

```
ansible-playbook launch-instance.yml
```

4. Once confirmed as running, configure the servers for port forwarding:

```
ansible-playbook -i ec2.py --private-key <EC2 PEM file location> setup.yml
```

5. Enjoy!

6. When no longer required, simply run this playbook to terminate the instances and cleanup the Security Group we created earlier:

```
ansible-playbook -i ec2.py terminate-instance.yml
```
