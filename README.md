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
2. Nginx Routing ConfigurationTo handle custom page routing (e.g., serving home.html as the default root page and handling extensionless URLs like /about), the virtual host configuration (/etc/nginx/sites-available/my-website) must be configured as follows:Nginxserver
{
    listen 80;
    listen [::]:80;

    server_name YOUR_SERVER_IP_OR_DOMAIN;

    root /var/www/html;
    index home.html home.htm;

    location / {
        try_files $uri $uri/ $uri.html /home.html;
    }
}
Note: Ensure this configuration is symlinked to /etc/nginx/sites-enabled/ and default landing configs are removed.
🔐 GitHub Secrets ConfigurationTo allow GitHub Actions to safely authenticate with your private EC2 instance without exposing credentials,
the following Repository Secrets must be added under Settings > Secrets and variables > Actions:Secret NameDescriptionExample ValueEC2_HOST
The Public IPv4 address of your AWS EC2 instance43.205.x.xEC2_SSH_KEYThe contents of your private key file (.pem)-----BEGIN RSA PRIVATE KEY----- ...
Note: The deployment username (ubuntu) is hardcoded directly into the workflow file to ensure robust parsing.
📂 Project StructurePlaintext├── .github/
│   └── workflows/
│       └── deploy.yml       # GitHub Actions Workflow configuration
├── images/                  # Media assets (logos, background images)
├── home.html                # Main entrance/landing page
└── README.md                # Project documentation
⚙️ How to Deploy Changes Local-to-RemoteWhenever you add new features or modify pages locally, use the standard Git workflow to push to GitHub, which automatically kicks off the deployment:Bash# 1. Stage all modifications
git add .

# 2. Commit changes with a meaningful message
git commit -m "feat: updated website content and routing"

# 3. Push to main branch
git push origin main
You can monitor the live execution logs under the Actions tab of this GitHub repository. A green checkmark indicates that the files have been transferred and Nginx has successfully reloaded.
