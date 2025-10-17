# 🌟 **TASK03: Install and Configure httpd on App Servers**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to install and configure the `httpd` web server on all app servers in *Stratos DC* to support application dependencies. The task requires a playbook to automate the installation and service management.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use existing `/home/thor/ansible/inventory` with app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - Install `httpd` on all app servers.  
   - Ensure `httpd` service is started and enabled on boot.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Note**: The playbook must be executable by `thor` without extra arguments.

👉 **Your Mission**: Write the playbook to install and configure `httpd`, and verify the service status.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`  
  - *App Servers*: As listed above  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Service*: `httpd`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Pre-existing with app server details.  
- *Playbook*: Uses `package` and `service` modules for `httpd` installation and management.  
- *App Servers*: Run `httpd` service.  

### **Implementation Strategy**  
1. Verify the existing inventory file.  
2. Create `/home/thor/ansible/playbook.yml` to install and configure `httpd`.  
3. Run the playbook using the specified command.  
4. Verify `httpd` service status on each app server.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Package Installation Failure**:  
   - *Issue*: `httpd` package not found.  
   - *Fix*: Check repository access with `yum list httpd`.  
2. **Service Failure**:  
   - *Issue*: `httpd` service fails to start.  
   - *Fix*: Verify `service` module parameters and system logs.  
3. **Connectivity Issues**:  
   - *Issue*: Ansible cannot connect to app servers.  
   - *Fix*: Test SSH connectivity manually.  

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```

### **Step 2: Navigate to Working Directory**  
```bash
cd /home/thor/ansible
```

### **Step 3: Verify Inventory File**  
```bash
cat /home/thor/ansible/inventory
```

### **Step 4: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Install and configure httpd on all app servers
  hosts: all
  become: true
  tasks:
    - name: Install httpd package
      package:
        name: httpd
        state: present
    - name: Ensure httpd service is started and enabled
      service:
        name: httpd
        state: started
        enabled: true
```

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Install and configure httpd on all app servers] ***************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Install httpd package] ***************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Ensure httpd service is started and enabled] *****************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0
stapp02                    : ok=3    changed=2    unreachable=0    failed=0
stapp03                    : ok=3    changed=2    unreachable=0    failed=0
```

### **Step 6: Verify Service**  
```bash
ansible all -i inventory -m command -a "systemctl status httpd"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
   Active: active (running) since Fri 2025-10-17 11:31:XX UTC; ...
```

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/ansible/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

### **Playbook** (`/home/thor/ansible/playbook.yml`)  
```yaml
---
- name: Install and configure httpd on all app servers
  hosts: all
  become: true
  tasks:
    - name: Install httpd package
      package:
        name: httpd
        state: present
    - name: Ensure httpd service is started and enabled
      service:
        name: httpd
        state: started
        enabled: true
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` targets all servers.  
- *Privilege Escalation*: `become: true` for root access.  
- *Tasks*:  
  - *Install*: Installs `httpd` package.  
  - *Service*: Starts and enables `httpd` service.  
*Purpose*: Installs and configures `httpd` service.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Inventory    | Hostname      | `stapp01` | App Server 1 alias |
| Inventory    | ansible_host  | `172.16.238.10` | App Server 1 IP |
| Inventory    | ansible_user  | `tony`    | App Server 1 user |
| Inventory    | ansible_ssh_pass | `Ir0nM@n` | App Server 1 password |
| Inventory    | Hostname      | `stapp02` | App Server 2 alias |
| Inventory    | ansible_host  | `172.16.238.11` | App Server 2 IP |
| Inventory    | ansible_user  | `steve`   | App Server 2 user |
| Inventory    | ansible_ssh_pass | `Am3ric@` | App Server 2 password |
| Inventory    | Hostname      | `stapp03` | App Server 3 alias |
| Inventory    | ansible_host  | `172.16.238.12` | App Server 3 IP |
| Inventory    | ansible_user  | `banner`  | App Server 3 user |
| Inventory    | ansible_ssh_pass | `BigGr33n` | App Server 3 password |
| Playbook     | Hosts         | `all`     | Targets all servers |
| Playbook     | Task Modules  | `package`, `service` | Manages package and service |
| Playbook     | Package       | `httpd`   | Web server package |
| Playbook     | Service       | `httpd`   | Web server service |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/ansible/inventory
```

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml
```

### **Step 3: Verify Service**  
```bash
ansible all -i inventory -m command -a "systemctl status httpd"
```

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh tony@172.16.238.10
ssh steve@172.16.238.11
ssh banner@172.16.238.12
```

### **Test Playbook Syntax**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml --syntax-check
```

### **Test Dry Run**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml --check
```

### **Test Package Availability**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "yum list httpd"
```

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd /home/thor/ansible

# Create playbook
vi playbook.yml

# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify service
ansible all -i inventory -m command -a "systemctl status httpd"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Package Installation Fails**  
*Symptoms*: `httpd` package not found.  
*Solution*: Check repository configuration.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "yum repolist"
```

### **Issue 2: Service Fails to Start**  
*Symptoms*: `httpd` service not running.  
*Solution*: Check logs and `service` task.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "journalctl -u httpd"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys or Ansible Vault.  
- *Idempotency*: `package` and `service` modules ensure safe re-runs.  
- *Verbose Output*: Use `-v` for debugging.  

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Service Management*: Ensuring `httpd` is running and enabled.  
2. *Package Availability*: Ensuring `httpd` is available in repositories.  

### **Solution Approach**  
1. Used `package` and `service` modules.  
2. Verified repository access during testing.

### **Key Success Factors**  
- *Inventory*: Verified pre-existing file.  
- *Playbook*: Correct module usage.  
- *Verification*: Confirmed service status.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Configure `httpd` securely (e.g., SSL, restricted access).  
- 📊 *Logging*: Enable `httpd` and Ansible logging.  
- 🛡️ *Firewall*: Open HTTP port (80) if needed.  

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Package Module*: Installing software with Ansible.  
- *Service Module*: Managing services.  
- *Verification*: Checking service status.

**Ansible Features Used**:  
- `package` module.  
- `service` module.  
- `become` for privilege escalation.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to install `httpd`.  
- *Execution*: Ran successfully.  
- *Verification*: Confirmed `httpd` service running.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: `httpd` is installed and running on all app servers.