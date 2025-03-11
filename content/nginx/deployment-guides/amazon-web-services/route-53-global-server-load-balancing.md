---
description: Deploy global server load balancing (GSLB) for domains hosted in multiple
  AWS regions, with Amazon Route 53 and F5 NGINX Plus in an HA configuration.
docs: DOCS-448
doctypes:
- task
title: Global Server Load Balancing with Amazon Route 53 and NGINX Plus
toc: true
weight: 300
---

## Overview

This guide explains how to configure global server load balancing (GSLB) for web domains hosted in Amazon Elastic Compute Cloud (EC2). By using Amazon Route 53 and NGINX Plus, you can distribute traffic across multiple AWS regions, improving availability and performance.

### How it works

- Route 53 routes traffic based on network latency, directing clients to the closest AWS region.
- NGINX Plus load balances traffic across backend servers within each region.
- If a region becomes unavailable, Route 53 automatically fails over to the next closest region.

For instructions on setting up EC2 instances and installing NGINX, see the [Appendix](#appendix).

---

## Topology for global load balancing with Amazon Route 53 and NGINX Plus {#topology}

This deployment uses Amazon Elastic Compute Cloud (EC2) instances, Amazon Route 53, and NGINX Plus to provide global server load balancing (GSLB).

{{<note>}}
[Global server load balancing](https://www.nginx.com/resources/glossary/global-server-load-balancing/) is sometimes called *global load balancing* (GLB). The terms are used interchangeably in this guide.
{{</note>}}

<img src="/nginx/images/aws-route-53-topology.png" 
     alt="Diagram showing the topology for global server load balancing (GSLB) with Amazon Route 53 and NGINX Plus. Route 53 directs traffic based on network latency to two AWS regions: US West and US East. Each region contains two NGINX Plus load balancers distributing traffic to backend servers. If a region becomes unavailable, Route 53 fails over to the other region." />

### Traffic flow

- Amazon Route 53 directs client traffic to the AWS region with the lowest latency.
- NGINX Plus load balancers distribute traffic across backend servers within each region.
- Health checks monitor server availability and trigger failover if needed.

### Deployment setup

This guide uses two AWS regions:

- US West (Oregon)
- US East (N. Virginia)

Each region contains:

- Two NGINX Plus load balancers in a high-availability (HA) configuration
- Two backend servers running NGINX Open Source

If a backend server or load balancer fails, NGINX Plus reroutes traffic within the region. If all servers in a region fail, Route 53 redirects traffic to the other region.

### Health monitoring

- Route 53 health checks monitor NGINX Plus instances and fail over if both instances in a region are unavailable.
- NGINX Plus health checks monitor backend servers and remove unhealthy servers from the load balancing pool.

For setup instructions, see [Configuring Global Server Load Balancing](#gslb).

---

## Prerequisites {#prereqs}

Before configuring global server load balancing, ensure you have the following:

- [An AWS account](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/AboutAWSAccounts.html).
- An NGINX Plus subscription, either [purchased](https://www.nginx.com/products/pricing/#nginx-plus) or a [free 30-day trial](https://www.nginx.com/free-trial-request/).
- Familiarity with NGINX and NGINX Plus configuration syntax. This guide provides configuration snippets but does not analyze them in detail.
- Eight EC2 instances, with four in each of two AWS regions.

For instance setup and NGINX installation, see the [Appendix](#appendix).

---

## Configuring global server load balancing {#gslb}

After setting up your AWS environment, configure Amazon Route 53 for global server load balancing (GSLB).

Follow these steps:

- [Create a hosted zone](#hosted-zone)
- [Link the domain to EC2 instances](#link-instances)
- [Configure health checks for failover](#route-53-health-checks)
- [Enable NGINX Plus application health checks](#nginx-plus-health-checks)


### Creating a hosted zone {#hosted-zone}

Create a hosted zone in Amazon Route 53 to manage your domain.

{{<note>}}
If you transfer an existing domain, DNS record propagation can take up to 48 hours. New domains typically propagate faster.
{{</note>}}

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/).
2. Open the **Route 53** service.
3. In the **Registered Domains** tab:
   - Select **Register Domain** to buy a new domain.
   - Select **Transfer Domain** to move an existing domain.
4. Follow the on-screen instructions to complete domain registration or transfer.
5. Route 53 automatically creates a hosted zone when you register or transfer a domain.

If no domain is listed, select **Create Hosted Zone**, enter your domain name, and follow the prompts.

### Linking the domain to EC2 instances {#link-instances}

Associate your domain with EC2 instances by creating Route 53 **record sets**. This enables content to be served from the instances.

To configure global server load balancing (GSLB), set the **Latency** routing policy. This ensures Route 53 directs traffic to the region with the lowest latency.

{{<note>}}
You can use the **Geolocation** routing policy to route traffic based on a user’s location, but failover may not work as expected. This method is best suited for regional content customization, such as displaying local languages or currencies.
{{</note>}}

1. Go to the **Hosted Zones** tab in the Route 53 dashboard.
2. Select your domain.
3. Select **Create Record Set**.
4. Configure the record set:
   - **Name**: (e.g., `www.example.com`).
   - **Type**: `A - IPv4 address`.
   - **Alias**: `No`.
   - **TTL (Seconds)**: `60`.  
     {{<note>}} A lower TTL (such as 60 seconds) speeds up failover detection, but Route 53 requires at least two minutes to fail over.{{</note>}}
   - **Value**: Enter the Elastic IP addresses of your NGINX Plus load balancers.
   - **Routing Policy**: `Latency`.
   - **Region**: Select the AWS region of your load balancers.
   - **Set ID**: Provide an identifier (e.g., `US West LBs`).
   - **Associate with Health Check**: `No` (this will be configured later).
5. Select **Create**.
6. Repeat these steps for the second AWS region.

After completing this setup, Route 53 routes client requests based on network latency, ensuring optimal performance.

### Configuring health checks for Route 53 failover {#route-53-health-checks}

Configure health checks in Route 53 to enable automatic failover between AWS regions.

Route 53 monitors the health of NGINX Plus load balancers and fails over to the next available region if both instances are unresponsive or return non-`200 OK` status codes.

{{<note>}}
Route 53 failover can take up to three minutes, regardless of TTL settings.
{{</note>}}

1. Go to the **Health checks** tab in the Route 53 dashboard.
2. Select **Create Health Check**.
3. Configure the health check:
   - **Name**: Enter a name for the NGINX Plus load balancer (e.g., `US West LB 1`).
   - **What to monitor**: Select `Endpoint`.
   - **Specify endpoint by**: Select `IP address`.
   - **IP Address**: Enter the Elastic IP address of the NGINX Plus instance.
   - **Port**: Set to `80` (or the port your service uses).
4. Select **Next**.
5. On the **Get notified when health check fails** screen, set **Create alarm** to **Yes** or **No** as appropriate.
6. Select **Create health check**.
7. Repeat these steps for each load balancer.

After creating health checks for individual instances, set up regional health checks and link them to Route 53 record sets.

### Configuring Route 53 health checks for paired load balancers {#route-53-health-checks-pair}

Configure Route 53 health checks to monitor multiple load balancers in each AWS region. If both load balancers in a region become unavailable, Route 53 triggers failover to the next region.

1. Select **Create Health Check**.
2. Configure the health check with the following values, then select **Next**:
   - **Name**: Enter a name for the paired NGINX Plus load balancers (e.g., `US West LBs`).
   - **What to monitor**: Select `Status of other health checks`.
   - **Health checks to monitor**: Select the health checks of both load balancers in the region.
   - **Report healthy when**: Choose **at least 1 of 2 selected health checks are healthy**.
3. On the **Get notified when health check fails** screen, set **Create alarm** to **Yes** or **No**, then select **Create Health Check**.
4. Repeat these steps for the paired load balancers in the second AWS region.

After configuring regional health checks, update Route 53 record sets to associate them with these health checks.

### Modifying record sets to associate with health checks {#route-53-health-checks-record-sets}

Update Route 53 record sets to associate them with health checks. This enables automatic failover when NGINX Plus instances become unavailable.

1. Go to the **Hosted Zones** tab in the Route 53 dashboard.
2. Select your domain in the **Domain Name** list.
3. Select the record set for the first AWS region.
4. Under **Associate with Health Check**, select **Yes**.
5. In **Health Check to Associate**, choose the health check for the first region.
6. Select **Save Record Set**.
7. Repeat these steps for the second AWS region.

After updating the record sets, Route 53 will monitor the health of NGINX Plus instances and reroute traffic as needed.

## Configuring NGINX Plus application health checks {#nginx-plus-health-checks}

When using NGINX Plus as a load balancer, configure **application health checks** to monitor backend server availability. Unlike basic checks that only verify server responsiveness, NGINX Plus can inspect response content and other conditions before routing traffic.

Ensure NGINX Plus is installed and configured on two EC2 instances in each region before proceeding.

{{<note>}}
Some commands require `root` privilege. Use `sudo` if necessary.
{{</note>}}

### Steps

1. Connect to the first NGINX Plus load balancer instance in the **US West** region.
2. Navigate to the configuration directory:

   ```bash
   cd /etc/nginx/conf.d
   ```

3. Edit the configuration file (e.g., west-lb1.conf) and add the @healthcheck location:

   ```nginx
   upstream backend-servers {
    server backend1.example.com;
    server backend2.example.com;
    zone backend-servers 64k;
   }

   server {
       location / {
           proxy_pass http://backend-servers;
       }

       location @healthcheck {
           proxy_pass http://backend-servers;
           proxy_connect_timeout 1s;
           proxy_read_timeout 1s;
           health_check uri=/ interval=1s;
       }
   }
   ```

4. Validate and apply the NGINX configuration:

   ```shell
   nginx -t
   nginx -s reload
   ```

5. Repeat these steps for the remaining three load balancers in both regions.


<span id="appendix"></span>
## Appendix

The instructions in this Appendix explain how to create EC2 instances with the names used in this guide, and then install and configure NGINX Open Source and NGINX Plus on them:

- [Creating EC2 Instances and Installing the NGINX Software](#create-instance-install-nginx)
- [Configuring Elastic IP Addresses](#elastic-ip)
- [Configuring NGINX Open Source on the Backend Servers](#configure-backend-servers)
- [Configuring NGINX Plus on the Load Balancers](#configure-load-balancers)

<span id="create-instance-install-nginx"></span>
### Creating EC2 Instances and Installing the NGINX Software

The deployment in this guide uses eight EC2 instances, four in each of two AWS regions. In each region, two instances run NGINX Plus to load balance traffic to the backend (NGINX Open Source) web servers running on the other two instances.

Step‑by‑step instructions for creating EC2 instances and installing NGINX software are provided in our deployment guide, [Creating Amazon EC2 Instances for NGINX Open Source and NGINX Plus]({{< relref "ec2-instances-for-nginx.md" >}}).

**Note:** When installing NGINX Open Source or NGINX Plus, you connect to each instance over SSH. To save time, leave the SSH connection to each instance open after installing the software, for reuse when you configure it with the instructions in the sections below.

Assign the following names to the instances, and then install the indicated NGINX software.

- In the first region, which is <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West (Oregon)</span> in this guide:

  - Two load balancer instances running NGINX Plus:

    - <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 1</span>
    - <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 2</span>

  - Two backend instances running NGINX Open Source:

         * <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 1</span>
    - <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 2</span>

- In the second region, which is <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US East (N. Virginia)</span> in this guide:

  - Two load balancer instances running NGINX Plus:

    - <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US East LB 1</span>
    - <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US East LB 2</span>

  - Two backend instances running NGINX Open Source:

         * <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 3</span>
    - <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 4</span>

Here's the **Instances** tab after we create the four instances in the <span style="color:#666666; font-weight:bolder; white-space: nowrap;">N. Virginia</span> region.

<img src="https://cdn-1.wp.nginx.com/wp-content/uploads/2016/10/aws-ec2-useast-instances.png" alt="Screenshot showing newly created EC2 instances in one of two regions, which is a prerequisite to configuring AWS GSLB (global server load balancing) with NGINX Plus" style="border:2px solid #666666; padding:2px; margin:2px;" />

<span id="elastic-ip"></span>
### Configuring Elastic IP Addresses

For some EC2 instance types (for example, on‑demand instances), AWS by default assigns a different IP address to an instance each time you shut it down and spin it back up. A load balancer must know the IP addresses of the servers to which it is forwarding traffic, so the default AWS behavior requires you either to set up a service‑discovery mechanism or to modify the NGINX Plus configuration every time you shut down or restart a backend instance. (A similar requirement applies to Route 53 when we shut down or restart an NGINX Plus instance.) To get around this inconvenience, assign an _elastic IP address_ to each of the eight instances.

**Note:** AWS does not charge for elastic IP addresses as long as the associated instance is running. But when you shut down an instance, AWS charges a small amount to maintain the association to an elastic IP address. For details, see the Amazon EC2 Pricing page for your pricing model (for example, the **Elastic IP Addresses** section of the [On‑Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/) page).

Perform these steps on all eight instances.

1. Navigate to the **Elastic IPs** tab on the EC2 Dashboard.

   <img src="https://cdn-1.wp.nginx.com/wp-content/uploads/2018/03/aws-ec2-elastic-ips-tab.png" alt="Screenshot of the Elastic IPs tab used during configuration of a new AWS EC2 instance, which is a prerequisite to configuring NGINX GSLB (global server load balancing)" style="border:2px solid #666666; padding:2px; margin:2px;" />

2. Click the <span style="background-color:#3366cc; color:white; white-space: nowrap;"> Allocate New Address </span> button. In the window that pops up, click the <span style="background-color:#3366cc; color:white; white-space: nowrap;"> Yes, Allocate </span> button and then the <span style="background-color:#3366cc; color:white; white-space: nowrap;"> Close </span> button.

3. Associate the elastic IP address with an EC2 instance:

   - Right‑click in the IP address' row and select <span style="background-color:#666666; color:white; white-space: nowrap;"> Associate Address </span> from the drop‑down menu that appears.
   - In the window that pops up, click in the **Instance** field and select an instance from the drop‑down menu.

   Confirm your selection by clicking the <span style="background-color:#3366cc; color:white; white-space: nowrap;"> Associate </span> button.

   <img src="https://cdn-1.wp.nginx.com/wp-content/uploads/2016/10/aws-ec2-associate-address-popup.png" alt="Screenshot of the interface for associating an AWS EC2 instance with an elastic IP address, which is a prerequisite to configuring AWS global load balancing (GLB) with NGINX Plus" style="border:2px solid #666666; padding:2px; margin:2px;" />

After you complete the instructions on all instances, the list for a region (here, <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Oregon</span>) looks like this:

<img src="https://cdn-1.wp.nginx.com/wp-content/uploads/2018/03/aws-ec2-elastic-ip-address-list.png" alt="Screenshot showing the elastic IP addresses assigned to four AWS EC2 instances during configuration of global server load balancing (GSLB) with NGINX Plus" style="border:2px solid #666666; padding:2px; margin:2px;" />

<span id="configure-backend-servers"></span>
### Configuring NGINX Open Source on the Backend Servers

Perform these steps on all four backend servers: <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 1</span>, <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 2</span>, <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 3</span>, and <span style="color:#666666; font-weight:bolder; white-space: nowrap;">Backend 4</span>. In Step 3, substitute the appropriate name for `Backend X` in the **index.html** file.

**Note:** Some commands require `root` privilege. If appropriate for your environment, prefix commands with the `sudo` command.

1. Connect over SSH to the instance (or return to the terminal you left open after installing NGINX Open Source) and change directory to and change directory to your root directory. For the instance in this guide, it is **/home/ubuntu**.

   ```shell
   cd /home/ubuntu
   ```

2. Create a directory called **public_html** and change directory to it.

   ```shell
   mkdir public_html
   cd public_html
   ```

3. Using your preferred text editor, create a new file called **index.html** and add this text to it:

   ```none
   This is Backend X
   ```

4. Change directory to **/etc/nginx/conf.d**.

   ```shell
   cd /etc/nginx/conf.d
   ```

5. Rename **default.conf** to **default.conf.bak** so that NGINX Plus does not load it.

   ```shell
   mv default.conf default.conf.bak
   ```

6. Create a new file called **backend.conf** and add this text, which defines the docroot for this web server:

   ```nginx
   server {
       root /home/ubuntu/public_html;
   }
   ```

   Directive documentation: [root](https://nginx.org/en/docs/http/ngx_http_core_module.html#root), [server](https://nginx.org/en/docs/http/ngx_http_core_module.html#server)

7. Verify the validity of the NGINX configuration and load it.

   ```shell
   nginx -t
   nginx -s reload
   ```

<span id="configure-load-balancers"></span>
### Configuring NGINX Plus on the Load Balancers

Perform these steps on all four backend servers: <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 1</span>, <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 2</span>, <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US East LB 1</span>, and <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 2</span>.

**Note:** Some commands require `root` privilege. If appropriate for your environment, prefix commands with the `sudo` command.

1. Connect over SSH to the instance (or return to the terminal you left open after installing NGINX Plus) and change directory to **/etc/nginx/conf.d**.

   ```shell
   cd /etc/nginx/conf.d
   ```

3. Rename **default.conf** to **default.conf.bak** so that NGINX Plus does not load it.

   ```shell
   mv default.conf default.conf.bak
   ```

4. Create a new file containing the following text, which configures load balancing of the two backend servers in the relevant region. The filename on each instance is:

   - For <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 1</span> – <span style="font-weight:bold; white-space: nowrap;">west-lb1.conf</span>
   - For <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 2</span> – <span style="font-weight:bold; white-space: nowrap;">west-lb2.conf</span>
   - For <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US East LB 1</span> – <span style="font-weight:bold; white-space: nowrap;">east-lb1.conf</span>
   - For <span style="color:#666666; font-weight:bolder; white-space: nowrap;">US West LB 2</span> – <span style="font-weight:bold; white-space: nowrap;">east-lb2.conf</span>

   In the `server` directives in the `upstream` block, substitute the public DNS names of the backend instances in the region; to learn them, see the **Instances** tab in the EC2 Dashboard.

   ```nginx
   upstream backend-servers {
       server <public DNS name of Backend 1>; # Backend 1
       server <public DNS name of Backend 2>; # Backend 2
   }
   server {
       location / {
           proxy_pass http://backend-servers;
       }
   }
   ```

   Directive documentation: [location](https://nginx.org/en/docs/http/ngx_http_core_module.html#location), [proxy_pass](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass), [server virtual](https://nginx.org/en/docs/http/ngx_http_core_module.html#server), [server upstream](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#server), [upstream](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#upstream)

5. Verify the validity of the NGINX configuration and load it.

   ```shell
   nginx -t
   nginx -s reload
   ```

8. To test that the configuration is working correctly, for each load balancer enter its public DNS name in the address field of your web browser. As you access the load balancer repeatedly, the content of the page alternates between "This is Backend 1" and "This is Backend 2" in your first region, and "This is Backend 3" and "This is Backend 4" in the second region.

Now that all eight EC2 instances are configured and local load balancing is working correctly, we can set up global server load balancing with Route 53 to route traffic based on the IP address of the requesting client.

Return to main instructions, [Configuring Global Server Load Balancing](#gslb)

### Revision History

- Version 3 (April 2018) – Reorganization of Appendix
- Version 2 (January 2017) – Clarified information about root permissions; miscellaneous fixes (NGINX Plus Release 11)
- Version 1 (October 2016) – Initial version (NGINX Plus Release 10)

