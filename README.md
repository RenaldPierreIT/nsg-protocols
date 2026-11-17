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
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/e165dc75-99e9-42bb-9f11-a4c8570a92b7" />

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
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/958e11c0-96ca-4ce9-bae0-1803c2873969" />

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
  To terminate the SSH session, execute the following command: exit. As shown in the Wireshark capture below, a TCP RST, ACK (Reset/Acknowledgment) packet was captured during the SSH connection termination process. This packet indicates that the TCP connection was reset rather than closed through the normal TCP termination process.
</p>

<p align="center">
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/cac302ee-a468-4c0c-85b7-aae87a0d36bd" />

<p>
  Next, let's move on to DHCP traffic and examine how IP addresses are assigned to devices on a network.

Dynamic Host Configuration Protocol (DHCP) is a network management protocol that automatically assigns IP addresses to devices when they connect to a network.

For this demonstration, we will use the Windows 10 VM to release its current DHCP-assigned IP configuration and request a new one.

First, open Wireshark on the Windows 10 VM and enter ‘dhcp’ into the display filter bar to isolate DHCP traffic. Alternatively, DHCP traffic can be identified by filtering for its associated UDP port numbers (67 & 68).

Next, open PowerShell on the Windows 10 VM and execute the following command to release its current DHCP-assigned IPv4 configuration: ipconfig /release

Once the current IP configuration has been released, execute the following command to request a new IP configuration from the DHCP server: ipconfig /renew. This prompts the Windows VM to initiate DHCP communication to obtain an IP address and other network configuration information.

As shown in the Wireshark capture below, we successfully captured all four stages of the DHCP DORA process: Discover, Offer, Request, and Acknowledgment (ACK). These packets illustrate how the Windows VM communicates with the DHCP server to obtain its IP configuration.
</p>

<p align="center">
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/03624a0d-502c-4890-b381-a12ac7a5b886" />

<p>
Next, we will inspect DNS traffic to observe how domain names are resolved into IP addresses.

The Domain Name System (DNS) is responsible for translating human-readable domain names (such as disney.com) into IP addresses that computers use to locate and communicate with other devices and services across a network.

With Wireshark open on the Windows 10 VM, enter ‘dns’ into the display filter bar to isolate DNS traffic.

Next, open PowerShell on the Windows 10 VM and execute the following command: nslookup disney.com

After pressing Enter, the Windows 10 VM sends a DNS query to its configured DNS server asking, “What is the IP address for Disney.com?” The DNS server then looks up the domain name and sends the corresponding IP address back to our machine, so it knows where to go to access Disney’s website.

As shown in the screenshot below, the DNS query returned the IP address 130.211.198.204 for disney.com. This is one of the IP addresses returned during our lookup that a client can use to establish a connection to the associated website.
</p>

<p align="center">
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/34f61dfc-664a-4a80-b3f7-2081cd5e45bf" />

<p>
  Finally, let's examine Remote Desktop Protocol (RDP) traffic and observe the network communication associated with an active remote desktop connection.

  RDP is a Microsoft protocol that allows users to remotely connect to another computer and access and interact with its desktop environment through a graphical user interface (GUI).

This is exactly what we've been doing since we first connected to the Windows 10 VM at the beginning of this tutorial!

Next, open Wireshark and apply the following display filter to isolate traffic using the default RDP port: tcp.port == 3389

TCP port 3389 is the default port used by RDP. We will use this filter rather than ‘rdp’ because tcp.port == 3389 filters traffic based on the TCP port number, regardless of whether Wireshark identifies the individual packets as RDP protocol traffic.
</p>

<p align="center">
<img width="70%" height="70%" alt="image" src="https://github.com/user-attachments/assets/88756a73-70e5-4b8b-a2c3-4298dc728a1c" />
<p>
  One thing worth noting is the continuous stream of network packets displayed in Wireshark after applying the filter. This is because an active RDP session involves ongoing communication between the remote computer (Windows 10 VM) and the client machine (PC). This communication includes screen updates, user input, and other session-related data.

Even when we're not actively interacting with the remote computer, RDP may continue exchanging network traffic to maintain the session and communicate updates. Although the continuous stream of packets may initially look like unnecessary or repetitive traffic, it's simply Wireshark capturing the ongoing network communication associated with our remote desktop session.

Think of RDP like a live video stream: even when you're not actively interacting with the computer, it may continue transmitting updates to keep the remote desktop synchronized with what you see on your local screen.
</p>
