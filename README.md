# Ansible-Docker Portfolio Deployment

### Automated Deployment of a Student Portfolio Website Across Ubuntu and CentOS Using Ansible and Docker

A multi-platform infrastructure automation project that uses **Ansible** and **Docker** to build, deploy, and verify a containerized student portfolio website across Ubuntu and CentOS Linux virtual machines.

This project was developed by **Team 4** as part of an academic activity demonstrating configuration management, containerization, and automated application deployment.

---

## 1. Project Overview

The project demonstrates how Ansible can automate Docker-based web application deployment across multiple Linux servers.

A student portfolio website is packaged into a Docker image using NGINX and deployed across Ubuntu and CentOS virtual machines. Ansible handles Docker installation, image building, container deployment, and service verification.

The portfolio website showcases the academic profiles, hands-on activities (HOAs), examinations, and reflections of the four team members.

## 2. Project Objectives

- Automate Docker installation and configuration on Ubuntu and CentOS.
- Build a containerized web application using Docker and NGINX.
- Deploy the same Docker image across multiple Linux servers using Ansible.
- Manage multiple deployment targets through an Ansible inventory.
- Automate verification of deployed containers and HTTP services.
- Demonstrate cross-platform deployment using a single Ansible controller.
- Maintain project source code and configurations through GitHub.

## 3. Technologies Used

| Technology | Purpose |
|---|---|
| Ansible | Infrastructure automation and configuration management |
| Docker Engine | Application containerization |
| Docker Buildx | Building the Docker image |
| NGINX | Web server hosting the student portfolio |
| Ubuntu Linux | Ansible controller and deployment targets |
| CentOS Linux | Additional Linux deployment target |
| HTML5 | Website structure and content |
| CSS3 | Website styling and responsive layout |
| JavaScript | Interactive portfolio features |
| Git and GitHub | Source code management and collaboration |

## 4. System Architecture

The project uses one Ansible controller and three target virtual machines.

| Machine | IP Address | Role |
|---|---|---|
| Ubuntu Workstation | `192.168.56.103` | Ansible Controller |
| Ubuntu VM1 | `192.168.56.101` | Docker Deployment Target |
| Ubuntu VM2 | `192.168.56.102` | Docker Deployment Target |
| CentOS VM | `192.168.56.104` | Docker Deployment Target |

### Deployment Workflow

```text
                  ANSIBLE CONTROLLER
                 Ubuntu Workstation
                   192.168.56.103
                          |
                          |
                    SSH / Ansible
                          |
             +------------+------------+
             |            |            |
         Ubuntu VM1   Ubuntu VM2    CentOS VM
          .101          .102          .104
             |            |            |
           Docker       Docker       Docker
             |            |            |
           NGINX        NGINX        NGINX
             |            |            |
        Portfolio    Portfolio    Portfolio
          :8080        :8080        :8080
```

The Ansible controller transfers the application files to each target, builds the Docker image, and deploys a container serving the same student portfolio website.

## 5. Project Directory Structure

```text
ansible-portfolio/
├── ansible.cfg
├── inventory.ini
├── requirements.yml
├── Dockerfile
├── 01-install-docker.yml
├── 02-build-image.yml
├── 03-deploy-container.yml
├── 04-verify.yml
├── site/
│   ├── index.html
│   ├── style_updated.css
│   ├── images/
│   ├── videos/
│   └── files/
└── README.md
```

### File Descriptions

| File | Description |
|---|---|
| `ansible.cfg` | Configures Ansible inventory and connection settings |
| `inventory.ini` | Defines the Ubuntu and CentOS managed hosts |
| `requirements.yml` | Specifies required Ansible collections |
| `Dockerfile` | Packages the portfolio website using NGINX |
| `01-install-docker.yml` | Installs and configures Docker on target servers |
| `02-build-image.yml` | Transfers website files and builds the Docker image |
| `03-deploy-container.yml` | Deploys the portfolio Docker containers |
| `04-verify.yml` | Verifies container operation and website accessibility |
| `site/index.html` | Student portfolio webpage |
| `site/style_updated.css` | Website styling and responsive layout |

## 6. Prerequisites

### Ansible Controller

- Ubuntu Linux
- Ansible
- Git
- SSH client
- Network connectivity to all target VMs
- Access to the GitHub repository

### Managed Hosts

- Ubuntu or CentOS Linux
- SSH server enabled
- Python 3
- Sudo privileges
- Internet connectivity to install Docker and retrieve images
- Available port `8080`

The controller must be able to communicate with all managed hosts over SSH.

## 7. Installation and Deployment

### Step 1 — Clone the Repository

```bash
git clone https://github.com/piosjoshua/Ansible-Docker-Portfolio-Deployment.git
cd Ansible-Docker-Portfolio-Deployment
```

### Step 2 — Install Ansible

On the Ubuntu controller:

```bash
sudo apt update
sudo apt install -y ansible git openssh-client
```

### Step 3 — Install the Required Ansible Collection

```bash
ansible-galaxy collection install -r requirements.yml
```

The project requires the `community.docker` collection to automate Docker image building and container management.

### Step 4 — Configure Inventory

Edit `inventory.ini`:

```ini
[ubuntu_targets]
ubuntu-vm1 ansible_host=192.168.56.101 ansible_user=YOUR_UBUNTU1_USERNAME
ubuntu-vm2 ansible_host=192.168.56.102 ansible_user=YOUR_UBUNTU2_USERNAME

[centos_targets]
centos-vm ansible_host=192.168.56.104 ansible_user=YOUR_CENTOS_USERNAME

[image_targets:children]
ubuntu_targets
centos_targets

[all_targets:children]
ubuntu_targets
centos_targets
```

