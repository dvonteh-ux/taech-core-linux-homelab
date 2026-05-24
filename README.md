# Taech-Core Linux Homelab

Building a Linux-based homelab and self-hosted infrastructure platform for IT engineering, automation, containerization, and remote systems management.

## Project Overview

Taech-Core is my personal Linux homelab build designed to support hands-on learning, self-hosted services, remote administration, automation workflows, and future AI tooling.

The goal was to convert older Apple hardware into a reliable always-on engineering workstation capable of running:

* Ubuntu Linux
* Docker containers
* Portainer
* Tailscale remote access
* RustDesk remote desktop
* VS Code
* Git/GitHub workflows
* Future OpenClaw / AI agent tooling
* Future Pi-hole, monitoring, and NAS-style services

## Hardware Used

* Dedicated always-on Linux infrastructure node
* Portable monitor
* Ethernet connection
* External SSD for macOS fallback/recovery
* USB installer for Ubuntu
* iPad/MacBook/phone for remote management testing

## Operating System

* Ubuntu Desktop
* Installed to the internal drive
* Configured as the primary operating system for Taech-Core

## Core Tools Installed

| Tool      | Purpose                      |
| --------- | ---------------------------- |
| Ubuntu    | Primary Linux OS             |
| Docker    | Container runtime            |
| Portainer | Docker web GUI               |
| Tailscale | Secure remote network access |
| RustDesk  | Remote desktop access        |
| Git       | Version control              |
| VS Code   | Development environment      |
| Chrome    | Web-based admin tools        |

## Network Configuration

Taech-Core was configured with a stable LAN IP through the Verizon router admin console.

The router DHCP range was reviewed and the device was organized for predictable access across the home lab.

Remote access was also configured through Tailscale, allowing Taech-Core to be reached securely from other personal devices.

## Docker Setup

Docker was installed and verified with:

```bash
docker --version
sudo systemctl status docker
```

Docker was confirmed running successfully before deploying Portainer.

## Portainer Setup

A Docker volume was created for Portainer data:

```bash
sudo docker volume create portainer_data
```

Portainer was launched using:

```bash
sudo docker run -d \
-p 8000:8000 \
-p 9443:9443 \
--name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v portainer_data:/data \
portainer/portainer-ce:lts
```

Portainer was then accessed at:

```text
https://localhost:9443
```

The local Docker environment was connected using the Docker socket.

## Troubleshooting Notes

### Portainer unable to connect to Docker daemon

#### Symptoms

Portainer launched successfully and the web UI was reachable, but the local Docker environment failed to connect with an error similar to:

```text
Cannot connect to Docker daemon. Is the daemon running?
```

#### Root Cause

The Docker socket bind mount contained a typo.

Incorrect:

```bash
-v /var/run/docker.sock:/var/run/dcoker.sock
```

Correct:

```bash
-v /var/run/docker.sock:/var/run/docker.sock
```

The container was able to start, but Portainer could not communicate with Docker because the socket destination path was incorrect.

#### Resolution

The broken Portainer container was removed:

```bash
sudo docker rm -f portainer
```

Then recreated with the correct socket mount:

```bash
sudo docker run -d \
-p 8000:8000 \
-p 9443:9443 \
--name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v portainer_data:/data \
portainer/portainer-ce:lts
```

The mount was validated using:

```bash
sudo docker inspect portainer | grep -A 10 Mounts
```

## Remote Access

### Tailscale

Tailscale was installed to allow secure access to Taech-Core from personal devices without router port forwarding.

### RustDesk

RustDesk was installed for remote desktop access from iPad and other devices.

Wayland screen-sharing limitations were identified, so the system was adjusted toward Xorg/X11 for better unattended remote access behavior.

## Skills Demonstrated

* Linux installation and configuration
* Repurposing older hardware for infrastructure use
* Docker installation and service validation
* Containerized application deployment
* Portainer setup and Docker socket troubleshooting
* Remote access setup using Tailscale and RustDesk
* Router-side network planning
* Package management troubleshooting
* Practical homelab documentation

## Current Status

Taech-Core is now operational as a Linux-based homelab foundation with Docker, Portainer, Tailscale, and remote desktop access in place.

## Next Steps

* Deploy Uptime Kuma
* Deploy FileBrowser
* Configure OpenClaw / AI tooling
* Add Pi-hole
* Build monitoring dashboard
* Automate GitHub lab repo updates
* Document each service deployment as separate lab write-ups
* Create a full Taech-Core architecture diagram
