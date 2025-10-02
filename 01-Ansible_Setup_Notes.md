# 📘 Ansible Notes: Setup and Basics

## 🔹 What is Ansible?
* An **IT Automation Tool**.
* A **Configuration Management Tool** used to automate:
    * Software installation
    * Configuration changes
    * Application deployment
    * System management

---

## 🔹 Installation (Amazon Linux 2, t2.micro)

```
dnf install ansible -y
```

```
dnf install python3 python3-pip python3-devel -y
```

```
ansible --version
```

```
passwd root
```
### By default root login is blocked. We enable it temporarily so Master can log in.
Open file and uncomment line 38 and 61
```
vi /etc/ssh/sshd_config
```

```
systemctl restart sshd
```

```
systemctl status sshd
```

---


## 🔹 SSH Key Authentication Setup

### On Ansible Master
1.  **Generate Key Pair:**
    ```
    ssh-keygen               # Generates key pair (id_rsa + id_rsa.pub)
    ```
    ```
    ll ~/.ssh                # Confirm files are created
    ```
2.  **Copy Public Key to Targets:**
    ```bash
    # Run this for target servers (Amsible Client)
    ssh-copy-id root@<prod-1-private-ip>
    ```

**Key Authentication Flow:**
* The **Public Key** (`id_rsa.pub`) is copied to `/root/.ssh/authorized_keys` on the remote server.
* The **Private Key** (`id_rsa`) remains securely on the Ansible Master.
* Future logins use key authentication, eliminating the need for a password.

---

## 🔹 Inventory File (`/etc/ansible/hosts`)

The Inventory file defines the servers Ansible manages.

### Example Configuration:
```ini
[Client]
private-ip
```

**Check inventory:**
```
ansible-inventory --list
```

**Test connection:**
```
ansible -m ping all
```

## Reason:
- Copies public key (id_rsa.pub) to /root/.ssh/authorized_keys on remote servers.
- Private key stays safe on Ansible Master.
 -Next login uses key auth → no password needed.

## 🔹 Adhoc Commands
Used for quick, one-time tasks or repeated daily maintenance (not saved in playbooks). 
- -a = argument

- Example :
- To run on all connected client 
  ```
  ansible all -a "dnf install -y git"
  ```
- or
- To Run On Specific Group 
  ```
  ansible client -a "dnf install -y git"
  ```

# 📝 Important Q&A Recap

### Q: Why set root password & edit sshd_config?
- To temporarily allow root login with password.  
- Needed only for first-time `ssh-copy-id`.

### Q: Which sshd_config lines?
- `PermitRootLogin yes` → allow root login.  
- `PasswordAuthentication yes` → allow password login.  

### Q: What is sshd service?
- **SSH Daemon** → the background service that listens on port **22** for SSH connections.  
- Handles authentication (password or key).  

### Q: Which key is copied with ssh-copy-id?
- **Public key (`id_rsa.pub`)** → copied to remote server.  
- **Private key (`id_rsa`)** → stays on Ansible Master.  

### Q: How does SSH key login work?
1. Server checks your public key in `authorized_keys`.  
2. Sends a challenge.  
3. Master proves identity with matching private key.  
4. Login succeeds without password.  

  
