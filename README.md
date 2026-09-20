# EC2 Web Server with CloudWatch Alarm

Launched a Linux EC2 instance (Amazon Linux 2023) that serves a simple web page, and set up a CloudWatch alarm to monitor its CPU usage.

![AWS](https://img.shields.io/badge/AWS-EC2-orange) ![AWS](https://img.shields.io/badge/AWS-CloudWatch-orange) ![Linux](https://img.shields.io/badge/OS-Amazon%20Linux%202023-blue)

## Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Services Used](#services-used)
- [Configuration](#configuration)
- [Step-by-Step Setup](#step-by-step-setup)
- [Screenshots](#screenshots)
- [Clean Up](#clean-up)
- [What I Learned](#what-i-learned)

## Project Overview

The goal of this project was to get hands-on experience with two core AWS services:

- **Amazon EC2** to launch and run a Linux virtual server
- **Amazon CloudWatch** to monitor the server and raise an alarm when CPU usage is high

The server is set up automatically at launch using a **user data** script, so no manual login to the server was needed.

## Architecture

```
   User (Browser)
        |
        |  HTTP (port 80)
        v
+---------------------------+
|  EC2 Instance             |
|  t3.micro                 |
|  Amazon Linux 2023        |
|  us-east-1 (N. Virginia)  |
+---------------------------+
        |
        |  CPUUtilization metric
        v
+---------------------------+
|  CloudWatch Alarm         |
|  CPU >= 70% (5 minutes)   |
+---------------------------+
```

## Services Used

| Service | Purpose |
|---|---|
| Amazon EC2 | Runs the Linux virtual server |
| Amazon CloudWatch | Monitors CPU usage and raises an alarm |

## Configuration

| Setting | Value |
|---|---|
| Instance name | `achu-web-server` |
| Region | US East (N. Virginia) |
| Operating system | Amazon Linux 2023 |
| Instance type | `t3.micro` (free tier eligible) |
| Key pair | None (server setup done through user data) |
| Inbound traffic | HTTP (port 80) |
| Alarm name | `achu-high-cpu-alarm` |
| Alarm metric | CPUUtilization (Average) |
| Alarm condition | Greater than or equal to 70% |
| Alarm period | 5 minutes, 1 consecutive period |
| Alarm notification | None (kept simple for this demo) |

## Step-by-Step Setup

### 1. Launch the EC2 instance

1. Open the EC2 console and click **Launch instance**.
2. Name it `achu-web-server`.
3. Choose **Amazon Linux** (Amazon Linux 2023, free tier eligible).
4. Choose instance type **t3.micro**.
5. Select **Proceed without a key pair**.
6. Under network settings, allow **HTTP traffic from the internet**.

### 2. Add the user data script

Under **Advanced details > User data**, paste:

```bash
#!/bin/bash
dnf install -y httpd
systemctl enable --now httpd
echo "<h1>Hello from Achu's EC2 server!</h1>" > /var/www/html/index.html
```

This script runs once when the server first starts. It installs a web server, starts it, and creates the web page.

### 3. Verify the server

1. Wait until the instance shows **Running** and **3/3 status checks passed**.
2. Copy the **Public IPv4 address**.
3. Open `http://<public-ip>` in a browser (use `http`, not `https`).
4. The page shows: **Hello from Achu's EC2 server!**

### 4. Create the CloudWatch alarm

1. Select the instance, then **Actions > Monitor and troubleshoot > Manage CloudWatch alarms**.
2. Set the metric to **CPU utilization** (Average).
3. Set the threshold to **70**, with a **5 minute** period and **1** consecutive period.
4. Name the alarm `achu-high-cpu-alarm` and click **Create**.

## Screenshots

### Web page running on the EC2 instance
![Web page](screenshots/01-web-page.png)

### CloudWatch alarm (state: OK)
![CloudWatch alarm](screenshots/02-cloudwatch-alarm.png)

### EC2 instance running with 3/3 status checks passed
![Instance running](screenshots/03-instance-running.png)

## Clean Up

To avoid charges, I deleted the resources after taking the screenshots:

1. Deleted the CloudWatch alarm `achu-high-cpu-alarm`
2. Terminated the EC2 instance `achu-web-server`

## What I Learned

- How to launch and configure a Linux EC2 instance
- How to automate server setup at launch with a user data script
- How to open a web page hosted on an EC2 instance using its public IP
- How to create a CloudWatch alarm to monitor CPU usage
- Why it is important to clean up resources after a project

## Author

**Achu**
