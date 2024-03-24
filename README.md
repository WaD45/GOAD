# GOAD

# Install your own Active Directory lab

## What you will learn in this course 🧐🧐

If you want to practice your pentest skills on a real-life Active Directory, you may find it challenging to set up a local lab on your machine. This is because even a small lab demands significant storage and computing power to run several VMs simultaneously (in addition to your Kali). Therefore, in this course, we'll introduce a method to construct a cloud-based AD lab, enabling you to efficiently hone your new skills.

## Pre-requisites

### Create your Azure account

Go to this [page](https://azure.microsoft.com/en-us/free) and click on _Start free_. Follow the instructions to create your account.

<Note type="note" title="Azure Free Tier">

If you've never created an Azure free account, you'll receive 12 months free for most basic services, plus an additional \$200 credit. **If you already have an Azure account, simply use another email address that's not associated with one 😉.**

Also, **Azure will request your credit card details**. While this may seem daunting, depending on the AD lab configuration you choose, you might expend between \$12 to \$30 daily. Your complimentary credits should sufficiently cover these costs, especially since certain resources are part of the 12-month free tier. **Thus, you shouldn't incur any charges**.

By the end of this course, we'll guide you on how to terminate all Azure resources. If you're seeking added assurance, consider checking out [how to set a cost alert in Azure](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending).

</Note>

Once it is done, you should be able to access the Azure portal.

### Upgrade your Azure account

To deploy the Active Directory lab, you'll need to upgrade your Azure account to a Pay-As-You-Go subscription. This is because the free tier doesn't support the required resources.

Don't worry, you won't be charged for this upgrade. It merely allows you to access the necessary resources for the lab. And you can still use your \$200 credit.

Here is how to upgrade your account:

- In the search bar, type _subscription_ and select _Subscriptions_.

![Search for Subscription](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/upgrade-account-1.png)

- In the next window, click on your subscription. Then, click on _Upgrade your subscription_ in the banner at the top.

![Upgrade Subscription](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/upgrade-account-2.png)

- Select the Basic plan and click on _Upgrade to pay-as-you-go_. It will ask you to confirm the your creduit card details. Don't worry, you will still have your \$200 credit.

![Upgrade to Pay-As-You-Go](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/upgrade-account-3.png)

Okay! You upgraded your account successfully.

From here you have two paths:

- Either you want to run everything from your machine.
- Or – and we strongly recommend this, you want to run everything from the Azure shell command.

If you are up to run everything from your machine, you have to follow the next sections in order to install the necessary tools: Azure CLI, Terraform and Git.

### Install Azure CLI

We need to install the Azure CLI to interact with Azure services. You can find the installation instructions [here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

Or you can follow the instructions below.

#### For Windows users

We recommend you to download the MSI package from the [Azure CLI download page](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest&tabs=azure-cli).

Double click on the downloaded file and select yes if it asks for permission. Follow the instructions on the screen to install the Azure CLI.

#### For MacOS users

We recommend to use Homebrew to install the Azure CLI. If you don't have Homebrew installed, you can find the installation instructions [here](https://brew.sh/) or simply copy and paste:

```bash
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then to install the Azure CLI, run:

```bash
$ brew update && brew install azure-cli
```

#### For Linux users

<Note type="warning">

Depending on your Linux system you may need to install the Azure CLI using different methods. Please refer to the [official documentation](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?view=azure-cli-latest&pivots=apt) for more information.

</Note>

The Azure CLI has been tested for: Ubuntu 20.04 LTS (Focal Fossa), Ubuntu 22.04 (Jammy Jellyfish), Debian 10 (Buster), Debian 11 (Bullseye) and Debian 12 (Bookworm). If you are not using one of these, we cannot guarantee the following steps will work for you.

A simple option the install Azure CLI is to run:

```bash
$ curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### Install Terraform

Terraform is a potent tool designed to replace the conventional GUI from Azure (and from other cloud providers) with code. This ensures that instead of manually clicking on multiple resources (a process that can't be automated), you simply draft a Terraform script that automatically sets up your entire cloud environment.

We will Terraform to deploy the virtual machines to Azure cloud.

#### For Windows users

You need to go to [Terraform website](https://developer.hashicorp.com/terraform/install#windows), download the binary and install it manually.

#### For MacOS users

You should have Homebrew installed from the previous step.

You only need to run:

```bash
$ brew tap hashicorp/tap
$ brew install hashicorp/tap/terraform
```

#### For Linux users

Depending on your distribution you can [check the documentation](https://developer.hashicorp.com/terraform/install#linux).

For most of Ubuntu and Debian, you can run:

```bash
$ wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
$ echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
$ sudo apt update && sudo apt install terraform
```

### Install Git

Check if Git is setup by running (in a bash shell preferably):

```bash
$ git version
```

If you have an output like `git version 2.39.3` you are good to go. Otherwise follow the steps for your appropriate distribution below.

#### For Windows users

The optimal approach to install `git` on your PC is via the [Git installer for Windows](https://gitforwindows.org/).

Re-run the `git --version` command and check the output.

#### For MacOS users

The most efficient way to install `git` is to execute:

```bash
$ brew install git
```

### Wrapping up

Once you are done with this section you should be able to have:

<Note type="note">

The versions may differs. Do not worry about that.

</Note>

- You Azure account with free tier available
- Azure CLI

```bash
$ az --version
azure-cli                         2.58.0

core                              2.58.0
telemetry                          1.1.0
...
```

- Terraform

```bash
$ terraform --version
Terraform v1.7.5
on xxx
```

- Git

```bash
$ git version
git version 2.39.3
```

Check if all these commands gives you similar outputs. We have all are dependencies ready to create our AD lab in the cloud! 🚀

## Install your Active Directory lab

We will use a Github Repository named [GOAD](https://github.com/Orange-Cyberdefense/GOAD) (Game Of Active Directory) as the foundation for our Active Directory. We will use the Light version of the lab to keep it simple.

The infrastructure will resemble:

![Azure Architecture](https://github.com/Orange-Cyberdefense/GOAD/raw/main/docs/img/GOAD-Light_schema.png)

In this section we consider that you are using the Azure shell command. If you will to use your machine the steps are similar, start from _Clone your project_.

### Open the Azure shell

In your Azure dashboard you can start a new Azure shell by clicking on the icon in the top right corner:

![Azure Shell](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/azure-shell-1.png)

- Select _Bash_ as the shell type:

![Azure Shell Type](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/azure-shell-2.png)

- Click on _Create storage_:

![Azure Shell Storage](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/azure-shell-3.png)

Here is your Azure shell:

![Azure Shell](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/azure-shell-4.png)

You will run every following commands in this shell from now on!

### Clone the project

<Note type="warning">

If you are on Windows you have to use [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) and the bash shell.

</Note>

First, clone the repository to your local computer:

1. Navigate to the folder where you want the repository (e.g., `Downloads`, `Desktop`, `Documents`).
2. Clone the repository using:

```bash
$ git clone https://github.com/Orange-Cyberdefense/GOAD
```

3. Enter the repository directory:

```bash
$ cd GOAD
```

4. Modify the file `./ad/GOAD-Light/providers/azure/terraform/variables.tf` so as to change:

```
variable "location" {
  type    = string
  default = "westeurope"
}
```

to:

```
variable "location" {
  type    = string
  default = "westus"
}
```

You can use `nano` or `vim` to edit the file:

```bash
$ nano ./ad/GOAD-Light/providers/azure/terraform/variables.tf
```

### Deploy to Azure

Next step is to deploy our lab to our Azure cloud.

Follow these steps:

1. (Optional if you are in the Azure shell) Log in to Azure: `az login`. A browser window will pop up for authentication. Once logged in, close the window.
2. Let's check if everything is ready:

```bash
$ ./goad.sh -t check -l GOAD-Light -p azure
[✓] Task: check
[✓] Lab: GOAD-Light
[✓] Provider: azure
[✓] folder ad/GOAD-Light/providers/azure found
[✓] Launch check : ./scripts/check.sh azure local
[+] Enumerating azure
  [✓] azure is installed
  [✓] terraform was found in your PATH
  [✓] rsync was found in your PATH
[✓] Check is ok, you can start the installation
```

3. Deploy the directory:

```bash
$ ./goad.sh -t install -l GOAD-Light -p azure
```

The installation process will start, typically taking between 15 to 30 minutes. During this, you'll need to confirm resources being allocated to your Azure account by typing `yes` in your terminal:

```bash
...

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes
```

Grab a coffee, relax, and wait for the installation to complete. It can takes up to 45 minutes. It first call Terraform to create the cloud resources, then it will use Ansible to configure the DC and the server.

At the end you should see something like:

```bash
...
dc02                       : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
srv02                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[✓] Command successfully executed
[✓] your lab is successfully setup ! have fun ;)
goad@ubuntu-jumpbox:~/GOAD/ansible$                   exit
logout
Connection to 13.64.251.160 closed.



Ubuntu jumpbox IP: 13.64.251.160
You can now connect to the jumpbox using the following command:
ssh -i ad/GOAD-Light/providers/azure/ssh_keys/ubuntu-jumpbox.pem goad@13.64.251.160



[✓] ssh/config :
Host goad_azure
    Hostname 13.64.251.160
    User goad
    Port 22
    IdentityFile /Users/ademenet/Work/Jedha/GOAD_demo/ad/GOAD-Light/providers/azure/ssh_keys/ubuntu-jumpbox.pem
```

## Connect to your jumpbox

You need a PEM key to connect to the jumpbox server. A jumpbox server is a server that is used to connect to other servers in a secure way because it is the only server to have public IP.

If you followed the previous steps using the Azure shell, you have to copy and paste the PEM key content into a new file. Here is how to do it:

- In Azure shell, type `cat ./ad/GOAD-Light/providers/azure/ssh_keys/ubuntu-jumpbox.pem` to display the content of the file.
- Copy the content of the file.
- Create a new file on your Kali machine with `nano ubuntu-jumpbox.pem` and paste the content. Do not forget to add a new line at the end of the file. Save and exit.

If you followed the previous steps on your machine, you should have the file `./ad/GOAD-Light/providers/azure/ssh_keys/ubuntu-jumpbox.pem` on your machine directly.

Now you can connect to the jumpbox server using the following command:

```bash
ssh -i ./path/to/your/pen/key/ubuntu-jumpbox.pem goad@<ubuntu_public_ip>
```

Replace `<ubuntu_public_ip>` with the IP address of the jumpbox server. You can find it in the output of the installation process or in your Azure dashboard:

![IP Retrieval](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/retrieve_IP_address.png)

You are now connected to the jumpbox server! Congratulations! 🎉

## RDP

Given that Windows is in play, you might require RDP (Remote Desktop Protocol) for VM access.

<Note type="warning">

RDP requires credentials — your task is to discover them!

</Note>

For additional security, **deploy an Azure Bastion**:

- Navigate to `goad-virtual-network` (Azure resources section).
- Select _Bastion_ and click _Deploy Bastion_.

![Bastion Deployment](https://learn.microsoft.com/en-us/azure/bastion/media/quickstart-host-portal/deploy.png)

After a 10-minute wait, return to Azure resources, access your bastion, and update its configurations to permit IP addresses.

![Configuration Update](https://learn.microsoft.com/en-us/azure/bastion/media/connect-ip-address/ip-connection.png)

To RDP into a VM, select your target VM and provide the necessary credentials.

![RDP Access](https://cyber-fullstack-assets.s3.amazonaws.com/M03-Red_Team/D10-Active_directory_Part_1/01-Exercises/RDP_into_VM.png)

<Note type="note" title="Discover the credentials">

Finding the admin/password is part of your exercise, so no hints here! 😜

</Note>

## Troubleshooting

### Can't RDP into Azure despite correct credentials?

Security Groups might be the culprit. Cloud providers often use security groups to regulate traffic to VMs. Adjust the GOAD infrastructure by configuring a Security Group manually.

Detailed steps include searching for _Security Groups_, selecting _Network Security Groups_, and creating a new group. Ensure the subscription type is _Pay-As-You-Go_ and the Resource Group is _GOAD-Light_. Name the group, finalize its creation, and associate it with the `goat-vm-subnet`. Finally, include _RDP_ and _SSH_ in the _Inbound Rules_.

Test the setup with:

```bash
$ ssh -i ssh_keys/ubuntu-jumpbox.pem goad@<ubuntu_public_ip> -D 1080
```

## AD Structure

Depending on the GOAD version chosen, your infrastructure will vary:

- **GOAD Light**:

![GOAD Light Structure](https://github.com/Orange-Cyberdefense/GOAD/blob/main/docs/img/GOAD-Light_schema.png?raw=true)

- **Full GOAD**:

![Full GOAD Structure](https://github.com/Orange-Cyberdefense/GOAD/blob/main/docs/img/GOAD_schema.png?raw=true)

The full GOAD version is more intricate.

## Cease Azure Resources and Conserve Money 💸

To avoid unforeseen charges, execute (either in Azure shell or your machine, depending on how you started):

```bash
$ ./goad.sh -t destroy -l GOAD-Light -p azure
```

Alternatively, you can also delete the resource group directly with Azure CLI:

```bash
$ az group delete --name GOAD-Light
```

You can also delete the resource group directly from the Azure portal.

This action terminates all active VMs, preventing additional costs.

Enjoy your lab experience! 😘

<Note type="warning" title="Installing the **Light version** of GOAD">

The commands above install the Light version of the GOAD Active Directory, which offers numerous functionalities.

**Interested in the full GOAD version?**

First, adjust your Azure settings to **increase the VM running limit to 12**:

1. Access your Azure portal.
2. Search for _Quota_.
3. Select _Compute_ to request an increased VM quota.
4. Opt for _Request Quota Increase_ (top-left) and request 15 VMs for good measure.
5. Allow 5-10 minutes for processing.

Then execute: `./goad.sh -t install -l GOAD -p azure`

More details on quota adjustments are available [here](https://learn.microsoft.com/en-us/azure/quotas/quickstart-increase-quota-portal).

If your quota request doesn't process, contact Azure Customer Support.

</Note>

## Resources 📚📚

- [Azure CLI](https://learn.microsoft.com/fr-fr/cli/azure/install-azure-cli-windows?tabs=powershell)
- [Terraform Installation](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
- [Homebrew Installation](https://brew.sh/)
- [VM Connection via Specific Private IP](https://learn.microsoft.com/en-us/azure/bastion/connect-ip-address)
- [Azure Bastion Quickstart](https://learn.microsoft.com/en-us/azure/bastion/quickstart-host-portal)
