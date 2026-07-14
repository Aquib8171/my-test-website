# Static Website Deployment via GitHub Actions to AWS EC2

This repository contains the source code for a responsive static website, along with a fully automated CI/CD pipeline built using **GitHub Actions**. Every push to the `main` branch triggers an automated secure transfer of files to an AWS EC2 instance running an optimized **Nginx** web server.

---

## 🚀 CI/CD Pipeline Architecture

The workflow is designed to ensure zero manual intervention for updates:
1. **Trigger:** A developer pushes code to the `main` branch.
2. **Checkout:** The GitHub runner fetches the latest code using `actions/checkout@v4`.
3. **Deploy (SCP):** The runner securely copies all files to the target EC2 path (`/var/www/html`) using `appleboy/scp-action`.
4. **Reload (SSH):** The runner establishes an SSH connection using `appleboy/ssh-action` to gracefully reload the Nginx server, making changes live instantly.

---

## 🛠️ Prerequisites & Infrastructure Setup

Before making any changes to the pipeline, ensure the following configurations are in place on the target Ubuntu server:

### 1. Nginx Directory Permissions
Nginx needs appropriate read permissions to serve the files, and the deployer user (`ubuntu`) needs write access to transfer them:
```bash
sudo chown -R ubuntu:www-data /var/www/html
sudo chmod -R 755 /var/www/html
