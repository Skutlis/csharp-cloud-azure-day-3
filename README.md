# C# Cloud Azure Day 3 - TerraForm

## Learning Objectives

- To be able to create a Linux Virtual Machine on Azure using TerraForm
- Alter the configuration in the TerraForm file to change the configuration of the generated server

## Setup

1.  Terraform setup

    - Run `terraform version` to check what version you have installed.
    - Installation details can be found [here](https://developer.hashicorp.com/terraform)

2.  Azure Setup

    - Run `az version` to check what version you have installed.
    - Run `az upgrade` to upgrade your current version.
    - Installation details can be found [here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

3.  Cloud Shell

It is conventient to run Terraform from within the Cloud Shell as Terraform, Git (although you can't push without authentication) and VS Code are built

![myNames](./assets/CloudShellStartup.png)

## TerraForm HowTo

Azure's own deployment guide is here: [Deployment guide](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-terraform?tabs=azure-cli)

We are going to be closely following the way they have done it, tweaking a few settings along the way.

## Generate some SSH Keys

- Before you start you need to generate a set of ssh keys to pass to the Terraform script so that you will be able to connect to the VM via SSH after it is created. To do this open the terminal and run the following command:

```bash
ssh-keygen -t rsa -b 4096
```

- When it asks you for a filename to save it to, enter `azure-tf-server`.
- The public key that is generated by this is going to be automatically sent to the VM when we generate it.
- Do not save the files generated by this to GitHub, that would be \***\*BAD\*\***

## Change your Location!

- Go and change line 3 of the `variables.tf` file to match your region. The username value at the bottom is set to `azureadmin` which will be the name you need to login to the server with once it is up and running.

## Name your resources

- There is a function at the start of the `main.tf` file which will create a random name string (the `random_pet` part) which is used to name the Resource Group all of the rest of the parts will be assigned to. In theory this means that you can leave the names of all of the other resources as they are and they will happily be created with no name clashes happening. Whilst this is a good thing, it does mean that if all of you leave the name as it is then when you go into the Virtual Machines list there will be 50 VMs all called `myVM` and the only way to find yours will be to find the correct Resource Group.
- Instead of that you need to go through the names of the various resources in `main.tf` and find all of the ones that start `mySomething` like `"myVnet"` and `"mySubnet"` in the image below.

![myNames](assets/my-names.png)

- In each instance replace the `my` parts in the double quotes with your name so `"myVnet"` in my case would become `"daveAmesVnet"`, change the 7 instances that begin with `my` throughout the file in the same way. If you leave them with their generic names then later in the day we will go through and delete any generically named resources to save space/bandwidth.
- The rest of the settings seem pretty standard although we may have to go in manually after creating the VM and make a few changes to get everything configured. One thing you cna investigate if you wish is how to update the version of Ubuntu that is used to generate the image in Terraform and how to configure things like the Inbound rules for other ports apart from the SSH one.

## Creating the VM and Other Resources

- Once you have gone through and made the necessary changes to the Terraform files, you are ready to start running the commands to deploy your VM and the other resources.
- Make sure you have already generated your public and private keys, and that they are present in the same folder as the Terraform files are.
- Open your Terminal in the same folder and initialise Terraform with the following command:

```bash
terraform init -upgrade
```

- The upgrade option will pull in any new or updated Terraform files since the last time you ran the code.
- Next run the validate step to check for syntax errors etc.

```bash
terraform validate
```

- If all is well you can now run a command that will create all of the steps which are going to be run and write them out to a `plan` file which we can then use to actually apply the changes.

```bash
terraform plan -out main.tfplan
```

- You can check through the output in the terminal to make sure nothing looks out of place. If all is well then you can move on to applying it

```bash
terraform apply main.tfplan
```

- Once the command completes there will be some useful information output at the end in the terminal.
- Use the IP address which is generated to test if you can SSH into the VM using the private key you generated before.

```bash
ssh -i azure-tf-server azureadmin@<THE IP ADDRESS OF THE VM>
```

- If all is well you can continue on and configure the server as you see fit via the Portal in the same way you did previously as well as adding any missing configuration.

## Core

- Create the VM and its associated infrastructure by modifying the files here.
- Deploy your backend to the VM and run it so that the endpoints are available.
- Screenshot your success and post the screenshots below here to showcase your success
  
![alt text](assets/api-in-azure-vm-working.png "Azure-vm-api-working")
## Extension

- Update the Terraform to deploy a React or Angular frontend to the same VM configured so that it can talk to backend
- Screenshot and Document the various parts of your stack in action and post the results here

![alt text](assets/frontend_working.png "Azure-vm-api-working")