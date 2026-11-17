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

Before inspecting network traffic, we must first install Wireshark on the Windows 10 VM. Wireshark is a network protocol analyzer (commonly referred to as a packet analyzer or packet sniffer) that allows us to capture, inspect, and analyze network traffic.

To download Wireshark, visit the official website: https://www.wireshark.org/

Once Wireshark is installed, use Remote Desktop Protocol (RDP) to connect to the Windows 10 VM. After successfully logging in, launch Wireshark.
With our Azure environment configured and Wireshark running, we're ready to begin inspecting network traffic between our Azure Virtual Machines, starting with ICMP traffic. 
</p>

<p align="center">
<img height="50%" width="50%" alt="image" src="https://github.com/user-attachments/assets/bb6562c0-2458-41d2-a1b8-05acde863c12" />
<img height="50%" width="45%" alt="image" src="https://github.com/user-attachments/assets/5b3f4228-74f2-4f8d-ad9a-360e65b71808" />

<p>
  Before diving into the packet capture, it is important to understand what ICMP (Internet Control Message Protocol) is and how it relates to Ping.

ICMP is a network-layer protocol used to help devices communicate information about network connectivity and troubleshoot network problems. Ping is a network troubleshooting tool that uses ICMP Echo Requests and Echo Replies to test connectivity between two devices.

With that understanding, let’s generate and inspect some ICMP traffic.

First, open Wireshark on the Windows 10 VM and enter ‘icmp’ into the display filter bar to isolate ICMP traffic. Next, retrieve the Linux VM's private IP address (10.0.0.5) from the Azure portal.

Once you have the IP address, open PowerShell on the Windows 10 VM and run the following command: ping 10.0.0.5

Referencing the screenshot below: The ping was successful, as the Linux VM responded to all four ICMP Echo Requests sent by the Windows 10 VM, confirming network connectivity between the two virtual machines.

We can observe the same activity in Wireshark within the purple-highlighted section of the screenshot. Wireshark captured a total of eight packets: four ICMP Echo Requests originating from the Windows VM (10.0.0.4) and four corresponding ICMP Echo Replies originating from the Linux VM (10.0.0.5).
</p>

<p align="center">
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/a58bc7a2-44e4-4816-9bce-8a932736f15e" />


<p>
  In this part of the tutorial, we will observe what happens when inbound ICMP traffic is blocked using an Azure Network Security Group (NSG) associated with the Linux VM.

Once again, we will use Wireshark to filter for ICMP traffic. However, this time, we will execute the following command in PowerShell on the Windows 10 VM: ping 10.0.0.5 -t

The ‘-t’ parameter instructs Windows to continuously send ICMP Echo Requests to the Linux VM (10.0.0.5) until the ping process is manually stopped.

Once ICMP Echo Replies begin appearing in Wireshark, go to the Azure portal and create an inbound security rule. Configure the inbound security rule to deny ICMP traffic destined for the Linux VM. After the rule is applied, observe Wireshark and the PowerShell window to see how the previously successful ping requests are affected.

To restore ICMP connectivity: Go back to the NSG in the Azure portal and delete the inbound security rule you created by selecting the trash icon. 
</p>

<p align="center">
<img src="https://i.imgur.com/5vXO75R.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p align="center">
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/5a647093-04b4-4200-8bde-6431d3f1694d" />

<p>
  Next, we will inspect SSH traffic in Wireshark to observe the network communication associated with establishing and terminating a Secure Shell (SSH) connection.

SSH is a network protocol that provides a secure, encrypted connection between two computers, allowing users to remotely access and interact with another machine through a command-line interface (CLI).

To begin, open Wireshark on the Windows 10 VM and enter ‘ssh’ into the display filter bar to isolate SSH traffic.

Next, open PowerShell or Command Prompt on the Windows VM and execute the following command: ssh labuser@10.0.0.5. This command initiates an SSH connection from the Windows 10 VM to the Ubuntu Linux VM (10.0.0.5) using the username labuser.

Shortly after pressing Enter, the command-line prompt will change to reflect that you are now interacting with the Ubuntu Linux VM remotely.
</p>

<p align="center">
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/572f992e-6113-427f-b73b-15fb39bc0280" />

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
