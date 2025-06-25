<div align="center">
  <img src="https://github.com/daichianoop/GCP-load-balancer-setup/blob/main/gcp.png?raw=true" alt="logo" width="50" />
  # GCP HTTP Load Balancer Setup
</div>


<div align="center">
  <div>
<img src="https://img.shields.io/badge/-HTML-black?style=for-the-badge&logoColor=white&logo=html5&color=E34F26" alt="HTML" />
<img src="https://img.shields.io/badge/-CSS-black?style=for-the-badge&logoColor=white&logo=css3&color=1572B6" alt="CSS" />
<img src="https://img.shields.io/badge/-JavaScript-black?style=for-the-badge&logoColor=white&logo=javascript&color=F7DF1E" alt="JavaScript" />
<img src="https://img.shields.io/badge/-Google_Cloud_Platform-black?style=for-the-badge&logoColor=white&logo=googlecloud&color=4285F4" alt="Google Cloud Platform" />
  </div>

This repository contains the documentation and an interactive Single-Page Application (SPA) demonstrating a comprehensive HTTP Load Balancing setup on Google Cloud Platform (GCP). The project showcases how to deploy a highly available and scalable web application by efficiently distributing incoming traffic across multiple Virtual Machine (VM) instances running Apache web servers.

## Introduction to Load Balancing

Load balancing is a fundamental component in modern web application architectures. It acts as a traffic manager, intelligently distributing incoming client requests across a pool of backend servers. This prevents any single server from becoming a bottleneck, ensuring optimal performance, preventing outages, and enhancing the overall user experience.

## Features & Benefits

This load balancing solution provides:

- **High Availability & Fault Tolerance**: Intelligent monitoring via HTTP Health Checks ensures traffic is only sent to healthy VMs. Unhealthy instances are automatically removed from rotation.

- **Concurrent VM Computing**: Efficient distribution of traffic across multiple Apache-enabled VM instances within an Unmanaged Instance Group, demonstrating parallel processing.

- **Comprehensive GCP Architecture**: Integration of essential GCP services including Custom VPC Network, Firewall Rules, Backend Services, URL Maps, Target HTTP Proxies, and Global Static IP Addresses.

- **Scalability & Performance**: A robust foundation for horizontal scaling, allowing for easy expansion and improved response times during fluctuating traffic.

- **Real-world Applicability**: Applicable to high-traffic scenarios like e-commerce, streaming, API gateways, and online gaming, where uptime and performance are critical.

## Interactive Explorer & Documentation

I have fully documented the entire process and built an interactive Single-Page Application (SPA) that visually explains the traffic flow, component roles, and setup steps. This SPA is designed to be a learning tool, making the complex concepts of GCP load balancing easily digestible.

