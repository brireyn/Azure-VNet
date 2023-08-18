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
 
![addsubnet](https://github.com/brireyn/Azure-VNet/assets/96150916/a6972497-28fa-4e2c-adbe-d45dfd87c250)

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

From appvm01 SSH terminal run: nc -vz webvm01 22   (this creates a client-server connection to listen on port 22) 

![VM_connection](https://github.com/brireyn/Azure-VNet/assets/96150916/78791886-fca3-46b5-81c9-265ee9b7c89a)

Step 6: Create the Security Groups - Create the first Security group named application-nsg
![image](https://github.com/brireyn/Azure-VNet/assets/96150916/2fa91ee5-9ef4-435e-80cd-a8cc09117934)

Inside the Network Security Group Overview - Now configure the Inbound security rules for the allowed and denied traffic protocols. Note the priority assigned, in Azure two NSG rules in the same direction (inbound) cannot have the same priority value. 

![app_security_group1](https://github.com/brireyn/Azure-VNet/assets/96150916/a8f8ea6c-2a5c-4227-a514-019f413c0f0d)

![app_inbound_rules](https://github.com/brireyn/Azure-VNet/assets/96150916/07bf5014-51a7-4921-ba88-d06920ac8734)

![app_inbound_2](https://github.com/brireyn/Azure-VNet/assets/96150916/08c7d1d1-8ee4-4fc0-820a-447697480ba6)

Add a 2nd Inbound Rule for the Frontend:
![frontend_inboundrule](https://github.com/brireyn/Azure-VNet/assets/96150916/cea2e971-1b05-4e09-bb03-7e1ffdbab476)

*Frontend Security Rule Successful* 
![successful_inbound](https://github.com/brireyn/Azure-VNet/assets/96150916/813bc07b-03db-4198-972e-c7489fb3c476)

![tcp8080_inboundrule](https://github.com/brireyn/Azure-VNet/assets/96150916/131bff13-1b28-45a6-9ce8-5c50428f4e2d)
The TCP port 8080 rule is to allow the specific communication needed from frontend towards the backend subnets.


Now add the last inbound rule for application-nsg  (should have 3 inbound security rules created )
![last_inbound_rule](https://github.com/brireyn/Azure-VNet/assets/96150916/ed51dc00-25bc-42f9-9b09-abbb0986dd52)

![overview_inboundrule](https://github.com/brireyn/Azure-VNet/assets/96150916/cc62c483-89a4-4f72-99ad-677d9ec60821)

Step 7:  Now Associate the 3 subnets to the application-nsg  (repeat this step 3 times) under Settings -> Subnets -> Assoicate Subnet:
![associated_subnet1](https://github.com/brireyn/Azure-VNet/assets/96150916/6708008c-e5c0-4d01-913d-8b3dc6bc139d)

Under Virtual Networks, the Frontend Subnet and Backend Subnet and Azure Bastion subnet should look like this:

![virtualnetworks](https://github.com/brireyn/Azure-VNet/assets/96150916/0f0918d4-8975-4438-bb2f-299db196cd8c)

![subnets](https://github.com/brireyn/Azure-VNet/assets/96150916/c1b773c4-93d7-4c99-a198-b78b81d927f0)

Step 8: Validate Connectivity and Evaluate Effective Security Rules:
![step8](https://github.com/brireyn/Azure-VNet/assets/96150916/a3cb6035-066e-4a0a-a3e8-e31ffd0d5efe)





























<br />


<br />
 <br/>

</p>

