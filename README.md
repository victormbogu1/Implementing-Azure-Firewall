# Configuring-NSG-and-ASG-for-Web-and-Management-Servers
## Description
This project illustrates how I installed Azure Firewall to enhance network security by controlling inbound and outbound access. This setup involves creating a virtual network with workload and jump host subnets, deploying a virtual machine in each subnet, and implementing a custom route that directs all outbound traffic through the firewall. Additionally, the configuration includes application rules to allow outbound traffic only to `www.bing.com` and network rules to permit DNS server lookups. This lab is designed to simulate a business environment setup, utilizing tools such as the Microsoft Azure portal for configuration and Microsoft Server 2019 for testing the web server.
## Environments Used
  + Azure Portal
  + Virtual Machines
## Program walk-through

[![Azure-drawio.png](https://i.postimg.cc/g2fMLh9H/Azure-drawio.png)](https://postimg.cc/fVf76JRV)

The diagram above illustrates the steps involved in completing this project. The first step I deploy virtual machines using a template via the Azure portal. These VMs play vital roles in testing and validating firewall rules, creating isolated network segments, enabling remote administration, hosting web applications, and monitoring network traffic. Deploying these VMs ensures that the Azure Firewall is properly configured and effectively safeguarding the network.

[![Screenshot-2024-07-20-153249.png](https://i.postimg.cc/L50mSDqp/Screenshot-2024-07-20-153249.png)](https://postimg.cc/bSb7HQHC)

[![Screenshot-2024-07-20-153715.png](https://i.postimg.cc/KcsbR9zX/Screenshot-2024-07-20-153715.png)](https://postimg.cc/dDdpfjrn)

[![Screenshot-2024-07-20-154115.png](https://i.postimg.cc/dVPzygcf/Screenshot-2024-07-20-154115.png)](https://postimg.cc/RNdsrsF1)

I deployed the Azure Firewall within the virtual network to ensure that only authorized traffic is permitted

[![Screenshot-2024-07-20-170708.png](https://i.postimg.cc/qvJWwygc/Screenshot-2024-07-20-170708.png)](https://postimg.cc/2VtH8qQ3)

Below are all resources created in resource group named Victor_Resource group

[![Screenshot-2024-07-20-223902.png](https://i.postimg.cc/T3DVFY1k/Screenshot-2024-07-20-223902.png)](https://postimg.cc/5XfQFJwv)

Next, I set up a routing table with a default route for the Workload-SN subnet, directing outbound traffic through the firewall. A routing table is an essential network component that determines the path data packets follow to reach their destination

[![Screenshot-2024-07-20-172055.png](https://i.postimg.cc/8zbNPTf4/Screenshot-2024-07-20-172055.png)](https://postimg.cc/bZdKmcTS)

I configured a custom route that ensures all outbound workload traffic from the workload subnet must use the firewall. The routing table for this Azure Firewall implementation is used to configure routes for the subnets associated with the virtual network and workload servers. This table is associated with the subnets requiring traffic inspection by the Azure Firewall, specifying which subnets traffic should be routed to based on certain IP ranges or destinations. Each route defines a next hop, which could be a virtual appliance such as the Azure Firewall, the internet, a virtual network gateway, or another subnet within the virtual network; in this case, it was the firewall. This setup provided centralized control, monitoring, and protection for network resources. The route was configured by adding the firewall's IP address, ensuring that traffic was directed appropriately.


[![Screenshot-2024-07-20-173323.png](https://i.postimg.cc/Wb5cQXBY/Screenshot-2024-07-20-173323.png)](https://postimg.cc/jLn1fQdy)

[![Screenshot-2024-07-20-173057.png](https://i.postimg.cc/QxxRkcFQ/Screenshot-2024-07-20-173057.png)](https://postimg.cc/XXmHnGcq)

I set up the application and network rules for the Azure Firewall. The application rule grants outbound access to `www.bing.com`. The network rule permits outbound traffic to two specific IP addresses on port 53 (DNS), which are designated as the primary and secondary DNS servers. Firewall Network rules that allow external DNS server lookups

The primary DNS server is the main point of contact for resolving domain names to IP addresses. If this server is available and responsive, all DNS queries will be directed to it. The secondary DNS server acts as a backup, taking over if the primary server is unavailable.


[![Screenshot-2024-07-20-173843.png](https://i.postimg.cc/jqGMCNzq/Screenshot-2024-07-20-173843.png)](https://postimg.cc/5jSBRX7D)

[![Screenshot-2024-07-20-174620.png](https://i.postimg.cc/VNdtjZ8x/Screenshot-2024-07-20-174620.png)](https://postimg.cc/B8ft3gtp)

I configure the DNS servers for the virtual machine to help it resolve the IP addresses of websites. In this case, the virtual machine (Srv-Work) needs to know how to find the IP addresses of websites. Srv-Work is set to use the following DNS servers: 209.244.0.3 and 209.244.0.4. When Srv-Work needs to look up a domain name such as example.com, it will query these DNS servers to obtain the corresponding IP address. Without DNS resolution, the VM would be unable to access websites or other network resources by their domain names

[![Screenshot-2024-07-20-180149.png](https://i.postimg.cc/mk5fFryf/Screenshot-2024-07-20-180149.png)](https://postimg.cc/y3crMsGv)

# Test the firewall

I downloaded the RDP file and used it to connect to the Srv-Jump Azure VM via Remote Desktop. After downloading, I was prompted to authenticate and provided the necessary credentials to access the Srv-Work virtual machine. This setup allows us to test the ability to access the `bing.com` website from the Srv-Work VM

[![Screenshot-2024-07-20-180544.png](https://i.postimg.cc/c4vNGQds/Screenshot-2024-07-20-180544.png)](https://postimg.cc/GHnfkTBN)

From the Remote Desktop session to Srv-Jump, I open the Run dialog box and enter the command below to connect to Srv-Work

[![Screenshot-2024-07-20-180903.png](https://i.postimg.cc/6pRJyt7g/Screenshot-2024-07-20-180903.png)](https://postimg.cc/yDVrGqyy)

From the Remote Desktop session to Srv-Work, I went to Server Manager and disabled the IE Enhanced Security Configuration by turning off both options.

[![Screenshot-2024-07-20-181141.png](https://i.postimg.cc/R0BrtyKF/Screenshot-2024-07-20-181141.png)](https://postimg.cc/qNjZVmmV)


From the Remote Desktop session to Srv-Work, I opened Internet Explorer and visited `https://www.bing.com`. The website loaded successfully, confirming that the firewall allowed the connection.

[![Screenshot-2024-07-20-181357.png](https://i.postimg.cc/kGZzgp77/Screenshot-2024-07-20-181357.png)](https://postimg.cc/RJ7XPG3Y)

I tested by attempting to access `http://www.microsoft.com/`, and the response indicated an HTTP request from `10.0.2.4:xxxxx` to `microsoft.com:80` was denied with the message: "No rule matched. Proceeding with default action." This result is anticipated, as the firewall is set to block access to this site.

[![Screenshot-2024-07-20-181520.png](https://i.postimg.cc/dtkgxw93/Screenshot-2024-07-20-181520.png)](https://postimg.cc/56JPHcTW)

# Explanation of Specific Components
Work Server (Srv-Work): This server is used to simulate a workload within the Workload-SN subnet. It is configured to route its traffic through the Azure Firewall for security purposes

Jump Server (Srv-Jump): This server is used as a management jump box, allowing administrators to connect to and manage other VMs in the network securely

Test-FW-VN: A secure virtual network in Azure that connects and isolates VMs

TEST-FW-PIP: This is the public IP address assigned to the Azure Firewall, allowing it to interact with external networks and provide ingress/egress filtering for the VNet

DNS Servers Configuration: Ensures VMs can resolve domain names to IP addresses for connectivity

The jump server facilitates secure administration, while the work server simulates internal traffic managed by the firewall