👉 Explore the interactive SPA: [Link to your SPA's GitHub Pages or hosted link if available.](https://daichianoop.github.io/GCP-load-balancer-setup/index.html)

## Architecture & Components Overview

The setup leverages several interconnected GCP services to achieve HTTP load balancing:

- **Virtual Private Cloud (VPC) & Subnet**: Isolated network for resources.

- **Firewall Rules**: Control ingress/egress traffic (HTTP/SSH allowed).

- **VM Instances (Web Servers)**: e2-micro VMs running Apache, serving index.html.

- **Unmanaged Instance Group**: Logical collection of VMs for backend service.

- **HTTP Health Check**: Periodically probes VM health.

- **Backend Service**: Defines traffic distribution to the instance group.

- **URL Map**: Routes HTTP(S) requests to backend services.

- **Target HTTP Proxy**: Forwards client connections to the URL map.

- **Forwarding Rule**: Listens for external traffic and directs it to the target proxy.

- **Reserved Global Static IP Address**: Public, permanent IP for application access.

## Traffic Flow Workflow

The application handles traffic in a specific sequence:

1. **User  Access**: User sends request to the Global Static IP.

2. **Forwarding Rule Interception**: The Forwarding Rule captures the HTTP request on Port 80.

3. **Traffic to Target HTTP Proxy**: Request is sent to the Target HTTP Proxy.

4. **URL Map Consultation**: The Target HTTP Proxy uses the URL Map to identify the correct Backend Service.

5. **Backend Service & Health Check**: The Backend Service initiates Health Checks on associated Instance Group VMs.

6. **Traffic Forwarding to VM**: Traffic is forwarded to a healthy VM instance.

7. **Apache Response**: The VM's Apache server responds (e.g., "Welcome to Server 1").

8. **Load Balancing in Action**: On refresh, the load balancer alternates responses between VMs, demonstrating traffic distribution.

The primary objective is for users to access a single, publicly available IP address and receive consistently alternating responses, confirming effective load distribution.

## Setup Guide (Free Tier Compliant - Single Zone)

This guide outlines the steps to deploy the load balancing application within GCP's Free Tier constraints, focusing on us-central1-c zone.

### Prerequisites

- A Google Cloud Platform Free Tier account.

### Project Configuration

- **Zone**: us-central1-c

- **VPC**: custom-vpc

- **Subnet**: web-subnet

- **VMs**: Two e2-micro instances in us-central1-c

- **Instance Group**: Unmanaged

- **Load Balancer**: HTTP Load Balancer

### Steps

1. **Create Custom VPC Network**

   - **Navigation**: VPC network → VPC networks → Create VPC network
   - **Details**: 
     - Name: custom-vpc
     - Subnet creation mode: Custom
     - Subnet name: web-subnet
     - Region: us-central1
     - IP range: 10.0.1.0/24
     - Private Google Access: Off
     - Routing mode: Regional.
   - **Action**: Click Create.

2. **Create Firewall Rules**

   - **Navigation**: VPC network → Firewall rules → Create Firewall Rule
   - **Rule A (Allow HTTP)**: 
     - Name: allow-http
     - Network: custom-vpc
     - Direction: Ingress
     - Action: Allow
     - Targets: All instances in the network
     - Source IP range: 0.0.0.0/0
     - Protocols and Ports: tcp:80. 
     - **Action**: Click Create.

   - **Rule B (Allow SSH)**: 
     - Name: allow-ssh
     - Network: custom-vpc
     - Direction: Ingress
     - Action: Allow
     - Targets: All instances in the network
     - Source IP range: 0.0.0.0/0
     - Protocols and Ports: tcp:22. 
     - **Action**: Click Create.

3. **Create Two VMs (Same Zone)**

   - **Navigation**: Compute Engine → VM instances → Create instance
   - **VM #1 (web-server-1)**: 
     - Name: web-server-1
     - Region: us-central1
     - Zone: us-central1-c
     - Machine type: e2-micro
     - Boot disk: Debian/Ubuntu
     - VPC network: custom-vpc
     - Subnet: web-subnet
     - External IP: Ephemeral
     - Firewall: ✅ Allow HTTP, ✅ Allow HTTPS.
     - **Startup script**:
       ```bash
       #! /bin/bash
       sudo apt update
       sudo apt install apache2 -y
       echo "Welcome to Server 1" | sudo tee /var/www/html/index.html
       sudo systemctl start apache2
       ```
     - **Action**: Click Create.

   - **VM #2 (web-server-2)**: Same settings as VM #1, but Name: web-server-2 and startup script:
     ```bash
     #! /bin/bash
     sudo apt update
     sudo apt install apache2 -y
     echo "Welcome to Server 2" | sudo tee /var/www/html/index.html
     sudo systemctl start apache2
     ```
     - **Action**: Click Create.

4. **Create Unmanaged Instance Group**

   - **Navigation**: Compute Engine → Instance groups → Create instance group
   - **Details**: 
     - Name: web-group
     - Group Type: Unmanaged
     - Location: Single zone
     - Zone: us-central1-c
     - Network: custom-vpc
     - Subnet: web-subnet
     - VMs: Select web-server-1 and web-server-2.
   - **Action**: Click Create.

5. **Create Health Check**

   - **Navigation**: Network services → Load balancing → Health checks → Create Health Check
   - **Details**: 
     - Name: http-health-check
     - Protocol: HTTP
     - Port: 80
     - Request path: /.
   - **Action**: Click Create.

6. **Create Backend Service**

   - **Navigation**: Network services → Load balancing → Backend configuration → Create backend service
   - **Details**: 
     - Name: web-backend
     - Protocol: HTTP
     - Scope: Global
     - Backends: Select web-group
     - Port numbers: 80
     - Health check: http-health-check.
   - **Action**: Click Create.

7. **Create URL Map**

   - **Navigation**: Network services → Load balancing → URL maps → Create URL map
   - **Details**: 
     - Name: web-url-map
     - Default backend service: web-backend.
   - **Action**: Click Create.

8. **Create Target HTTP Proxy**

   - **Navigation**: Network services → Load balancing → Target proxies → Create target HTTP proxy
   - **Details**: 
     - Name: web-proxy
     - URL map: web-url-map.
   - **Action**: Click Create.

9. **Reserve Global Static IP**

   - **Navigation**: VPC network → External IP addresses → Reserve static IP
   - **Details**: 
     - Name: web-ip
     - Type: Global
     - IP version: IPv4.
   - **Action**: Click Reserve.

10. **Create Forwarding Rule**

    - **Navigation**: Network services → Load balancing → Create Load Balancer → HTTP(S) Load Balancing (Follow the guided wizard)
    - **Frontend Configuration**: 
      - Name: web-forwarding-rule
      - Protocol: HTTP
      - IP address: web-ip
      - Port: 80. 
      - **Action**: Click Done.

    - **Backend Configuration**: 
      - Select existing backend service: web-backend.
      - **Action**: Click Next, review, then Create.

11. **Test the Load Balancer**

    - Open your browser and navigate to: `http://<your-global-static-ip>`
    - You should see either "Welcome to Server 1" or "Welcome to Server 2". Upon refreshing, the load should alternate between the two messages.

## Common Problems & Solutions

- **VM Not Appearing in Instance Group**: Ensure VM is in the correct zone and network.

- **Health Check Fails**: Confirm Apache is running, path / responds, and firewall rules allow probes.

- **Static IP Not Showing**: Verify it's reserved as GLOBAL type.

- **Backend Service Empty**: Check if the VM group is unmanaged or unselected.

- **Load Balancer Not Forwarding Traffic**: Ensure firewall rule allows TCP:80.

- **Unhealthy Status of Instances**: Reconfirm correct port and request path in health check configuration.

- **URL Map Misconfigured**: Ensure URL map correctly points to the desired backend service.

- **Apache Not Installed or Running**: Use startup scripts for VM creation to install Apache.

- **Browser Timeout**: Verify VMs have external IP and Apache is listening.

## Future Scope

- **State-of-the-art Hotspot Mitigation**: Implementing advanced techniques to further optimize traffic distribution and prevent performance bottlenecks on specific servers under heavy, uneven load.

## License

This project is open-source and available under the MIT License.

## Connect

Feel free to connect with me on LinkedIn (replace with your actual LinkedIn profile link) if you have any questions or would like to discuss cloud architecture and load balancing!
