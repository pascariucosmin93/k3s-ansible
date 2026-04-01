# k3s-ansible

Ansible project for preparing an Ubuntu VM and installing a single-node `k3s` cluster.

This repo is designed as a compact infrastructure layer for a personal cluster, lab environment, or GitOps demo target.

It covers both phases:

1. VM bootstrap
2. `k3s` installation and base configuration

## Features

The bootstrap flow handles:

- package update and upgrade
- CLI tooling for administration
- hostname configuration
- admin user creation
- passwordless sudo setup
- Docker installation
- optional `qemu-guest-agent`
- optional UFW firewall setup

The `k3s` flow handles:

- rendering `/etc/rancher/k3s/config.yaml`
- installing the `k3s` server
- enabling and starting the `k3s` service
- optional cluster token support
- optional `cluster-init`
- disabling bundled components such as Traefik
- readable kubeconfig on the target VM

## Structure

```text
.
├── .gitignore
├── ansible.cfg
├── group_vars/
├── inventory/
├── playbooks/
├── requirements.yml
└── roles/
```

## Repository layout

- `playbooks/bootstrap.yml` prepares the VM
- `playbooks/k3s.yml` installs and configures `k3s`
- `playbooks/site.yml` runs the full flow
- `roles/common` manages package maintenance, timezone, firewall and guest agent
- `roles/bootstrap` manages hostname, admin user and sudoers
- `roles/docker` installs Docker
- `roles/k3s` installs and configures the cluster service

## Quick Start

Install collections:

```bash
ansible-galaxy collection install -r requirements.yml
```

Edit inventory:

```ini
[k3s]
vm-rui ansible_host=192.168.1.50
```

Edit shared vars in `group_vars/all.yml`:

```yaml
ansible_user: ubuntu
vm_hostname: vm-rui
vm_admin_user: cosmin
vm_admin_ssh_keys:
  - ssh-ed25519 AAAA...
k3s_disable:
  - traefik
```

Run only the VM bootstrap:

```bash
ansible-playbook playbooks/bootstrap.yml
```

Install `k3s` on the prepared VM:

```bash
ansible-playbook playbooks/k3s.yml
```

Or run the full flow:

```bash
ansible-playbook playbooks/site.yml
```

## Typical Flow

Use this order on a fresh VM:

1. connect with the default user, for example `ubuntu`
2. run `bootstrap.yml`
3. reconnect with the new admin user if you want
4. run `k3s.yml`

## Example Outcome

After both playbooks finish, the VM should have:

- updated packages
- a dedicated admin user with passwordless sudo
- Docker installed and running
- `k3s` installed and started
- a generated kubeconfig under `/etc/rancher/k3s/k3s.yaml`

## Notes

- This repository is intentionally scoped for a single-node `k3s` setup suitable for portfolio demos and small labs.
- If you want HA or agent nodes later, this structure is a good base to extend with separate host groups and roles.
- The VM bootstrap tasks stay close to `vm-bootstrap-ansible` so both repositories look consistent in your portfolio.

## Push to GitHub

```bash
git init
git remote add origin git@github.com:pascariucosmin93/k3s-ansible.git
git add .
git commit -m "Add Ansible bootstrap and k3s setup"
git branch -M main
git push -u origin main
```
