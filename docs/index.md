# Installation

## Requirements

- Git
- Ansible
- At least 3 VMs with the setup described in [VMs setup](#vms-setup)

## VMs setup

For the setup I used VirtualBox but it should work with any other virtualization software until your VMs have the required configuration.

### VMs requirements

This requirement are the minimum requirements for the VMs. You can use more powerful VMs if you want.

Jenkins VM:

- 2 CPU
- 2 GB RAM (2048 MB)
- 30 GB disk

Kubernetes VMs:

- 4 CPU
- 4 GB RAM (4096 MB)
- 30 GB disk

### Create the VMs

Create 1 VM for Jenkins and at least 2 VMs for the Kubernetes cluster.

#### Creating a VM with VirtualBox

- Open VirtualBox
- Click on "New"
- Name your VM
- Select your "ISO Image" (Ubuntu server 22.04.3 LTS in my case)
- Check "Skip unattended installation"

![image](images/VMSetupPartOne.png)

- Click on "Next"
- Set the memory size and CPU number according to [VMs requirements](#vms-requirements)

![image](images/VMSetupPartTwo.png)

- Click on "Next"
- Select "Create a virtual hard disk now"
- Set the disk size according to [VMs requirements](#vms-requirements)

![image](images/VMSetupPartThree.png)

- Click on "Next"
- Then click on "Finish"

#### Installing Ubuntu server

- Start your VM
- Select your language

![image](images/OSSelectLanguage.png)

- Select "Continue without updating"
- Then select "Done" until you reach the "Configure Ubuntu archive Mirror" screen

![image](images/OSConfigureUbuntuArchiveMirror.png)

- Wait to see if the mirror location pass the test
- If the test pass, select "Done", otherwise you may have an issue with your internet connection
- Select "Done" until you reach the "Storage configuration" screen
- Select "Done" and then "Continue" on the "Confirm destructive action" screen

You should now be on the "Profile setup" screen.

- Enter your name
- Enter your server name
- Enter your username (For all Kubernetes VMs, use the same username)
- Enter your password (For all Kubernetes VMs, use the same password)

![image](images/OSProfileSetup.png)

- Select "Done"
- Select "Continue" (We don't need Ubuntu Pro)
- Check "Install OpenSSH server"

![image](images/OSInstallOpenSSHServer.png)

- Select "Done"

You should now be on the "Featured server snaps" screen.

Check nothing and select "Done".

<!-- ![image](images/OSFeaturedServerSnaps.png) -->

Now wait for the installation to finish.

- Select "Reboot now"

![image](images/OSRebootNow.png)

- Wait for the VM to reboot
- Login with your username and password
- Run `shutdown -h now` to shutdown the VM correctly

- Now go to the VM settings
- Go to "Network"
- And change the "Attached to" to "Bridged Adapter"

![image](images/VMSetupNetwork.png)

- Start the VM
- Login with your username and password
- Run `ip -c a` to get the IP address of your VM

![image](images/OSGetIp.png)

Now repeat the same steps for the other VMs.

## Setup the VMs

Now clone the repository on your host machine.

At the root of the repository, you should have a `deployAll.sh` script.

Run this script with the IP addresses of your Kubernetes VMs as arguments (not the Jenkins VM, you will be prompted for it later).

```bash
./deployAll.sh <kubernetes-vm-ip-1> <kubernetes-vm-ip-2> ... <kubernetes-vm-ip-n>
```

This script will run two sub-scripts:

- `deployKubespray.sh` wich will setup Kubernetes on the Kubernetes VMs
- `deployJenkins.sh` wich will setup Jenkins on the Jenkins VM

The `deployKubespray.sh` script will clone the kubespray repository and run the `reset.yml` (to clear any previous install of kubernetes) then run the `cluster.yml` (to install kubernetes) playbook with the IP addresses of the Kubernetes VMs as arguments.

The `deployJenkins.sh` script will prompt you for all needed information to setup Jenkins and then use the `jenkins.yml` playbook to install Jenkins on the VM.

Once `deployAll.sh` is finished, all the VMs should be ready to use.

If you want to reinstall Jenkins, you can run the `deployJenkins.sh` script again.

!!! warning
    If you want to reinstall Kubernetes, you will have to run the `deployAll.sh` script since Jenkins needs a file from kubernetes to connect to the kubernetes cluster.
