# AWS Cloud Networking: Configuring Secure Access Between EC2 Instances in Public and Private Subnets

## Project Overview
![Test Connectivity Diagram](https://github.com/user-attachments/assets/5b743de9-9197-46ed-ba12-4fddaa27ce37)


This project is a continuation of my previous AWS Cloud Networking projects, where I explore secure communication between EC2 instances within a VPC. I created two EC2 instances—one in a private subnet and another in a public subnet—and set up network configurations to allow controlled communication between them. (The blue arrows demonstrates the connectivity test.)

## Steps

### 1. **Create VPC Using VPC Wizard**
- Created a VPC using the VPC Wizard, setting up public and private subnets, route tables, and an Internet Gateway for public subnet access.

### 2. **Rename VPC Components**
- Renamed VPC components for clarity:
    - Subnets: My Public Subnet and My Private Subnet
    - Route Tables: My Public Route Table and My Private Route Table
    - Internet Gateway: My Internet Gateway

### 3. **Create Network ACLs**
- Configured a Network ACL for My Private Subnet to manage inbound and outbound traffic. Initially, all traffic was denied. After launching the EC2 instances and testing communication from My Public Server to My Private Server, I configured the NACL to allow the necessary traffic, such as ICMP for ping requests.
- Verified that a NACL was created for My Public Subnet and configured it to allow all types of traffic.

### 4. **Create Security Groups**
- Created security groups to control traffic for both EC2 instances:
    1. **My Public Security Group**:
        - Allowed inbound traffic on HTTP (`80`) from Anywhere-IPv4 (`0.0.0.0/0`).
        - No need to specify outbound rules, as Security Groups are stateful (outbound traffic is automatically allowed if inbound traffic is permitted).
    
    2. **My Private Security Group**:
        - Configured to allow SSH access (`22`) only from instances associated with My Public Security Group.
        - Later configured to allow ICMP traffic for ping requests.

### 5. **EC2 Instances Launch Configuration**

I chose my custom VPC for deploying the EC2 instances instead of using the default VPC provided by AWS.

#### **Public EC2 Instance ("My Public Server")**

1. **Network Configuration**
    - Subnet: My Public Subnet (selected from the dropdown menu of existing subnets within My VPC).
    - Security Group: Associated the existing "My Public Security Group," which allows HTTP traffic from anywhere.
    
2. **Launched EC2**
    - Instance launched with a public IP and was accessible over the internet.

#### **Private EC2 Instance ("My Private Server")**

1. **Network Configuration**
    - Subnet: My Private Subnet (selected from the dropdown menu of existing subnets within My VPC).
    - Security Group: Created a new "My Private Security Group."
    - Inbound Rule: Configured a rule with a **custom source** to allow inbound SSH traffic (`22`) only from resources associated with My Public Security Group.
    
2. **Launched EC2**
    - Instance launched into My Private Subnet, only accessible via SSH from instances associated with My Public Security Group.

### 6. **Connect to Servers**

- **My Public Server**: Connected via SSH after resolving the security group configuration.
- **My Private Server**: Connected through SSH using the private IPv4 address, routed through My Public Server.

### 7. **Test Communication Between Servers**

- Successfully pinged My Private Server (private IP: `10.0.1.4`) from My Public Server after allowing ICMP traffic in both the Security Group and NACL for My Private Server.

### 8. **Verify Internet Access**

- Verified internet connectivity on My Public Server using:
    ```bash
    $ curl example.com
    ```
- Confirmed that the Internet Gateway was functioning as expected.

### 9. **Troubleshooting**

- **Issue**: Unable to connect to My Public Server via **EC2 Instance Connect**.
- **Cause**: SSH was not allowed in My Public Security Group.
- **Solution**: Edited **My Public Security Group** to include the SSH protocol (Port 22). Retried the connection, which succeeded after the rule update.

## Project Screenshots

1-) VPC Creation

![Create VPC](https://github.com/user-attachments/assets/e17879bc-1a01-4b20-a2d5-5a21df40c04f)

2-) Security Group Settings for Public and Private Servers
![Public EC2 Settings](https://github.com/user-attachments/assets/74229fbd-4559-4bce-9cef-7db075f0e5b4)
![Private EC2 Settings](https://github.com/user-attachments/assets/f9689206-a344-41cb-9ac8-2f3a01d88f51)

3-) Ping Command Result from My Public Server to My Private Server
![Connected and Pinged Private EC2](https://github.com/user-attachments/assets/b25fc016-1dea-4533-babc-6a314fe9ac80)

4-) Curl Command Output from My Public Server
![Checking Public Server's Internet Connectivity](https://github.com/user-attachments/assets/6a7dfdf4-af4d-4a85-a7f6-709e5d0af002)
