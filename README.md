<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GCP HTTP Load Balancer Setup - README</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #2f3e46; /* charcoal */
            color: #cad2c5; /* ash-gray */
            line-height: 1.6;
        }
        .container {
            max-width: 960px;
            margin: 0 auto;
            padding: 2rem;
        }
        h1, h2, h3, h4, h5, h6 {
            color: #f0f8ff; /* AliceBlue */
            font-weight: 700;
            margin-bottom: 1rem;
            margin-top: 2rem;
        }
        h1 { font-size: 2.5rem; }
        h2 { font-size: 2rem; }
        h3 { font-size: 1.75rem; }
        h4 { font-size: 1.5rem; }
        p {
            margin-bottom: 1rem;
        }
        ul {
            list-style-type: disc;
            margin-left: 1.5rem;
            margin-bottom: 1rem;
        }
        ol {
            list-style-type: decimal;
            margin-left: 1.5rem;
            margin-bottom: 1rem;
        }
        li {
            margin-bottom: 0.5rem;
        }
        a {
            color: #84a98c; /* cambridge-blue */
            text-decoration: none;
        }
        a:hover {
            text-decoration: underline;
        }
        .badge {
            display: inline-block;
            padding: 0.25em 0.75em;
            font-size: 0.8em;
            font-weight: 600;
            line-height: 1;
            text-align: center;
            white-space: nowrap;
            vertical-align: middle;
            border-radius: 0.375rem;
            color: #f0f8ff;
        }
        .badge-green { background-color: #52796f; /* hookers-green */ }
        .badge-blue { background-color: #354f52; /* dark-slate-gray */ }

        pre {
            background-color: #354f52; /* dark-slate-gray */
            padding: 1rem;
            border-radius: 0.5rem;
            overflow-x: auto;
            font-family: monospace;
            font-size: 0.9em;
            color: #cad2c5; /* ash-gray */
            border: 1px solid #52796f; /* hookers-green border */
            margin-bottom: 1rem;
        }
        strong {
            color: #f0f8ff; /* AliceBlue */
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>GCP HTTP Load Balancer Setup</h1>

        <p>
            <span class="badge badge-green">Status: Completed</span>
            <span class="badge badge-blue">GCP: Free Tier Compliant</span>
        </p>

        <p>
            This repository contains the documentation and an interactive Single-Page Application (SPA) demonstrating a comprehensive HTTP Load Balancing setup on Google Cloud Platform (GCP). The project showcases how to deploy a highly available and scalable web application by efficiently distributing incoming traffic across multiple Virtual Machine (VM) instances running Apache web servers.
        </p>

        <h2>Introduction to Load Balancing</h2>
        <p>
            Load balancing is a fundamental component in modern web application architectures. It acts as a traffic manager, intelligently distributing incoming client requests across a pool of backend servers. This prevents any single server from becoming a bottleneck, ensuring optimal performance, preventing outages, and enhancing the overall user experience.
        </p>

        <h2>Features & Benefits</h2>
        <p>This load balancing solution provides:</p>
        <ul>
            <li><strong>High Availability & Fault Tolerance</strong>: Intelligent monitoring via <strong>HTTP Health Checks</strong> ensures traffic is only sent to healthy VMs. Unhealthy instances are automatically removed from rotation.</li>
            <li><strong>Concurrent VM Computing</strong>: Efficient distribution of traffic across multiple <strong>Apache-enabled VM instances</strong> within an <strong>Unmanaged Instance Group</strong>, demonstrating parallel processing.</li>
            <li><strong>Comprehensive GCP Architecture</strong>: Integration of essential GCP services including <strong>Custom VPC Network</strong>, <strong>Firewall Rules</strong>, <strong>Backend Services</strong>, <strong>URL Maps</strong>, <strong>Target HTTP Proxies</strong>, and <strong>Global Static IP Addresses</strong>.</li>
            <li><strong>Scalability & Performance</strong>: A robust foundation for horizontal scaling, allowing for easy expansion and improved response times during fluctuating traffic.</li>
            <li><strong>Real-world Applicability</strong>: Applicable to high-traffic scenarios like e-commerce, streaming, API gateways, and online gaming, where uptime and performance are critical.</li>
        </ul>

        <h2>Interactive Explorer & Documentation</h2>
        <p>
            I have fully documented the entire process and built an interactive <strong>Single-Page Application (SPA)</strong> that visually explains the traffic flow, component roles, and setup steps. This SPA is designed to be a learning tool, making the complex concepts of GCP load balancing easily digestible.
        </p>
        <p>
            👉 <strong>Explore the interactive SPA:</strong> <a href="https://daichianoop.github.io/GCP-load-balancer-setup/index.html" target="_blank">https://daichianoop.github.io/GCP-load-balancer-setup/index.html</a>
        </p>

        <h2>Architecture & Components Overview</h2>
        <p>The setup leverages several interconnected GCP services to achieve HTTP load balancing:</p>
        <ul>
            <li><strong>Virtual Private Cloud (VPC) & Subnet</strong>: Isolated network for resources.</li>
            <li><strong>Firewall Rules</strong>: Control ingress/egress traffic (HTTP/SSH allowed).</li>
            <li><strong>VM Instances (Web Servers)</strong>: <code>e2-micro</code> VMs running Apache, serving <code>index.html</code>.</li>
            <li><strong>Unmanaged Instance Group</strong>: Logical collection of VMs for backend service.</li>
            <li><strong>HTTP Health Check</strong>: Periodically probes VM health.</li>
            <li><strong>Backend Service</strong>: Defines traffic distribution to the instance group.</li>
            <li><strong>URL Map</strong>: Routes HTTP(S) requests to backend services.</li>
            <li><strong>Target HTTP Proxy</strong>: Forwards client connections to the URL map.</li>
            <li><strong>Forwarding Rule</strong>: Listens for external traffic and directs it to the target proxy.</li>
            <li><strong>Reserved Global Static IP Address</strong>: Public, permanent IP for application access.</li>
        </ul>

        <h2>Traffic Flow Workflow</h2>
        <p>The application handles traffic in a specific sequence:</p>
        <ol>
            <li><strong>User Access</strong>: User sends request to the Global Static IP.</li>
            <li><strong>Forwarding Rule Interception</strong>: The Forwarding Rule captures the HTTP request on Port 80.</li>
            <li><strong>Traffic to Target HTTP Proxy</strong>: Request is sent to the Target HTTP Proxy.</li>
            <li><strong>URL Map Consultation</strong>: The Target HTTP Proxy uses the URL Map to identify the correct Backend Service.</li>
            <li><strong>Backend Service & Health Check</strong>: The Backend Service initiates Health Checks on associated Instance Group VMs.</li>
            <li><strong>Traffic Forwarding to VM</strong>: Traffic is forwarded to a healthy VM instance.</li>
            <li><strong>Apache Response</strong>: The VM's Apache server responds (e.g., "Welcome to Server 1").</li>
            <li><strong>Load Balancing in Action</strong>: On refresh, the load balancer alternates responses between VMs, demonstrating traffic distribution.</li>
        </ol>
        <p>
            The primary objective is for users to access a single, publicly available IP address and receive consistently alternating responses, confirming effective load distribution.
        </p>

        <h2>Setup Guide (Free Tier Compliant - Single Zone)</h2>
        <p>This guide outlines the steps to deploy the load balancing application within GCP's Free Tier constraints, focusing on <code>us-central1-c</code> zone.</p>

        <h3>Prerequisites:</h3>
        <ul>
            <li>A Google Cloud Platform Free Tier account.</li>
        </ul>

        <h3>Project Configuration:</h3>
        <ul>
            <li><strong>Zone:</strong> <code>us-central1-c</code></li>
            <li><strong>VPC:</strong> <code>custom-vpc</code></li>
            <li><strong>Subnet:</strong> <code>web-subnet</code></li>
            <li><strong>VMs:</strong> Two <code>e2-micro</code> instances in <code>us-central1-c</code></li>
            <li><strong>Instance Group:</strong> Unmanaged</li>
            <li><strong>Load Balancer:</strong> HTTP Load Balancer</li>
        </ul>

        <h3>Steps:</h3>
        <ol>
            <li>
                <h4>Create Custom VPC Network</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>VPC network</code> &rarr; <code>VPC networks</code> &rarr; <code>Create VPC network</code></li>
                    <li><strong>Details:</strong> Name: <code>custom-vpc</code>, Subnet creation mode: <code>Custom</code>, Subnet name: <code>web-subnet</code>, Region: <code>us-central1</code>, IP range: <code>10.0.1.0/24</code>, Private Google Access: <code>Off</code>, Routing mode: <code>Regional</code>.</li>
                    <li><strong>Action:</strong> Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create Firewall Rules</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>VPC network</code> &rarr; <code>Firewall rules</code> &rarr; <code>Create Firewall Rule</code></li>
                    <li><strong>Rule A (Allow HTTP):</strong> Name: <code>allow-http</code>, Network: <code>custom-vpc</code>, Direction: <code>Ingress</code>, Action: <code>Allow</code>, Targets: <code>All instances in the network</code>, Source IP range: <code>0.0.0.0/0</code>, Protocols and Ports: <code>tcp:80</code>. Click <code>Create</code>.</li>
                    <li><strong>Rule B (Allow SSH):</strong> Name: <code>allow-ssh</code>, Network: <code>custom-vpc</code>, Direction: <code>Ingress</code>, Action: <code>Allow</code>, Targets: <code>All instances in the network</code>, Source IP range: <code>0.0.0.0/0</code>, Protocols and Ports: <code>tcp:22</code>. Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create Two VMs (Same Zone)</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>Compute Engine</code> &rarr; <code>VM instances</code> &rarr; <code>Create instance</code></li>
                    <li><strong>VM #1 (<code>web-server-1</code>):</strong> Name: <code>web-server-1</code>, Region: <code>us-central1</code>, Zone: <code>us-central1-c</code>, Machine type: <code>e2-micro</code>, Boot disk: <code>Debian/Ubuntu</code>, VPC network: <code>custom-vpc</code>, Subnet: <code>web-subnet</code>, External IP: <code>Ephemeral</code>, Firewall: ✅ <code>Allow HTTP</code>, ✅ <code>Allow HTTPS</code>.
                        <p><strong>Startup script:</strong></p>
                        <pre><code>#! /bin/bash
sudo apt update
sudo apt install apache2 -y
echo "Welcome to Server 1" | sudo tee /var/www/html/index.html
sudo systemctl start apache2
</code></pre>
                        Click <code>Create</code>.
                    </li>
                    <li><strong>VM #2 (<code>web-server-2</code>):</strong> Same settings as VM #1, but Name: <code>web-server-2</code> and <code>echo "Welcome to Server 2" | sudo tee /var/www/html/index.html</code> in the startup script. Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create Unmanaged Instance Group</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>Compute Engine</code> &rarr; <code>Instance groups</code> &rarr; <code>Create instance group</code></li>
                    <li><strong>Details:</strong> Name: <code>web-group</code>, Group Type: <code>Unmanaged</code>, Location: <code>Single zone</code>, Zone: <code>us-central1-c</code>, Network: <code>custom-vpc</code>, Subnet: <code>web-subnet</code>, VMs: Select <code>web-server-1</code> and <code>web-server-2</code>.</li>
                    <li><strong>Action:</strong> Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create Health Check</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>Network services</code> &rarr; <code>Load balancing</code> &rarr; <code>Health checks</code> &rarr; <code>Create Health Check</code></li>
                    <li><strong>Details:</strong> Name: <code>http-health-check</code>, Protocol: <code>HTTP</code>, Port: <code>80</code>, Request path: <code>/</code>.</li>
                    <li><strong>Action:</strong> Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create Backend Service</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>Network services</code> &rarr; <code>Load balancing</code> &rarr; <code>Backend configuration</code> &rarr; <code>Create backend service</code></li>
                    <li><strong>Details:</strong> Name: <code>web-backend</code>, Protocol: <code>HTTP</code>, Scope: <code>Global</code>, Backends: Select <code>web-group</code>, Port numbers: <code>80</code>, Health check: <code>http-health-check</code>.</li>
                    <li><strong>Action:</strong> Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create URL Map</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>Network services</code> &rarr; <code>Load balancing</code> &rarr; <code>URL maps</code> &rarr; <code>Create URL map</code></li>
                    <li><strong>Details:</strong> Name: <code>web-url-map</code>, Default backend service: <code>web-backend</code>.</li>
                    <li><strong>Action:</strong> Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create Target HTTP Proxy</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>Network services</code> &rarr; <code>Load balancing</code> &rarr; <code>Target proxies</code> &rarr; <code>Create target HTTP proxy</code></li>
                    <li><strong>Details:</strong> Name: <code>web-proxy</code>, URL map: <code>web-url-map</code>.</li>
                    <li><strong>Action:</strong> Click <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Reserve Global Static IP</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>VPC network</code> &rarr; <code>External IP addresses</code> &rarr; <code>Reserve static IP</code></li>
                    <li><strong>Details:</strong> Name: <code>web-ip</code>, Type: <code>Global</code>, IP version: <code>IPv4</code>.</li>
                    <li><strong>Action:</strong> Click <code>Reserve</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Create Forwarding Rule</h4>
                <ul>
                    <li><strong>Navigation:</strong> <code>Network services</code> &rarr; <code>Load balancing</code> &rarr; <code>Create Load Balancer</code> &rarr; <code>HTTP(S) Load Balancing</code> (Follow the guided wizard)</li>
                    <li><strong>Frontend Configuration:</strong> Name: <code>web-forwarding-rule</code>, Protocol: <code>HTTP</code>, IP address: <code>web-ip</code>, Port: <code>80</code>. Click <code>Done</code>.</li>
                    <li><strong>Backend Configuration:</strong> Select existing backend service: <code>web-backend</code>.</li>
                    <li><strong>Action:</strong> Click <code>Next</code>, review, then <code>Create</code>.</li>
                </ul>
            </li>
            <li>
                <h4>Test the Load Balancer</h4>
                <ul>
                    <li>Open your browser and navigate to: <code>http://&lt;your-global-static-ip&gt;</code></li>
                    <li>You should see either "Welcome to Server 1" or "Welcome to Server 2". Upon refreshing, the load should alternate between the two messages.</li>
                </ul>
            </li>
        </ol>

        <h2>Common Problems & Solutions</h2>
        <ul>
            <li><strong>VM Not Appearing in Instance Group</strong>: Ensure VM is in the correct zone and network.</li>
            <li><strong>Health Check Fails</strong>: Confirm Apache is running, path <code>/</code> responds, and firewall rules allow probes.</li>
            <li><strong>Static IP Not Showing</strong>: Verify it's reserved as <code>GLOBAL</code> type.</li>
            <li><strong>Backend Service Empty</strong>: Check if the VM group is unmanaged or unselected.</li>
            <li><strong>Load Balancer Not Forwarding Traffic</strong>: Ensure firewall rule allows <code>TCP:80</code>.</li>
            <li><strong>Unhealthy Status of Instances</strong>: Reconfirm correct port and request path in health check configuration.</li>
            <li><strong>URL Map Misconfigured</strong>: Ensure URL map correctly points to the desired backend service.</li>
            <li><strong>Apache Not Installed or Running</strong>: Use startup scripts for VM creation to install Apache.</li>
            <li><strong>Browser Timeout</strong>: Verify VMs have external IP and Apache is listening.</li>
        </ul>

        <h2>Future Scope</h2>
        <ul>
            <li><strong>State-of-the-art Hotspot Mitigation</strong>: Implementing advanced techniques to further optimize traffic distribution and prevent performance bottlenecks on specific servers under heavy, uneven load.</li>
        </ul>

        <h2>License</h2>
        <p>This project is open-source and available under the <a href="#" target="_blank">MIT License</a>.</p>

        <h2>Connect</h2>
        <p>Feel free to connect with me on <a href="https://www.linkedin.com/in/daichianoop/" target="_blank">LinkedIn</a> if you have any questions or would like to discuss cloud architecture and load balancing!</p>
    </div>
</body>
</html>
