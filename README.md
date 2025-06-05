# PearlThoughts Assignment Deployment

This repository automates the deployment of a Yii2 PHP application using Docker Swarm, NGINX, and Ansible.

---

## 📦 Project Structure

```
ansible/
├── deploy.yml                 # Main playbook
├── inventory.ini              # Inventory file
├── roles/
│   ├── docker/                # Docker installation and setup
│   ├── docker_swarm/          # Docker Swarm init and join
│   ├── nginx/                 # NGINX install and reverse proxy config
│   └── app/                   # App cloning and stack deploy
└── nginx.conf                 # Reverse proxy configuration file
```

---

## ⚙️ Setup Instructions

### 1. Prerequisites
- An EC2 instance (Amazon Linux 2023) with:
  - A public IP address
  - SSH key access
- A local system with:
  - Ansible installed (`pip install ansible`)
  - Your EC2 SSH key (e.g., `key.pem`) available

### 2. Configure Inventory

Edit `inventory.ini`:

```ini
[all]
your.ec2.ip.address ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/key.pem
```

### 3. Run the Playbook

To execute a dry run:
```bash
ansible-playbook deploy.yml -i inventory.ini --check --diff
```

To run actual deployment:
```bash
ansible-playbook deploy.yml -i inventory.ini
```

---

## 🧠 Assumptions

- The EC2 instance does **not** have Docker, Docker Compose, or Git pre-installed.
- The Yii2 app repository is:  
  `https://github.com/shaggyyy2002/pearlThoughts-assignment.git`
- The repo is cloned into `/home/ec2-user/pearlthoughts-assignment`
- The application is already containerized using Docker and includes `docker-compose-prod.yml`.
- The app listens on port `8000` internally.
- NGINX acts as a reverse proxy forwarding traffic to `http://127.0.0.1:8000`.

---

## ✅ How to Test Deployment

Once the playbook completes:

1. SSH into the instance:
   ```bash
   ssh -i ~/.ssh/key.pem ec2-user@your.ec2.ip.address
   ```

2. Check running services:
   ```bash
   docker service ls
   docker ps
   ```

3. Check NGINX status:
   ```bash
   sudo systemctl status nginx
   ```

4. Open your browser:
   - Navigate to `http://your.ec2.ip.address`
   - You should see the Yii2 application’s home page

---

## 📎 Notes

- If you rerun the playbook, it will skip repo cloning if the directory already exists.
- If Docker Swarm is already initialized, it won't re-init again.
- You can change the NGINX config (`nginx.conf`) if your app runs on a different port.

---