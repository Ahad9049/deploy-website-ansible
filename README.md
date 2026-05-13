# 🚀 Deploy Website with Ansible

Automated, role-based deployment of dynamic web pages across Ubuntu & Amazon Linux EC2 instances using Ansible and Jinja2 templating.

[![Ansible](https://img.shields.io/badge/Ansible-2.9%2B-red?logo=ansible)](https://docs.ansible.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Ubuntu%20%7C%20Amazon%20Linux-yellow)](https://aws.amazon.com/ec2/)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Usage](#usage)
- [Key Features](#key-features)
- [Troubleshooting](#troubleshooting)
- [Acknowledgments](#acknowledgments)

---

## 🎯 Overview

This project solves a real-world DevOps challenge: **deploying dynamic landing pages consistently across multiple EC2 instances running different Linux distributions** — without manual SSH sessions or config drift.

Instead of editing files server-by-server, define your infrastructure once with Ansible roles and Jinja2 templates, then deploy everywhere with a single command.

---

## 🏗️ Architecture

```
┌─────────────────┐     ┌─────────────────┐
│   Control Node  │────▶│  EC2 Instances  │
│  (Your Laptop)  │     │                 │
│                 │     │  ┌───────────┐  │
│  site.yaml      │────▶│  │  Ubuntu   │  │
│  roles/web/     │     │  │  Nginx    │  │
│  Jinja2 Templates│    │  │  App      │  │
│                 │     │  └───────────┘  │
│  requirements.yaml│───▶│                 │
│                 │     │  ┌───────────┐  │
│                 │────▶│  │  Amazon   │  │
│                 │     │  │  Linux 2  │  │
│                 │     │  │  Nginx    │  │
│                 │     │  │  App      │  │
│                 │     │  └───────────┘  │
└─────────────────┘     └─────────────────┘
```

---

## ⚡ Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/Ahad9049/deploy-website-ansible.git
cd deploy-website-ansible
```

### 2. Install Ansible Roles
```bash
ansible-galaxy install -r requirements.yaml
```

### 3. Configure Your Inventory
Edit `inventory/` or `ubuntu_servers.yaml` with your EC2 instance IPs:
```yaml
[webservers]
54.160.x.x ansible_user=ubuntu ansible_ssh_private_key_file=~/.keys/my-key.pem
34.229.x.x ansible_user=ec2-user ansible_ssh_private_key_file=~/.keys/my-key.pem
```

### 4. Run the Playbook
```bash
ansible-playbook -i inventory site.yaml
```

### 5. Verify Deployment
```bash
curl http://<your-ec2-ip>
```

---

## 📁 Project Structure

```
deploy-website-ansible/
├── ansible.cfg              # Ansible configuration
├── requirements.yaml        # External roles dependencies
├── site.yaml                # Main playbook
├── ubuntu_servers.yaml      # Ubuntu-specific server variables
├── inventory/               # Host definitions
│   └── hosts                # Inventory file
├── group_vars/              # Group-specific variables
│   └── all.yaml             # Shared variables across hosts
├── roles/
│   └── web/                 # Custom web deployment role
│       ├── tasks/           # Role tasks
│       ├── templates/       # Jinja2 templates (HTML, Nginx)
│       ├── handlers/        # Service restart handlers
│       └── defaults/        # Default variables
└── README.md                # This file
```

---

## ⚙️ Configuration

### Variables (`group_vars/all.yaml`)
```yaml
---
# Application settings
app_name: "dynamic-landing-page"
app_version: "1.0.0"

# Nginx configuration
nginx_port: 80
server_name: "_"

# Dynamic content (Jinja2-templated)
campaign_title: "Summer Launch 2026"
launch_date: "2026-06-01"
```

### Dynamic Templating with Jinja2
The `roles/web/templates/` directory contains Jinja2 templates that render differently based on:
- **OS family** (`ansible_os_family`: Debian vs. RedHat)
- **Host variables** (per-server customization)
- **Group variables** (shared configuration)

Example Nginx config template:
```jinja2
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};

    root /var/www/{{ app_name }};
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

---

## 🚀 Usage

### Deploy to All Servers
```bash
ansible-playbook -i inventory site.yaml
```

### Deploy to Specific Group
```bash
ansible-playbook -i inventory site.yaml --limit webservers
```

### Check Mode (Dry Run)
```bash
ansible-playbook -i inventory site.yaml --check
```

### Verbose Output
```bash
ansible-playbook -i inventory site.yaml -vvv
```

---

## ✨ Key Features

| Feature | Description |
|---------|-------------|
| **🎭 Role-Based Automation** | Modular `roles/web` structure via `requirements.yaml` |
| **📄 Dynamic Templating** | Jinja2 renders HTML & Nginx configs on-the-fly |
| **🔧 Variable-Driven** | Single source of truth in `group_vars/` |
| **🐧 Multi-OS Compatible** | One playbook handles Ubuntu & Amazon Linux |
| **⚡ Idempotent** | Safe to run multiple times without side effects |
| **🔒 Secure** | Uses SSH key authentication, no password exposure |

---

## 🛠️ Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| Ansible | 2.9+ | Automation engine |
| Python | 3.8+ | Ansible dependency |
| AWS CLI | 2.x | EC2 instance management (optional) |
| SSH Key | — | Secure server access |

Install Ansible:
```bash
# Ubuntu/Debian
sudo apt update && sudo apt install ansible

# macOS
brew install ansible

# Python pip
pip install ansible
```

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| `Permission denied (publickey)` | Verify `ansible_ssh_private_key_file` path and permissions (`chmod 600`) |
| `Failed to connect to host` | Check security group allows SSH (port 22) from your IP |
| `Nginx not found` | Ensure OS-specific package names are correct in role variables |
| `Template not rendering` | Verify Jinja2 syntax with `ansible-playbook --check` |

---

## 🙏 Acknowledgments

- **Miseacademy** — For comprehensive DevOps training and mentorship
- **Hafiz Muhammad Umair Munir** — For making infrastructure-as-code concepts practical and impactful

---

## 📜 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🔗 Connect

Built by **Abdul Ahad** ([@Ahad9049](https://github.com/Ahad9049))

⭐ Star this repo if it helped you!  
🐛 Found an issue? [Open a ticket](https://github.com/Ahad9049/deploy-website-ansible/issues)

---

> *"The best infrastructure is the kind you don't have to think about."*
