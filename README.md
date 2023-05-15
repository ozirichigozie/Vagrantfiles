# Building Virtual Machines for Home Lab

Learning how to set up a home lab where you can practice System Admin, Cloud, and DevOps engineering exercises is essential to your growth as a software engineer. Although, you may use the platforms of Cloud service providers for practice; for example,
 
- [Amazon Web Service](https://aws.amazon.com) (AWS) gives you a 'Free Tier' account where you can get hands-on within a limited number of their services for free.
- [Google Cloud Platform](https://cloud.google.com) (GCP) gives you a $300 free credit on signup to use their services for 30 days.
- On sign-up, you receive a $200 free credit to use the services of [Microsoft Azure](https://azure.microsoft.com) for 30 days.
- Using the promo code of any of Linode’s YouTube influencers, you could get up to a $100 free credit to use the services of [Linode](https://www.linode.com) when you signup. 
__________________________________

In your learning journey, creating an account with each of these cloud providers would help increase your exposure and practical knowledge of these platforms. However, setting up a home lab on your local machine will enable you

- greatly minimize cost especially since you are not yet earning from your practice, 
- practice more often without the fear of accruing the cost of using cloud services.
______________________________

That said, let us dive into the main purpose of this article.
______________________________
![cover-photo](images/cover-photo.png)
_________________________________

## PREREQUISITES
To follow this tutorial, you would need
- a Personal Computer,
- [VirtualBox](https://www.virtualbox.org),
- [Vagrant](https://developer.hashicorp.com/vagrant/docs/installation) by Hashicorp,
- a stable internet connection, and
- background knowledge of Vagrant, Linux OS commands, and working with the terminal.

These [vagrant files](https://github.com/ozirichigozie/Vagrantfiles.git) are customised to create 3 Virtual Machines consisting of
- two host nodes and
- one control node
_______________________________________

## Take note of the following:
- Virtual Box is the virtual machine provider used here, so you need to have it already installed.
- This repository offers two identical Vagrantfiles for spinning up ubuntu/focal64, and centos/7 virtual machines. However, you may change it to whatever Linux Distribution of your choice.
- The network configuration is set in such a way that an ssh connection between the centos/7 and ubuntu/focal64 virtual machines will be allowed.
_______________________________________

### Starting your VMs
Once you have your Vagrantfile copied to a designated directory — you may clone this repository by executing the following commands:

```
mkdir vagrantfiles_for_homelab
cd vagrantfiles_for_homelab
git clone https://github.com/ozirichigozie/Vagrantfiles.git .
```

 Say we want to spin up the Ubuntu VMs, we execute the commands below consecutively

```
cd ubuntu-focal64
vagrant init
vagrant up
vagrant ssh ubuntu-control
```
>
> The `ubuntu-control` node is used as an example in the code block above.
>
_______________________________________

### SSHD Configuration
To enable ssh connection, go to the /etc/ssh/sshd_config files of each node you wish to connect to and edit accordingly. Here I am going to be using the Vim text editor. You may use Nano if you want.
`sudo vim /etc/ssh/sshd_config`

and change the following parameters to positive:
 1. PermitRootLogin
 2. PubKeyAuthentication
 3. PasswordAuthentication
As shown below'

```
...
#LoginGraceTime 2m
PermitRootLogin yes
#StrictModes yes
...
PubKeyAuthentication yes
...
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes
#PermitEmptyPasswords no
 ```

The lines beginning with a ‘#’ are comments, and as such will have no effect on our configuration. So ensure you delete the ‘#’ character if it is found in front of any of the lines we are trying to edit. Do not alter the rest of this file to avoid running into errors. On Vim, Save and exit with ‘:wq’, or ‘CTRL+O Enter’ to save, and ‘CTRL+X’ to exit on Nano.

To integrate these changes, run 
 `sudo systemctl restart sshd`
>
> #### Note: this setup is for home labs used for practice (where security is not a concern).
>
_______________________________________
### Generate SSH Keys
Generate ssh keys on your control nodes using the `ssh-keygen` command. We use the -t flag to specify a particular key type. Usually, I prefer the ed25519 key type because it is short and neat. The RSA key type will be assigned by default if you don’t specify. You may also add a comment with the -C flag. This may be for the purpose of description but is totally optional. Therefore, we have a command line like this:
`ssh-keygen -t ed25519 -C any-optional-descriptive-comment`

This will generate a pair of private and public keys located by default at /home/vagrant/.ssh/id_ed25519 and /home/vagrant/id_ed25519.pub respectively. The default RSA key type also follows this same pattern. (The default user of Vagrant virtual machines is vagrant).
_______________________________________

### Copy SSH Keys to Host Nodes
Copy SSH Keys to Host Nodes
Then you must copy your SSH private key to the target VMs’ authorized keys file before you can ssh into your VM successfully. Execute the command below:
`ssh-copy-id -i /home/vagrant/id_ed25519 vagrant@ip.address.of.vm`

You will be prompted to enter a password if this command runs successfully. The default password for Vagrant’s virtual machines is ‘vagrant’. Enter this password, and connect to your target host node using the command: 
`ssh vagrant@ip.address.of.vm` or simply
`ssh ip.address.of.vm`
___________________________________

### Your IP Addresses
Following the construct of our Vagrantfiles, your IP addresses will be:
- ubuntu-node-1: 192.168.43.21
- ubuntu-node-2: 192.168.43.22
- ubuntu-control: 192.168.43.11
and for the centos/7 servers,
- centos-node-1: 192.168.43.61
- centos-node-2: 192.168.43.62
- centos-control: 192.168.43.51

You may adjust this if you want, but make sure they are all within the same network range. For example, if you set the IP of your Ubuntu VMs to 192.168.43.xx and that of CentOS to 10.15.29.xx, you may be unable to connect from the Ubuntu to the CentOS servers and vice versa successfully via ssh without needing to do further network configurations which is beyond the scope of this article.

To confirm your IP address after connecting to your VM, run `ip addr show` or `ip a` for short.

You may include `sudo` in your commands where ever necessary.
__________________________________

## Conclusion
Now that you have successfully set up your home lab, you can practice as much as you want with no charges. Moving forward, you should learn more about Vagrant commands such as 
- `vagrant reload`: to reload your VMs when necessary,
- `vagrant halt`: to gracefully shutdown your VMs, and so on.

See you at the top!
_________________________________

## Attributions
- [IAMGINI](https://github.com/iamgini/vagrant-iac-usecases/blob/master/virtualbox-ansible-lab/Vagrantfile)
- [Sulaiman Adamu](https://medium.com/@sulaimanadamu_57075/how-to-create-ubuntu-and-centos-servers-on-vagrant-and-ssh-into-them-55d9b3e9e938)
