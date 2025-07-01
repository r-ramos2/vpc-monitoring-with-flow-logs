<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# VPC Monitoring with Flow Logs

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-networks-monitoring)

**Author:** R  

---

## VPC Monitoring with Flow Logs

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_3e1e79a1)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC allows you to create an isolated network within AWS. It provides full control over network configuration, like IP address range, subnets, routing, and security settings, creating secure and scalable environments for your cloud resources.

### How I used Amazon VPC in this project

In today's project, I used Amazon VPC to set up a peering connection between two VPCs, allowing EC2 instances in different VPCs to communicate with each other over private IP addresses.

### One thing I didn't expect in this project was...

One thing I didn’t expect was how much configuration is required to establish secure communication between two VPCs, especially with route tables, security groups, and peering connections. It was a good learning experience about VPC networking.

### This project took me...

It took me around 2 hours to complete the entire setup, including setting up the peering connection, configuring the route tables, and validating the network traffic using EC2 Instance Connect and flow logs.

---

## In the first part of my project...

### Step 1 - Set up VPCs

In this step, we're creating two separate VPCs using the VPC wizard. This sets the foundation for testing peering connections and monitoring network traffic between them using Flow Logs.

### Step 2 - Launch EC2 instances

My EC2 instances' security groups allow communication between VPCs. This is because I’m launching one instance in each VPC to test VPC peering and create network traffic for monitoring later in the project.

### Step 3 - Set up Logs

In this step, we’re enabling VPC Flow Logs to track all network traffic going in and out of our VPCs. This helps us monitor, analyze, and troubleshoot connectivity and security issues effectively.

### Step 4 - Set IAM permissions for Logs

In this step, we’re creating an IAM role and policy to grant VPC Flow Logs permission to publish log data to CloudWatch. This is needed so our flow logs can be stored and viewed for analysis.

---

## Multi-VPC Architecture

I started my project by launching two VPCs: NextWork-1 with CIDR block 10.1.0.0/16 and NextWork-2 with CIDR block 10.2.0.0/16. Each VPC contains one public subnet, totaling two subnets.​

The CIDR blocks for VPCs 1 and 2 are 10.1.0.0/16 and 10.2.0.0/16, respectively. They have to be unique to avoid overlapping IP addresses, which can cause routing conflicts and connectivity issues, especially when establishing VPC peering connections.

### I also launched EC2 instances in each subnet

My EC2 instances' security groups allow traffic between them. This is because we need to test VPC peering by enabling communication, which also helps generate network activity for monitoring later in the project.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_e7fa8775)

---

## Logs

Logs are records of events and activities within your systems, like network traffic, user actions, and errors. They help monitor performance, troubleshoot issues, and track security events.

Log groups are containers in CloudWatch that organize related logs, typically from the same source or service, and are stored regionally.

### I also set up a flow log for VPC 1

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_e8398869)

---

## IAM Policy and Roles

I created an IAM policy because VPC Flow Logs need explicit permission to send network traffic logs to CloudWatch. This policy allows the VPC to write logs to the designated log group.

I also created an IAM role to assign the policy to VPC Flow Logs. Policies define what can be done, but roles are needed to attach those permissions to services so they can use them securely and effectively.

A custom trust policy restricts who can assume a role. By specifying "vpc-flow-logs.amazonaws.com" as the trusted service, we ensure only VPC Flow Logs can use this role, improving security and control.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_4334d777)

---

## In the second part of my project...

### Step 5 - Ping testing and troubleshooting

In this step, we’re generating network traffic by sending test messages from Instance 1 in VPC 1 to Instance 2 in VPC 2. This helps us verify both our VPC peering and that flow logs are capturing the traffic correctly.

### Step 6 - Set up a peering connection

In this step, we’re setting up a peering connection to link VPC 1 and VPC 2. This will allow the two VPCs to communicate with each other using their private IP addresses, solving the connectivity issue we identified earlier.

### Step 7 - Analyze flow logs

In this step, we'll review the VPC Flow Logs to analyze network traffic within VPC 1’s public subnet. This will help us understand how data is flowing, identify potential issues, and confirm that the setup is functioning as expected.

---

## Connectivity troubleshooting

My first ping test between my EC2 instances had no replies, which means the connection attempt from Instance 1 to Instance 2 failed. This suggests there may be a misconfiguration in the VPC peering, routing tables, or network ACLs.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_99d4ba42)

I could receive ping replies if I ran the ping test using the other instance's public IP address, which means both instances are reachable over the public internet, but the VPC peering setup is likely misconfigured or incomplete.

---

## Connectivity troubleshooting

Looking at VPC 1’s route table, I identified that the ping test with Instance 2’s private address failed because there’s no route directing private traffic to the peering connection, but only a route to the internet gateway.

### To solve this, I set up a peering connection between my VPCs

I also updated both VPCs' route tables so that traffic can be properly routed through the peering connection. This ensures VPC 1 can communicate with VPC 2 using private IP addresses, bypassing the public internet.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_7316a13d)

---

## Connectivity troubleshooting

I received ping replies from Instance 2's private IP address! This means the VPC peering connection is working as expected, allowing Instance 1 in VPC 1 to communicate with Instance 2 in VPC 2 using private IPs.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_4ec7821f)

---

## Analyzing flow logs

Flow logs tell us about the source and destination IP addresses, the protocol used (e.g., TCP/UDP), the source and destination ports, traffic direction (IN/OUT), the number of bytes and packets, the action (ACCEPT/REJECT), and the network interface 

For example, the flow log I've captured tells us that 344 bytes of data were successfully sent from the EC2 Instance Connect IP (18.237.140.165) to Instance 1's private IP (10.1.5.112) using TCP on port 22, with traffic accepted.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_d116818e)

---

## Logs Insights

Logs Insights is a CloudWatch feature that helps you analyze logs using queries. It allows you to filter, process, and aggregate data from logs, making it easier to troubleshoot issues, understand trends, and gain insights into network traffic.

I ran the query "Top 10 byte transfers by source and destination IP addresses." This query analyzes the largest data transfers between IP pairs, identifying the busiest traffic flows in the network to investigate bottlenecks or unusual traffic.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-monitoring_3e1e79a1)

---

---
