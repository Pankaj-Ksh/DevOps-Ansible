# Ansible Notes: Setup and Basics

* An **IT Automation Tool**.
* A **Configuration Management Tool** used to automate:
    * Software installation
    * Configuration changes
    * Application deployment
    * System management

---
### Steps must do on Ansible Client and can be used on Ansible Master
By default root login is blocked. We enable it temporarily so Master can log in.

**Change Password**
```
passwd root
```
- set password : pankaj123

**Give Access**
```
vi /etc/ssh/sshd_config
```
### Add or make 
- PasswordAuthentication yes
- PermitRootLogin yes

**Restart the sshd service** 
```
systemctl restart sshd
```

```
systemctl status sshd
```
  

### Steps to do On Ansible master : 

**Download Ansible** 
```
dnf install ansible -y
```

**Download python**
```
dnf install python3 python3-pip python3-devel -y
```

**Check Version**
```
ansible --version
```

**Inventory File (`/etc/ansible/hosts`)**
```
vi /etc/ansible/hosts
```
- add server using private ip

**Check inventory:**
```
ansible-inventory --list
```

**Generate Key Pair**
```
ssh-keygen               
```

**Check keys** 
```
ll ~/.ssh                
```

**Copy Public Key to Targets:**
```
ssh-copy-id root@<private-ip>
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

  
