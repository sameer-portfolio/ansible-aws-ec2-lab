# Ansible Automation on AWS EC2

I used one EC2 server as a "manager" (control node) to configure two "worker" servers (web1 and web2) with a single command, using Ansible.

## Architecture
- `ansible-control`: runs Ansible
- `web1` and `web2`: managed nodes, reached over SSH using their private IPs
- All 3 are Ubuntu servers on AWS EC2 (ap-south-1)

## Step 1: Passwordless SSH
Created a key pair on the control node with `ssh-keygen`. Since EC2 has no passwords, I added the public key to `~/.ssh/authorized_keys` on each worker. The private key never leaves the control node.

## Step 2: Inventory
`inventory.ini` lists the workers by private IP in a group called `linux_servers`.

Test connectivity: `ansible linux_servers -i inventory.ini -m ping`

![Ping test]

## Step 3: Playbook
`webserver.yml` installs Nginx, makes sure OpenSSH is present, and creates a custom home page on both servers.

Run: `ansible-playbook -i inventory.ini webserver.yml`

![Playbook run]

## Step 4: Verify
Each server answers with its own message.

![Curl test]

## Step 5: Run again
Running the playbook a second time changes nothing (`changed=0`). Ansible only changes what is missing.

![Second run]

## Troubleshooting
- `UNREACHABLE`: check the security group allows SSH from itself
- `Permission denied (publickey)`: the public key was not added correctly on the worker
- Key issues: `ssh-add ~/.ssh/id_rsa`

## What I learned
- Ansible automates the same task on many servers at once
- Playbooks are repeatable and safe to run twice
- On AWS, SSH keys are added by hand because there are no passwords

## Tools
Ansible, SSH, Linux, Nginx, AWS EC2
