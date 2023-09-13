# Azure-VNet
A lab tutorial demonstrating securing network access to Azure Virtual Networks using NSGs, Azure Firewall, and Azure Bastion. 


<h1>Securing VNet Access in Azure</h1>

 

<h2>Description</h2>
This lab tutorial demonstrates securing network access to Azure Virtual Networks using NSGs, Azure Firewall, Virtual Machines, and Azure Bastion. The two VMs consist of a web frontend and a backend. The end goal displays a secured environment with only the necessory open ports and protocols to aid in creating a zero-trust architecture.
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

Under Virtual Networks, the Frontend Subnet and Backend Subnet should look like this:

![virtualnetworks](https://github.com/brireyn/Azure-VNet/assets/96150916/0f0918d4-8975-4438-bb2f-299db196cd8c)

![subnets](https://github.com/brireyn/Azure-VNet/assets/96150916/c1b773c4-93d7-4c99-a198-b78b81d927f0)

Note the rules with priority values 100, 110, and 120, which allow specific communications needed.
![security-rules](https://github.com/brireyn/Azure-VNet/assets/96150916/3440dead-cad1-43c6-8ccc-c35c40734fde)

Step 8: Validate Connectivity and Evaluate Effective Security Rules:
![step8](https://github.com/brireyn/Azure-VNet/assets/96150916/a3cb6035-066e-4a0a-a3e8-e31ffd0d5efe)

-In the Azure Portal go to webvm01 then in the Networking sub group - notate the Public IP address , then click Connect and select Bastion.
-Use the SSH private key to connect to the Bastion VM.

-When using the nc -vz webvm01 22 -w 3 command, there is a time out when trying to connect to port 22 tcp as the inbound rule now blocks the VMs from accessing one another on port 22.

![appvm01_nowork](https://github.com/brireyn/Azure-VNet/assets/96150916/47d92440-5c91-47de-8775-b7a448a8bb48)

-Start a simple web server on port 8080 in the appvm01 
![webserver](https://github.com/brireyn/Azure-VNet/assets/96150916/9f0f7142-d22d-44c4-aed2-3bc3f4525ae1)

- Go to webvm01 again, and run the command: nc -vz appvm01 8080 -w 3
  The output should be: Connecion to appvm01 8080 port [tcp/http-alt] succeeded!
  
- Back in the appvm01 Cntrl + C to termincate the web appliation and run the command: python -m SimpleHTTPServer 8888
- In the webvm01 SSH terminal repeat the connectivity test (nc -vz appvm01 8888 -w 3) The connectivity should time out for 3 seconds, even though the server is running on that port, our NSGs are now blocking all inbound traffic to appvm01 except for TCP port 8080 from the frontend subnet.

- To test for restricted inbound traffic from the public internet on webvm01, start python web server on port 443 using command: sudo python -m SimpleHTTPServer 443

-Next, in a browser tab serach for the Public IP of webvm01 that was copied earlier and append :443 on the end to connect to the server domain and see that the server is working for HTTPS traffic.
![443](https://github.com/brireyn/Azure-VNet/assets/96150916/da22736a-2d85-4564-b4b4-76fede5e8724)

The connection was validated showing the python web server. ![pythonserver](https://github.com/brireyn/Azure-VNet/assets/96150916/ccdd68da-883f-4ea1-b053-3dbd306aac4c)

Step 9:
Go to webvm01 -> Networking 
![step9-webserver](https://github.com/brireyn/Azure-VNet/assets/96150916/76d05470-a014-4707-baba-2082dbd0e520)

Click Effective Security rules in the middle of the page.
![effective_sec_rules](https://github.com/brireyn/Azure-VNet/assets/96150916/c7bb826e-4441-42e0-83ee-744cd8e9ff34)
![step9_rules](https://github.com/brireyn/Azure-VNet/assets/96150916/9d1ecc89-2a07-4923-8a1c-e51b6add4c6d)
Click to expand the full list
![effective_sec_rules_exp](https://github.com/brireyn/Azure-VNet/assets/96150916/e76746b5-b07d-4352-9828-429735b5166d)

webvm01 application-nsg  Inbound and outbound rules
![application-nsg-rules](https://github.com/brireyn/Azure-VNet/assets/96150916/65bc8759-6b88-4eff-b21f-bc588dc31d3e)


Step 10: Secure outbound traffic with Azure Firewall to secure access using FQDN filtering: 

![azure_firewall_diagram](https://github.com/brireyn/Azure-VNet/assets/96150916/0a208f4c-306f-42cc-8f0b-ebd9a4b6deab)


![step10](https://github.com/brireyn/Azure-VNet/assets/96150916/d1274732-8046-4981-af4e-e5b6f9ea0fef)

Create a subnet for the Firewall 
![firewall_subnet](https://github.com/brireyn/Azure-VNet/assets/96150916/14bd219d-af1b-4aac-b000-4a2b2a52c36c)

Next go to Firewalls -> Create a firewall:
![create_firewall](https://github.com/brireyn/Azure-VNet/assets/96150916/3935abc4-32d2-4858-9a65-3969ffc8669f)

![create_firewall2](https://github.com/brireyn/Azure-VNet/assets/96150916/d1dad0c7-4bc3-4698-b8e6-e2d11e91e004)

Step 11: Create the Default Route table for the subnets so that all internet-bound traffic goes through the Firewall. 

![create_route](https://github.com/brireyn/Azure-VNet/assets/96150916/c0d41840-9347-4337-b936-4a640b7628ab)

![firewall-ip](https://github.com/brireyn/Azure-VNet/assets/96150916/25709d79-84eb-42ce-9a6f-7fc719a73271)


![firewall_add_route](https://github.com/brireyn/Azure-VNet/assets/96150916/aaaf8a66-9a0f-4391-8670-b08b81351647)

![successful_route_fire](https://github.com/brireyn/Azure-VNet/assets/96150916/516fbd37-dcc8-4c0a-9f5e-1cb88980c821)

Under the azufw-policy -> Rule Collections go to Add -> Rule collection
![rule_collections](https://github.com/brireyn/Azure-VNet/assets/96150916/712b2bd0-a9d1-4619-a17f-ab7bdf81b867)

Add the FQDN rule to the firewall
![Add_rule_FQDN](https://github.com/brireyn/Azure-VNet/assets/96150916/fb1fe023-72f6-4a1f-a820-ca78e9b9bb00)

Step 12: Test the VM connection in webvm01 with the wget google.com command
![verify_vm](https://github.com/brireyn/Azure-VNet/assets/96150916/74fe73ba-a55e-4448-964e-c9ae362f8416)

This succeeds with a 200 OK message in the output and the google front page should be downloaded to the VM. Next run the following: 

![final](https://github.com/brireyn/Azure-VNet/assets/96150916/ae735e3f-0104-453f-ab3d-328d2daa6ded)
![final2](https://github.com/brireyn/Azure-VNet/assets/96150916/bff56ffe-5128-4044-a6a1-b5f24d26e376)

Since the rule with the ‘wildcard’ *.googleapis.com, every FQDN that is a subdomain of googleapis.com will be accessible.

Now secured connections to outside networks and the environment is now less susceptible to downloading malicious software. 



































<br />


<br />
 <br/>

</p>

