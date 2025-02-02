<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Observe ICMP Traffic
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic


<h2>Actions and Observations</h2>


 1.) The first thing we are going to do is create a resource group so we can put both of our virtual machines in. Once we have our resource group made we then want to make our first virtual machine. The first virtual machine we are going to make is a Windows 10 vm. Select the resource you made, and then name the virtual machine VM1. Make sure you select Windows 10 Pro, version 22H as the operating system. As for the size of the machine we are going to want atleast 2 vcpus, and 16 gb of memory. Create a username and password of your choosing, and keep the inbound port rules as the default options.

<p>
<img src="https://imgur.com/WgPD275.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  

<p>
<img src="https://imgur.com/X6ZMTJG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
2.) After this step we are going to click on next until we get to the networking page and it should automatically create a virtual network and subnet for us. 
  

<p>
<img src="https://imgur.com/XzdSPoR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
  Click review and create our VM.
  
  Now that we have created our first VM we are going to go ahead and create our second VM, but this time it will be a Ubuntu Server 20.04 LTS machine. It will be the same process as creating our first machine but instead we are going to switch the SSH public key to password instead. 
  
<p>
<img src="https://imgur.com/0KT3Fmb.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
<p>
<img src="https://imgur.com/pyxsHfF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
  Click next until we get to the networking page again.
  
  The networking should automatically give us the virtual network from VM1 as well as the subnet. 
  
<p>
<img src="https://imgur.com/3fQXRcw.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
 
 Click review and create, and it will create our second VM.
 
 2.) Now that we have both virtual machines up and running we are going to connect to our Windows 10 vm using the remote desktop connection app. Once we are connected we are going to go to our browser and download and install Wireshark.
 
 "Wireshark is a free and open-source packet analyzer. It is used for network troubleshooting, analysis, software and communications protocol development, and education." 
 
 3.) Open wireshark and filter for ICMP traffic only.
 
 <p>
<img src="https://imgur.com/RrtChUe.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
 
 4.) We are going to want to retrieve the private IP address of our Ubuntu VM and then attempt to ping it from within our Windows 10 VM using wireshark. To ping the private IP address of the Ubuntu machine open CMD or Powershell on the Windows machine and type: ping 10.0.0.5 or whatever the private IP address is for your Ubuntu machine.
 
<p>
<img src="https://imgur.com/zmJzyne.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
 
<p>
<img src="https://imgur.com/pp4eZdK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
 
 In either CMD or Powershell ping www.google.com and observe the traffic in wireshark.
 
5.) We then are going to initiate a non-stop ping from our Windows 10 VM to our Ubuntu VM.
 
6.) Open the Network Security Group of our Ubuntu machine and disable incoming (inbound) ICMP traffic. To disable incoming ICMP traffic click "Add" new rule and copy everything exactly from the picture. Once that is done you can create the rule and it will create automatically and show up as a new rule.
 
 <p>
<img src="https://imgur.com/r3dH3Yy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
 
<p>
<img src="https://imgur.com/qiSIrsX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
 
 Now that we have disabled incoming ICMP traffic from VM2 if we go back to VM1 you can see the ping request is timing out. 
 
 7.) Re-enable ICMP traffic for the Network Security Group your Ubuntu VM is using
Back in the Windows 10 VM, observe the ICMP traffic in WireShark and the command line Ping activity (should start working)
Stop the ping activity
 
 8.) The next thing we are going to do is Observe SSH Traffic.
 
 

### Observing SSH Traffic

1.) Open Wireshark and set a filter for SSH traffic (`tcp.port == 22`).

2.) Retrieve the private IP address of the Ubuntu VM and attempt to establish an SSH connection from the Windows 10 VM.
   
   ```bash
   ssh username@10.0.0.5
   ```

3.) Observe the SSH handshake and encrypted session in Wireshark.

4.) Disconnect the SSH session and disable inbound SSH traffic from the Network Security Group of the Ubuntu VM.

5.) Attempt to reconnect via SSH from Windows 10; observe the failed connection attempt in Wireshark.

6.) Re-enable inbound SSH traffic and confirm that the SSH connection can be re-established.

### Observing DHCP Traffic

1.) Set a filter in Wireshark for DHCP traffic (`bootp`).

2.) Renew the IP address on the Windows VM using the following command:
   
   ```powershell
   ipconfig /release
   ipconfig /renew
   ```

3.) Observe the DHCP Discover, Offer, Request, and Acknowledgment packets in Wireshark.

### Observing DNS Traffic

1.) Set a filter in Wireshark for DNS traffic (`dns`).

2.) From the Windows VM, run the following command to resolve a domain:
   
   ```powershell
   nslookup google.com
   ```

3.) Observe the DNS query and response packets in Wireshark.

4.) Disable outbound DNS traffic in the Network Security Group and attempt another `nslookup` command.

5.) Observe the failed DNS resolution in Wireshark.

6.) Re-enable DNS traffic and confirm successful resolution.

### Observing RDP Traffic

1.) Set a filter in Wireshark for RDP traffic (`tcp.port == 3389`).

2.) Establish a Remote Desktop Connection from a local computer to the Windows 10 VM.

3.) Observe the RDP session establishment and encrypted data transfer in Wireshark.

4.) Disable inbound RDP traffic in the Network Security Group and attempt to reconnect.

5.) Observe the failed RDP connection in Wireshark.

6.) Re-enable RDP traffic and confirm successful reconnection.

### Conclusion

This tutorial demonstrated how to monitor and analyze various types of network traffic using Wireshark, as well as how Network Security Groups can be used to control traffic between Azure Virtual Machines. By observing ICMP, SSH, DHCP, DNS, and RDP traffic, we gained insights into network behavior and security configurations in Azure.


 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
  
  
