# Packet-Analysis-Lab
## Analyzing packets in Linux and Windows with tcpdump and Wireshark

This project aims to leverage the strengths of tcpdump for rapid packet capture and Wireshark for in-depth traffic analysis, providing a comprehensive understanding of network behavior and security posture.



## For this simulation, I used three virtual machines – A Kali Linux VM, which served as our attacker, an Ubuntu Linux VM, and a VM running Windows 11

📝This is a safe and controlled way to simulate real-world environments and cyberattack scenarios. Using virtual machines (VMs) ensures that any malicious activity is contained and doesn’t affect your actual operating system 📝



## Staring tcpdump in Linux will show something like this information and display all packets across the network interface. These packets will continue to be captured and displayed until a command like (Ctrl+C) is input to stop the live capture

📝 The packets shown are primarily DNS (Domain Name System) queries and responses, as indicated by the port (53) and the "PTR" records. PTR records are used for reverse DNS lookups, translating IP addresses to domain names. 📝

![Using tcpdump](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/1%20using%20TCPDUMP.png)



## Now I used the tcpdump command and applied some filters to it to look for specific traffic and start a ping from my “attack box” on the left

📝 The tcpdump command has been used with specific options (-i ens33 -c 5) to capture 5 packets on the ens33 interface, filtering for ICMP protocol traffic. 📝

![Using tcpdump to get ICMP packets](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/2%20using%20TCPDUMP%20to%20get%20icmp%20packets.png)



##Before we continue, let's take a second to analyze the structure of a packet

![Breakdown of IMCP packet](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/2.5%20Breakdown%20of%20a%20packet.png)

IP Header (Src/Dst IP, ID, Flags, TTL, Proto, Checksum):

        •	Src IP: The source IP address is where the packet originated.

        •	Dst IP: The destination IP address is where the packet is being sent to.

        •	ID: A unique identifier for the packets sent from source to destination.

        •	Flags: Control flags, such as whether the packet allows fragmentation.

        •	TTL (Time to Live): Decremented at each hop; when it reaches zero, the packet is discarded (to prevent looping).

        •	Protocol (Proto): Indicates the protocol used; for ICMP, this is typically set to '1'.

        •	Header Checksum: Used for error-checking of the header.

ICMP Message (Type, Code, Checksum, Identifier, Sequence Number):

       •	Type: For echo request and reply, the type is 8 and 0, respectively.

       •	Code: Subtype of the message, usually 0 for echo messages.

       •	Checksum: Error-checking for the ICMP message.

       •	Identifier: An identifier to match echo requests with replies.

       •	Sequence Number: Each echo request has an increasing sequence number. It is used to match replies with requests and to detect lost packets.

Data/Payload:

        •	This is the actual data sent in an ICMP message. It often contains a timestamp and a pattern that the sender can use to match with the reply.
The > symbol indicates the direction of the traffic, from source to destination. The tcpdump output doesn't always show the flags, TTL, and other details of the IP header or ICMP message unless specific verbose flags (-v, -vv, or -vvv) are used when starting tcpdump.



## In this step, I wanted to simulate some malicious traffic that attackers might use from my attack box and see what that would look like coming from tcpdump

📝 You can see the SYN flags in the packets, which indicate the initial handshake attempt of a TCP connection, as expected from the SYN stealth scan conducted by Nmap. The tcpdump command is set to exclude 
traffic on port 22 to avoid capturing SSH traffic, which is common when administrators are connected via SSH and do not want to capture their own session's traffic in the packet capture. 📝

![Syn Stealth Scan](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/3.%20Syn%20stealth%20scan%20could%20be%20used%20by%20attackers.png)

The left terminal is a SYN stealth scan. This type of scan is used to check for open ports without establishing a full TCP connection. It sends SYN packets (as if it were going to initiate a connection) and listens for responses, identifying the status of the port based on the received packets:
An SYN-ACK response indicates the port is open.
A RST (reset) response indicates the port is closed.
No response typically means the port is filtered by a firewall.

📝 Network administrators often use the SYN stealth scan for security assessments and also by attackers because it is less likely to be logged than a full TCP connection. 📝



# Part 2 is installing Wireshark on the windows VM because Windows does not use tcpdump

![installing wireshark](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/4.%20installing%20wireshark%20on%20windows%20vm.png)



##Now that Wireshark is installed I will send another ping from my attack box to this VM and see if I can find the icmp packets

📝 Wireshark has a GUI rather than a CLI which makes it a little to filter and see ICMP packets at a glance📝

![Using Wireshark 1](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/5%20using%20wireshark.png)
![Using Wireshark 2](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/6%20using%20wireshark.png)



## Lastly, I performed an aggressive penetrative scan using Nmap from my attack box and captured those network packets

![Penetrative Scan](https://github.com/Malikj10/Packet-Analysis-Lab/blob/00131293e26d5f2134dcbd0e609c37762b4994f4/Screenshots/7.%20Penetrative%20scan.png)

The initial ping sweep revealed active hosts, but targeted pings were blocked or went unanswered, suggesting firewall filtering or downed hosts.
Nmap scans indicated a large number of filtered ports and services, with the aggressive -Pn option uncovering 10 filtered TCP ports that were unreachable during the standard scan, pointing to robust network security measures.
Wireshark provided a visual analysis of the packet flow, confirming the presence of SYN flood patterns and potential network security devices acting upon the scanned packets.



## Conclusion
In my work with network analysis, tcpdump has proven invaluable for its swift and efficient packet capture capabilities, mainly when a graphical interface isn't available. Meanwhile, Wireshark has been indispensable for its deep-dive analytical features, allowing me to dissect and understand complex packet structures visually. Both tools are cornerstones in my cybersecurity toolkit, with tcpdump ideal for quick captures and scripting and Wireshark essential for detailed inspections and forensic investigation
