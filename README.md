# Setting up the Ansible host

These playbooks have been tested with Ansible 2.3.1.0 on macOS 10.12.6 but should work on any platform supported by Ansible.

In addition to Ansible and Python, you will need a working install of boto. Install this in the manner for your platform, then remember to set the credentials for the AWS account you will be using. This can either go at the bottom of the `ec2.ini` file (examples are in the template file below), or in ~/.aws/credentials.

If you have OS native packages I would recommend you use them, otherwise you can install via pip:

```
pip install ansible # Required when using the Brew install of Ansible
pip install six
pip install dnspython
pip install boto
pip install boto3
```

You will also need a copy of the ec2 dynamic inventory script and configuration file, which should be downloaded from here:

[ec2.py](https://raw.github.com/ansible/ansible/devel/contrib/inventory/ec2.py)
[ec2.ini](https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.ini)

# Setting up the EC2 instances

1. Set up your `group_vars/all/main.yml` file using `group_vars/main.yml.example` as a template. At the minimum you need to set:

* ec2_access_key:
* ec2_vpc_id:
* ec2_keypair:

These will all be unique to your AWS environment. 

You also need to specify your AWS secret key - unless you are sure you want it in the clear, put it in a vault file:

```
ansible-vault create group_vars/all/secret.yml
```

The vault file contents should be:

```
---
ec2_secret_key: xxxxxxxx
```

2. Set up redirection variables as required:

Copy the `group_vars/tag_ansible_group_bounce_server/redirect.yml.example` file to `group_vars/tag_ansible_group_bounce_server/redirect.yml` and edit it as appropriate. You can include as many or as few redirects as you want.

3. Spin up the instances as follows:

```
ansible-playbook --ask-vault-pass launch-instance.yml
```

4. Once confirmed as running, configure the servers for port forwarding:

```
ansible-playbook -i ec2.py --ask-vault-pass --private-key <EC2 PEM file location> setup.yml
```

5. Enjoy!

6. When no longer required, simply run this playbook to terminate the instances and cleanup the Security Group we created earlier:

```
ansible-playbook -i ec2.py --ask-vault-pass terminate-instance.yml
```
