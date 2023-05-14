# Vagrantfiles for Home Labs

## Introduction
Learning how to set up a home lab where you can practise some System Admin, Cloud and DevOps engineering skills is essential to your growth as a software engineer. You may use the platforms of Cloud service providers for practice; for example, 
- [Amazon Web Service](https://aws.amazon.com) (AWS) gives you a 'Free Tier' account where you can get hands-on within limited resources.
- [Google Cloud Platform](https://cloud.google.com) (GCP) gives you $300 free credit on signup to use their services for 30 days.
- On sign up, [Microsoft Azure](https://azure.microsoft.com) will give you $200 in credit to use their services for 30 days.
- [Linode](https://www.linode.com) gives you $100 free credit to use their services when you signup using the promo code of any of Linode's YouTube influencers. 
As a learner, I'll encourage you to create an account with each of these cloud providers. This will increase your exposure as a developers by giving you hands-on experience working with these platforms. However, to greatly minimize cost especially since you are not yet earning from your practise, it becomes needful to set up home labs. Setting up home labs in your local machine will ensure you can practise at any time with the fear of accruing cost of using cloud services.

That said, let us dive into the main purpose of this article.

## PREREQUISITES
To follow this tutorial, you need
- a PC
- VirtualBox installed. You can do that [here](https://www.virtualbox.org)
- Install Vagrant by Hashicorp according to your Operating System [here](https://developer.hashicorp.com/vagrant/docs/installation)
- a stable internet connection
- background knowledge on Vagrant, Linux OS commands, and working with the terminal.

These vagrant files are customised to create 3 VMs consisting of
- two host nodes and
- one control node

## Take note of the following:
- The provider is Virtual Box, so ensure you have it installed already.
- One of the Vagrantfile is for ubuntu/focal64, while the other is for centos/7
- The network configuration is set in such a way that ssh connection between the centos/7 and ubuntu/focal64 VMs will be allowed.

### Starting your VMs
Once you have your Vagrantfile copied to a designated directory, run the following commands consecutively

```
vagrant init
vagrant up
vagrant ssh ubuntu-control
```
The ubuntu-control node is used as an example in the code block above.

### SSHD Configuration
To enable this connection, go to the /etc/ssh/sshd_config files of each node and change the following parameters to positive:
1. PermitRootLogin yes
2. PasswordAuthentication yes
3. PubKeyAuthentication yes

>
> - Do not alter the rest of this file.
> - Make sure these lines are uncommented by deleting the '#' symbol at the beginning of the line.
> - Then run `sudo systemctl restart sshd` to integrate these changes.
>
> #### Note: this setup is for home labs used for practice (where security is not a concern).
>

### Generate SSH Keys
Generate ssh keys on your control nodes using the `ssh-keygen` command. Use the -t flag to specify the key-type you want. I prefer the ed25519 key-type, because it's short and neat. The RSA key-type will be assigned by default if you don't specify. You may also add a comment with the -C flag, but this is optional. Therefore, we have a command line like this: 
`ssh-keygen -t ed25519 -C optional-descriptive-comment`

This will generate a private and a public key located by default at /home/vagrant/.ssh/id_ed25519 and /home/vagrant/id_ed25519.pub respectively. The default RSA-key-type also follows the same pattern. (The default user is vagrant).

### Copy SSH Keys to Host Nodes
Then you must copy the private key to the VMs' authorized keys file by running,
`ssh-copy-id -i /home/vagrant/id_ed25519 vagrant@ip.address.of.vm`

Finally, you can ssh into your VMs by running, `ssh vagrant@ip.address.of.vm`

### Your IP Addresses
Following the construct of our Vagrantfiles, your IP addresses will be:
- ubuntu-node-1: 192.168.43.21
- ubuntu-node-2: 192.168.43.22
- ubuntu-control: 192.168.43.11
and for the centos/7 servers,
- centos-node-1: 192.168.43.61
- centos-node-2: 192.168.43.62
- centos-control: 192.168.43.51

To confirm your IP address after connecting with your VM, run `ip addr show` or `ip a` for short.

You may include `sudo` in your commands where ever necessary.

## Attributions
- [IAMGINI](https://github.com/iamgini/vagrant-iac-usecases/blob/master/virtualbox-ansible-lab/Vagrantfile)
- [Sulaiman Adamu](https://medium.com/@sulaimanadamu_57075/how-to-create-ubuntu-and-centos-servers-on-vagrant-and-ssh-into-them-55d9b3e9e938)