Replace the username placeholders with the actual usernames of the target machines.

### Step 5 — Configure SSH Access

Ensure SSH is running on each target machine.

For Ubuntu:

```bash
sudo systemctl enable --now ssh
```

For CentOS:

```bash
sudo systemctl enable --now sshd
```

From the Ansible controller, copy the SSH public key to each target:

```bash
ssh-copy-id USERNAME@192.168.56.101
ssh-copy-id USERNAME@192.168.56.102
ssh-copy-id USERNAME@192.168.56.104
```

Replace `USERNAME` with each VM's actual account name.

### Step 6 — Test Ansible Connectivity

```bash
ansible all_targets -m ping
```

All three managed hosts should return `SUCCESS` and `pong`.

### Step 7 — Install Docker

```bash
ansible-playbook 01-install-docker.yml -K
```

This playbook handles Docker setup and Buildx verification on the Ubuntu and CentOS targets.

### Step 8 — Build the Docker Image

```bash
ansible-playbook 02-build-image.yml -K
```

The playbook transfers the portfolio website files to the managed machines and builds:

```text
academic-portfolio:latest
```

### Step 9 — Deploy Docker Containers

```bash
ansible-playbook 03-deploy-container.yml -K
```

The playbook creates and starts the container:

```text
academic-portfolio-container
```

The application is exposed using the following port mapping:

```text
8080:80
```

### Step 10 — Verify Deployment

```bash
ansible-playbook 04-verify.yml -K
```

The verification playbook checks whether the container is running and whether the portfolio website responds correctly over HTTP.

## 8. Accessing the Portfolio Website

Once deployment succeeds, the website can be accessed at:

| Server | URL |
|---|---|
| Ubuntu VM1 | `http://192.168.56.101:8080` |
| Ubuntu VM2 | `http://192.168.56.102:8080` |
| CentOS VM | `http://192.168.56.104:8080` |

These IP addresses are part of the local virtual-machine network and may not be accessible from outside the laboratory environment.

### Manual HTTP Verification

```bash
curl -I http://192.168.56.101:8080
curl -I http://192.168.56.102:8080
curl -I http://192.168.56.104:8080
```

The expected result is an HTTP `200 OK` response.

## 9. Docker Containerization

The website is containerized using the following Dockerfile:

```dockerfile
FROM nginx:alpine

COPY site/ /usr/share/nginx/html/

EXPOSE 80
```

The Docker image contains the website's HTML, CSS, and associated assets.

NGINX serves the application inside the container on port `80`, which is mapped to port `8080` on the host.

This approach provides a consistent deployment environment across the Ubuntu and CentOS target machines.

## 10. Website Features

The student portfolio website includes:

- Navigation and landing page
- Four individual student portfolios
- Student profile information
- Preliminary, midterm, and final hands-on activities
- Preliminary, midterm, and final examinations
- Reflection and learning sections
- Demonstration and presentation video section
- Interactive student portfolio modals
- Responsive styling

Some linked images, documents, and videos require their corresponding files to be present in the `site/` directory.

## 11. Verification and Testing

The following tests are used to validate the implementation:

| Test | Expected Result |
|---|---|
| Ansible connectivity | All managed hosts respond |
| Docker installation | Docker Engine and Buildx available |
| Docker image build | `academic-portfolio:latest` created |
| Container deployment | Portfolio container running |
| HTTP verification | Status code `200` |
| Website content | Correct Team 4 portfolio displayed |
| CSS verification | Updated stylesheet accessible |
| Cross-platform deployment | Website runs on Ubuntu and CentOS |

Successful execution should produce an Ansible `PLAY RECAP` with no failed or unreachable hosts.

## 12. Documentation and Evidence

The project documentation may include screenshots of:

1. Ansible inventory and project files
2. Successful connection to Ubuntu and CentOS
3. Docker installation and Buildx verification
4. Docker image-building process
5. Successful container deployment
6. Running Docker containers
7. Portfolio website displayed in the browser
8. Final verification results

Screenshots are intended solely as implementation evidence and are not required for the website or Docker containers to function.

## 13. Updating the Website

To update the portfolio website, replace or modify:

```text
site/index.html
site/style_updated.css
```

Then rebuild and redeploy:

```bash
ansible-playbook 02-build-image.yml -K
ansible-playbook 03-deploy-container.yml -K
ansible-playbook 04-verify.yml -K
```

The updated website will be deployed to the configured Ubuntu and CentOS targets.

## 14. Version Control

Changes are managed through Git and GitHub.

After modifying the project:

```bash
git add .
git commit -m "Update portfolio website and Ansible deployment"
git push
```

GitHub stores the project source code and deployment configurations. Docker deployment is performed separately by Ansible.

## 15. Team Members

**Team 4**

- Nadine Malabanan
- John Marvin Masongsong
- Joshua Paul Pios
- Vander ohe Vernon Villareal

## 16. Conclusion

This project demonstrates the use of Ansible and Docker to automate the deployment of a student portfolio website across different Linux operating systems.

By integrating infrastructure automation, containerization, and centralized configuration management, the project provides a repeatable deployment process that reduces manual installation and configuration tasks.

The implementation illustrates how the same web application can be packaged, deployed, and verified consistently across Ubuntu and CentOS servers.

---

## Repository

**GitHub:** https://github.com/piosjoshua/Ansible-Docker-Portfolio-Deployment

**Project:** Ansible-Docker Portfolio Deployment

**Team:** Team 4
