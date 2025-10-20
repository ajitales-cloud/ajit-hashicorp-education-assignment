# Getting Started with Terraform and Docker

## Introduction
In this guide, you will learn how to use Terraform to deploy and manage a simple containerized application using the Docker provider.

You will:

- Install and configure Terraform  
- Create a working directory and configuration  
- Deploy and destroy an NGINX container  

By the end, you’ll understand the Terraform workflow:  
**init → plan → apply → destroy**

---

## Prerequisites

Make sure you have the following tools installed:

- **Terraform** v1.7.0 or later → [Install Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)  
- **Docker** (latest version) → [Install Docker](https://docs.docker.com/get-docker/)  
- Access to a **terminal** or **command line**

Verify installation:

```bash
terraform -version
docker --version
```

**Expected output:**
```
Terraform v1.7.5
Docker version 27.1.1, build 7d7afcc
```

---

## Step 1: Create a Working Directory

Create a new folder to hold your Terraform configuration:

```bash
mkdir terraform-demo
cd terraform-demo
```

This is your workspace — Terraform will store configuration files and state data here.

---

## Step 2: Write the Terraform Configuration

Create a configuration file:

```bash
touch main.tf
```

Open it in your text editor and add:

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0"
    }
  }
}

provider "docker" {
  host = "unix:///var/run/docker.sock"
}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 8080
  }
}
```

**Explanation:**

- The `terraform` block defines required providers.  
- The `provider` block connects Terraform to your local Docker socket.  
- The `docker_image` resource pulls the NGINX image.  
- The `docker_container` resource runs a container on port 8080.

---

## Step 3: Initialize Terraform

Run this command to install dependencies:

```bash
terraform init
```

**Example output:**

```
Initializing provider plugins...
- Installing kreuzwerker/docker v3.0.2...
Terraform has been successfully initialized!
```

This sets up your working directory and downloads the provider dependencies.

---

## Step 4: Apply the Configuration

Apply your configuration to deploy NGINX:

```bash
terraform apply
```

When prompted, type `yes`.

**Example output:**

```
docker_image.nginx: Creating...
docker_container.nginx: Creating...
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Check that your container is running:

```bash
docker ps
```

**Example output:**

```
CONTAINER ID   IMAGE          STATUS         PORTS
1f9a2b9b1b0f   nginx:latest   Up 10 seconds  0.0.0.0:8080->80/tcp
```

Then open your browser to [http://localhost:8080](http://localhost:8080).  
You should see the NGINX welcome page.

---

## Step 5: Destroy the Infrastructure

To remove all resources and clean up:

```bash
terraform destroy
```

When prompted, type `yes`.

**Example output:**

```
docker_container.nginx: Destroying...
docker_image.nginx: Destroying...
Destroy complete! Resources: 2 destroyed.
```

---

## Troubleshooting

| Issue | Cause | Fix |
|-------|--------|-----|
| `Error connecting to Docker socket` | Docker daemon not running | Start Docker Desktop or run `sudo service docker start` |
| `Plugin install failed` | Internet or registry unavailable | Run `terraform init -upgrade` |
| `Port 8080 already in use` | Another app using the port | Change `external = 8081` and reapply |

---

## Next Steps

You’ve learned how to:

- Write and run Terraform configurations  
- Initialize and apply Terraform workflows  
- Manage Docker containers with IaC principles  

Continue learning:

- [Docker Provider Docs](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs)  
- [Terraform Modules Overview](https://developer.hashicorp.com/terraform/language/modules/develop)  
- [HashiCorp Developer Tutorials](https://developer.hashicorp.com/terraform/tutorials)
