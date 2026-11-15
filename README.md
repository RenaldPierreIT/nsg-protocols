<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop (RDP)
- Various Command-Line Tools
- Various Network Protocols (ICMP, SSH, DHCP, DNS, RDP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Step 1: Install Wireshark 
- Step 2: Inspect ICMP Traffic 
- Step 3: Configure Network Security Groups 
- Step 4: Inspect SSH Traffic 
- Step 5: Inspect DHCP Traffic
- Step 6: Inspect DNS Traffic
- Step 7: Inspect RDP Traffic

<h2>Actions and Observations</h2>

<p>
Before beginning this tutorial, it is assumed that you have the following Azure resources already configured:

- A free Azure account (tenant) and an active Azure subscription.
- A Resource Group (RG) created within Azure.
- A Virtual Network (VNet) with at least one subnet configured within the Resource Group.
- Two Virtual Machines (VMs) deployed and connected to the VNet:
  - One running Windows 10.
  - One running Ubuntu Linux.

Before inspecting network traffic, we must first install Wireshark on the Windows VM. Wireshark is a network protocol analyzer (commonly referred to as a packet analyzer or packet sniffer) that allows us to capture, inspect, and analyze network traffic.

To download Wireshark, visit the official website: https://www.wireshark.org/

Once Wireshark is installed, use Remote Desktop Protocol (RDP) to connect to the Windows VM. After successfully logging in, launch Wireshark.
With our Azure environment configured and Wireshark running, we're ready to begin inspecting network traffic between our Azure Virtual Machines, starting with ICMP traffic. 
</p>

<p align="center">
<img height="50%" width="50%" alt="image" src="https://github.com/user-attachments/assets/bb6562c0-2458-41d2-a1b8-05acde863c12" />
<img height="50%" width="45%" alt="image" src="https://github.com/user-attachments/assets/5b3f4228-74f2-4f8d-ad9a-360e65b71808" />

<p align="center">
<img src="https://i.imgur.com/IIUShxp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
  We can inspect each individual packet and see the actual data that is being sent in each ping. the picture below demonstrates just that. 
</p>
<br />

<img src="https://i.imgur.com/GLxSIG3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
  In the next portion of the lab we will perpetually ping the Linux machine with the command ping -t. This will continually ping the machine until we decide to stop it, while the Windows machine is pinging the Linux machine we will go to the Linux machine and block inbound ICMP traffic on it's firewall. Once we do that we will stop recieving echo replys from the Linux machine. We will block ICMP by creating a new Network Security Group on the Linux machine that will be set to block ICMP. We can allow the traffic by allowing ICMP on the Linux Network Security Groups page on Azure. 
</p>
<br />

<img src="https://i.imgur.com/5vXO75R.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<img src="https://i.imgur.com/Asl80tN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
  Next we will use our Windows machine to SSH to the Linux machine. SSH has no GUI it just gives the user access to the machines CLI. We will set the wireshark filter to capture SSH packets only. When we ssh into the Linux machine with the command prompt "ssh labuser@10.0.0.5" we can see that wireshark starts to immediately capture SSH packets.
</p>
<br />

<img src="https://i.imgur.com/zteR41r.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
  Now we will use wireshark to filter for DHCP. DHCP is the Dynamic Host Configuration Protocol this works on ports 67/68. It is used to assign IP addresses to machines. We will request a new ip address with the command "ipconfig /renew". Once we enter the command wireshark will capture DHCP traffic.
</p>
<br />

<img src="https://i.imgur.com/vU8fpQf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
  Time to filter DNS traffic. We will set wireshark to filter DNS traffic. We will initiate DNS traffic by typing in the command "nslookup www.google.com" this command essentially asks our DNS server what is google's IP address.
</p>
<br />

<img src="https://i.imgur.com/VMcwmsO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
  Lastly we will filter for RDP traffic. When we enter tcp.port==3389 traffic is spammed non stop because we are using Remote Desktop Protocol to connect to our Virtual Machine. 
</p>
<br />

<img src="https://i.imgur.com/VxXGv6X.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
