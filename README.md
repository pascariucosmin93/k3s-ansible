# k3s-ansible

Ansible repo for preparing a fresh Ubuntu VM and installing `k3s`.

It covers the two steps you described:

1. VM bootstrap
2. `k3s` install

## What it does

The bootstrap playbook prepares a clean VM by:

- running `apt update` and `apt upgrade`
- installing common CLI packages
- creating an admin user
- adding that user to `sudo`
- configuring passwordless sudo
- installing Docker

The `k3s` playbook:

- creates `/etc/rancher/k3s/config.yaml`
- installs `k3s`
- enables and starts the service
- supports extra API server args through `k3s_kube_apiserver_args`

## Structure

```text
.
├── ansible.cfg
├── group_vars/
├── inventory/
├── playbooks/
├── requirements.yml
└── roles/
```

## Quick start

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
vm_admin_user: cosmin
vm_admin_ssh_keys:
  - ssh-ed25519 AAAA...
```

Run bootstrap:

```bash
ansible-playbook playbooks/bootstrap.yml
```

Install k3s:

```bash
ansible-playbook playbooks/k3s.yml
```

## Typical flow

Use this order on a fresh VM:

1. connect with the default user, for example `ubuntu`
2. run `bootstrap.yml`
3. reconnect with the new admin user if you want
4. run `k3s.yml`

## Example result

After both playbooks finish, the VM should have:

- updated packages
- a dedicated admin user with passwordless sudo
- Docker installed and running
- `k3s` installed and started

## Push to GitHub

If you created the repo locally:

```bash
git init
git remote add origin git@github.com:pascariucosmin93/k3s-ansible.git
git add .
git commit -m "Add Ansible bootstrap and k3s setup"
git branch -M main
git push -u origin main
```
