# AWS Hybrid Cloud VPN Bridge

This repository documents the design and implementation of a functional Hybrid Cloud architecture. The project establishes a secure, encrypted tunnel between a local on-premise environment (simulated via an Ubuntu 22.04 VM) and a public cloud infrastructure (AWS VPC).

## Project Objective
The primary goal was to enable secure, private communication between local resources and cloud-based instances. By utilizing a VPN tunnel, the architecture allows for data exchange over private IP addresses, effectively bypassing the public internet and reducing the attack surface.

## Technical Stack
* **Cloud Infrastructure**: AWS (VPC, EC2, Internet Gateway, Route Tables)
* **VPN Protocol**: OpenVPN (Server-Client Architecture)
* **Operating Systems**: Ubuntu 22.04 LTS
* **Networking Tools**: ICMP (Ping), Netstat, Systemd Service Management

## Implementation Phases

### 1. AWS Infrastructure Provisioning
* **Network Design**: Provisioned a VPC with a `10.2.0.0/16` CIDR block.
* **Compute Resources**: Deployed two EC2 instances: `vpn-gw-ec2` (Gateway) and `aws-test-vm` (Private Test Server).
* **Security Controls**: Configured AWS Security Groups to permit UDP traffic on Port 1194 for VPN handshakes and ICMP traffic for connectivity testing from the local `192.168.1.0/24` subnet.

### 2. VPN Configuration and Deployment
* **Server-Side**: Deployed and configured the OpenVPN server on the AWS Gateway instance.
* **Service Verification**: Confirmed the server was actively listening on UDP port 1194 and that the systemd service was in an active/running state.
* **Client-Side**: Initialized the OpenVPN client on the local Ubuntu machine using a secure `.ovpn` configuration file.

### 3. Connectivity and Routing Verification
* **Tunnel Establishment**: Successfully completed the VPN handshake, verified by the `Initialization Sequence Completed` log entry.
* **Cross-Platform Communication**: Confirmed end-to-end hybrid routing by successfully pinging the AWS private IP (`10.2.1.118`) from the local environment, achieving an average latency of ~9ms.

## Technical Challenges and Solutions
Initially, the project aimed to use **OpenStack** (including DevStack, MicroStack, and Sunbeam) as the private cloud platform. However, several critical blockers were encountered:
* **Hardware Constraints**: Installer requirements exceeded available local resources, requiring a 16GB RAM minimum while the environment was limited to 8GB.
* **Deployment Errors**: Encountered persistent database configuration bugs (`database_connection_url`) and package dependency locks during the installation of Glance and Horizon services.

**Solution**: Successfully pivoted to **K3s**. As a lightweight, certified Kubernetes distribution, K3s provided the necessary cloud-native orchestration capabilities while maintaining high performance on existing hardware.

## Technical Evidence
The following documentation supports the implementation milestones:
* **vpn-client-initialization.png**: Logs confirming successful tunnel initialization.
* **hybrid-ping-test-success.png**: Evidence of successful ICMP communication over the private tunnel.
* **security-group-inbound-rules.png**: Detailed view of AWS ingress rules.
* **aws-vpn-server-status.png**: Confirmation of the OpenVPN service health on the AWS gateway.
