# Azure-VNet
A lab created to practice securing network access to Azure Virtual Networks using NSGs, Azure Firewall, and Azure Bastion. 


<h1>Securing VNet Access in Azure</h1>

 

<h2>Description</h2>
This lab tutorial is created to practice securing network access to Azure Virtual Networks using NSGs, Azure Firewall, Virtual Machines, and Azure Bastion. The two VMs consist of a web frontend and a backend. The end goal is to have a well secured environment with only the necessory open ports and protocols to aid in creating a zero-trust architecture.
<br></br>

Here is a Diagram of the Virtual Network Architecture:

<b> ![diagram](https://github.com/brireyn/Azure-VNet/assets/96150916/edc7b9b5-bcdc-4f91-b117-20a3e8ce8a45)</b>
-<b> </b>
Here there are two Subnets, the Public Subnet contains the Bastion host. Each subnet contains a security group as well as a security group for the bastion. The lab demonstrates the white and blacklisting of different protocols for inbound and outbound security group rules.

<h2>Languages and Utilities Used</h2>

- <b>Linux</b> 
- <b>SSH</b>

<h2>Environments Used </h2>

- <b>Windows 10</b>
- <b>Microsoft Azure</b>

<h2>Program walk-through:</h2>

<p align="center">
First, create a Bastion: <br/>

![step1](https://github.com/brireyn/Azure-VNet/assets/96150916/7748ef5c-3d8a-4beb-8959-fda25b4d4c20)

![bastion-created](https://github.com/brireyn/Azure-VNet/assets/96150916/326a3783-9196-43a2-9ca9-e5c67f6c2137) 
<b>


![step2](https://github.com/brireyn/Azure-VNet/assets/96150916/e707e765-7106-4fca-89ef-7510c574d326)

Step2: 
Now set up the Virtual Network and the subnet. Make sure when using an Azure Bastion Subnet to use a prefix of at least /26 for the netmask. 

 Step3: 
 
![step3](https://github.com/brireyn/Azure-VNet/assets/96150916/bdf93c40-c246-4fe7-8c80-b98ca11b4c91)
For the first subnet, create a Subnet for the Azure Bastion with a subnet mask of /25. Bastion hosts reside in the Public Subnet. 
<br>

Step 4:
After creating the Bastion and the Virtual Network, check that it works correctly by SSH into the appvm01 (VM) using Azure Bastion and the downloaded Private Key-pair which is the id_rsa file. (The id_rsa.pub is the public key). Go to Virtual machines:

![connect_to_first_vm](https://github.com/brireyn/Azure-VNet/assets/96150916/2328d89d-5c07-46de-bebc-4f1ec907649a)

![connect_bastion_1vm](https://github.com/brireyn/Azure-VNet/assets/96150916/25a4011c-06b6-4c09-919e-c93152bdb4c6)

![step4](https://github.com/brireyn/Azure-VNet/assets/96150916/1f3be00b-f9a3-44a7-83ce-479d91b23a7f)

Once Connected you should be inside of <bold>appvm01</bold> Virtual Machine : ![image](https://github.com/brireyn/Azure-VNet/assets/96150916/151b4ebc-28f8-43fc-8b51-ada9fd9961ec)

Step 5:  Restrict Access with Network Security Groups (NSGs)

The frontend VM has a public IP and is vulnerable to attacks by being open to the internet. Now to restrict network access using NSGs follwing the zero-trust principle. To allow the frontend servers to access the backend servers on HTTP port 8080, the frontend servers need to be acessible over the Internet on HTTPS port 443. 

From appvm01 SSH terminal run: nc -vz webvm01 22 













<br />


<br />
 <br/>

</p>

