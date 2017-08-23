pouta-ansible-cuda-demo
--------------

This is a demo playbook that you can use to spawn one or more instances in an openstack and then install https://developer.nvidia.com/cuda-toolkit on.

The spawn a VM playbook is based on https://github.com/CSCfi/pouta-ansible-demo - more usage instructions are available there.

Usage:

 - source the openrc file
 - Run ansible.

<pre>
$ source project-openrc.sh
$ ansible-playbook -u cloud-user site.yml
</pre>

Now you'll need to assign a public IP manually if the auto_ip didn't assign one..

ssh in and try to use the GPGPU:
<pre>
$ sudo nvidia-smi
</pre>

Or use ansible for this too:
<pre>
$ ansible -i inventory -m command -a "/usr/bin/nvidia-smi" all
</pre>

FAQ:
-----

The nvidia-kmod DKMS needs to match the running kernel or nothing works. To verify that DKMS is configured **incorrectly** run:

<pre>
$ dkms status
nvidia, 375.51: added
</pre>

Fix this on CentOS7 by:
 - yum update
 - reboot
 - dkms autoinstall

Fix this on Ubuntu 16.04 by:
 - apt upgrade
 - install the linux-headers for the kernel (dkms autoinstall tells you which)
  - apt install linux-headers-$(uname -r)
 - dkms autoinstall
 - reboot

Good looking DKMS status looks like this:
<pre>
$ dkms status
nvidia, 375.51, 3.10.0-514.26.2.el7.x86_64, x86_64: installed
</pre>

or

<pre>
$ dkms status
nvidia-384, 384.66, 4.4.0-79-generic, x86_64: installed
nvidia-384, 384.66, 4.4.0-92-generic, x86_64: installed
</pre>



Cool things we could do:
----------

 - For when spawning many GPUs - don't waste floating IPs and use a bastion host
 - http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html#example-openstack-external-inventory-script
 - Create an inventory file with ansible for future runs
 - Reduce parameters to the os_server tasks in spawn_vms.yml playbook somehow - block: isn't possible here I think?
