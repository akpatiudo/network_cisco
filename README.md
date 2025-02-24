## Full Internet Simulation IN Packet Tracer Part One

![](https://i.imgur.com/XgpW4RB.png)

Here’s a step-by-step guide to setting up a simulated internet connection in Packet Tracer using a router instead of CloudPt. This will simulate how home networks connect to an ISP and access the internet.

Objective:
We will:
- Set up an ISP router as a fake internet gateway
- Connect a home router to the ISP
- Configure DHCP, NAT, and routing
- Test internet access

### Add Network Devices
-  Open Packet Tracer
-  Drag & Drop the following devices:
  -  1 x ISP Router (Router-PT)
  -  1 x Home Router (Router-PT)
  -  1 x Switch (Switch-PT)
  -  2 x PCs (PC-PT)

### Connecting the Devices
-  **ISP Router:**
  -  GigabitEthernet6/0 → Connected to GigabitEthernet6/0 of Home Router
  -  GigabitEthernet7/0 → Free (can be used for another connection later)
-  **Home Router:**
  -  GigabitEthernet6/0 → Connected to ISP Router GigabitEthernet6/0
  -  GigabitEthernet7/0 → Connected to Switch FastEthernet0/1
-  **Switch:**
-  FastEthernet0/1 → Connected to Home Router GigabitEthernet7/0

###  Step-by-Step Configuration
-  Configure ISP Router
Since the ISP router provides internet access, we will assign public IP addresses to its interfaces.

###  Steps to Assign the ISP Gateway IP
-  Configure the ISP Router (Upstream Router)
-  Assign an IP to GigabitEthernet6/0 (this will be the gateway for the home router).
- Go to Router Cli > enter > type enable >configure terminal
  - enable  (Puts you in privileged mode)
  -  configure terminal (Lets you configure the router)
  -  hostname HomeRouter (Gives your router a name)

-  interface GigabitEthernet6/0
-  ip address 203.0.113.1 255.255.255.0
-  no shutdown
-  exit
-  copy running-config startup-config (this will save the configurationn. make sure you are in privileged mode hit enter when it asked for destination file name)
-  This means the ISP router gateway IP is 203.0.113.1.
-   ![](https://i.imgur.com/yDDwQE7.png)

###  Configure the Home Router (Your Edge Router)
-  Assign an IP to GigabitEthernet6/0 in the same subnet as the ISP.
-  interface GigabitEthernet6/0
-  ip address 203.0.113.2 255.255.255.0
-  no shutdown
-  exit
-  The Home Router’s WAN IP is 203.0.113.2.
-  **Set the Default Route on the Home Router**
-  Now, tell the Home Router to send all unknown traffic to the ISP Router:
  -  ip route 0.0.0.0 0.0.0.0 203.0.113.1  (This means all traffic destined for the internet will go to 203.0.113.1 (ISP Router).)
  -   copy running-config startup-config (privileged mode)
-  ![](https://i.imgur.com/VTFuPBv.png)

**Configure LAN Interface on Home Router (For Internal Network)**
-  Assign an IP to GigabitEthernet7/0 (the internal network side):
  -  interface GigabitEthernet7/0
  -  ip address 192.168.1.1 255.255.255.0
  -  no shutdown
  -  exit
  -  copy running-config startup-config (privileged mode)
-  This creates a LAN subnet (192.168.1.0/24) for devices connected to the switch.

-  ![](https://i.imgur.com/VTFuPBv.png)

###  Set Up Routing on ISP Router (If Needed)
The ISP router needs to know how to reach the Home Router’s LAN, thus add:
-  ip route 192.168.1.0 255.255.255.0 203.0.113.2
-  copy running-config startup-config (privileged mode)
-  This tells the ISP router that 192.168.1.0/24 is behind 203.0.113.2 (Home Router).

### Final Network Setup After Configuration:
-  ![](https://i.imgur.com/4NkALbC.png)

###  Testing Connectivity
-  Check the routing table on the Home Router:
-  Go to privileged mode
-  show ip route
-  You should see a default route (0.0.0.0/0) pointing to 203.0.113.1.

Ping the ISP Router from the Home Router:
-  ping 203.0.113.1
If successful, the WAN side is working.
-    ![](https://i.imgur.com/JKE5p3n.png)

### Ping the ISP Router from a PC in the LAN:
lets rub ipconfig on one of the PC:
-  ![](https://i.imgur.com/jN4UgiQ.png)

- There is no Ip address, subnet or defualt gateway configured, Let have that done:
- For PC0 IPV4 set to 192.168.1.11
- subnet mask to 255.255.255.0
- defualt gateway 192.168.1.1
- do same to PC1 but the IPV4 shoud be 192.168.1.10
- ![](https://i.imgur.com/S17Rlo3.png)

ping 203.0.113.1
If the ISP router replies, routing is correctly set.

-  ![](https://i.imgur.com/DsAtgJq.png)

###  Result Explained:
Routing table and ping test results indicate that THE Home Router is properly configured! Here’s what the output tells us:
**Routing Table Breakdown:**
-  Directly Connected Networks:
-  192.168.1.0/24 → Connected to GigabitEthernet7/0 (LAN Side).
-  203.0.113.0/24 → Connected to GigabitEthernet6/0 (WAN Side to ISP).
-  Default Route (0.0.0.0/0):

-  Configured as a static route pointing to 203.0.113.1 (ISP Router).
-  This means any unknown traffic (internet-bound) will be sent to the ISP Router.

Ping Test Results
The ping command shows:
-  First packet dropped (likely due to ARP resolution delay).
-  Next four packets succeeded, confirming communication with 203.0.113.1 (ISP Router).

### ICMP SIMULATION PANEL
this is a snippet of network activities that was captured during the pinging 
-  ![](https://i.imgur.com/6qjtpBY.png)


click [here](https://github.com/akpatiudo/networksimulation) for part two

