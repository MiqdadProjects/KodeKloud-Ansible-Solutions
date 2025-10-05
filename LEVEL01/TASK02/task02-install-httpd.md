# 🌟 **TASK02: Install Apache HTTPD on App Server 3**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* is testing **Ansible playbooks** to automate tasks across their infrastructure. A playbook located at `/home/thor/playbook/playbook.yml` is ready to install and start the *Apache HTTPD* service on **App Server 3** in *Stratos DC*. To enable this, an Ansible inventory file must be created to target the server correctly.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Create an *INI-type* Ansible inventory file at `/home/thor/playbook/inventory` on the jump host.  
2. **App Server 3**: Include *App Server 3* in the inventory with necessary variables:  
   - *Hostname*: `stapp03` (per wiki naming convention)  
   - *IP Address*: `172.16.238.12`  
   - *Ansible User*: `banner`  
   - *SSH Password*: `BigGr33n`  
3. **Execution**: Ensure the playbook `/home/thor/playbook/playbook.yml` runs successfully using:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Verification**: Confirm the *httpd* package is installed and the service is running on *App Server 3*.  

**Note**: The `ansible` command is pre-configured on the jump host. No additional arguments are needed beyond the specified command.

👉 **Your Mission**: Create the inventory file, run the provided playbook, and verify that *httpd* is installed and running on *App Server 3*.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: Configured with Ansible  
  - *Target Server*: App Server 3 (`stapp03`, IP: `172.16.238.12`)  
  - *Inventory File*: `/home/thor/playbook/inventory`  
  - *Playbook File*: `/home/thor/playbook/playbook.yml`  
  - *Service*: `httpd` (Apache HTTP Server)  
- **Working Directory**: `/home/thor/playbook`

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines *App Server 3* with SSH credentials for Ansible connectivity.  
- *Playbook*: Installs and starts the *httpd* service using `yum` and `service` modules.  
- *Target Server*: *App Server 3*, accessed via SSH with user `banner`.  

### **Implementation Strategy**  
1. Create `/home/thor/playbook/inventory` with *App Server 3* details in INI format.  
2. Use the provided playbook `/home/thor/playbook/playbook.yml` to install and start *httpd*.  
3. Run the playbook with the specified command.  
4. Verify *httpd* installation and service status via SSH.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Inventory Misconfiguration**:  
   - *Issue*: Incorrect hostname, IP, user, or password.  
   - *Fix*: Verify `stapp03`, `172.16.238.12`, `banner`, `BigGr33n`.  
2. **Playbook Execution Failure**:  
   - *Issue*: Playbook fails due to connectivity or privilege issues.  
   - *Fix*: Ensure `become: yes` and `become_user: root` are set in the playbook.  
3. **SSH Connectivity Issues**:  
   - *Issue*: Cannot connect to *App Server 3*.  
   - *Fix*: Test SSH with `ssh banner@172.16.238.12`.  
4. **Service Not Starting**:  
   - *Issue*: *httpd* service fails to start.  
   - *Fix*: Check service status and logs with `systemctl status httpd` and `journalctl -u httpd`.  

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host to execute Ansible commands.

### **Step 2: Navigate to Working Directory**  
```bash
cd /home/thor/playbook
```
*Purpose*: Move to the directory containing the inventory and playbook files.

### **Step 3: Create Inventory File**  
```bash
vi inventory
```
**Content**:
```
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Configure *App Server 3* for Ansible connectivity.

### **Step 4: Verify Playbook**  
The provided playbook `/home/thor/playbook/playbook.yml` is:  
```yaml
---
- hosts: all
  become: yes
  become_user: root
  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: installed
    - name: Start service httpd
      service:
        name: httpd
        state: started
```
*Purpose*: Confirm the playbook installs and starts *httpd*. No changes are needed.

### **Step 5: Run the Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```
PLAY [all] ********************************************************************

TASK [Gathering Facts] ********************************************************
ok: [stapp03]

TASK [Install httpd package] **************************************************
changed: [stapp03]

TASK [Start service httpd] ****************************************************
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp03                    : ok=3    changed=2    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to install and start *httpd*.

### **Step 6: Verify httpd Installation and Service**  
Open a new terminal and SSH into *App Server 3*:  
```bash
ssh banner@stapp03
```
*Password*: `BigGr33n`  
Check the *httpd* service:  
```bash
systemctl status httpd
```
**Expected Output**:
```
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
   Active: active (running)
```
*Purpose*: Confirm *httpd* is installed and running.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/playbook/inventory`)  
```
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hostname*: `stapp03`  
- *IP Address*: `172.16.238.12`  
- *Ansible User*: `banner`  
- *SSH Password*: `BigGr33n`  
*Purpose*: Defines *App Server 3* for Ansible.

### **Playbook** (`/home/thor/playbook/playbook.yml`)  
```yaml
---
- hosts: all
  become: yes
  become_user: root
  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: installed
    - name: Start service httpd
      service:
        name: httpd
        state: started
```
- *Hosts*: `all` (targets `stapp03` via inventory).  
- *Privilege Escalation*: `become: yes`, `become_user: root` for root access.  
- *Tasks*:  
  - *Install httpd*: Uses `yum` module with `state: installed`.  
  - *Start httpd*: Uses `service` module with `state: started`.  
*Purpose*: Installs and starts the *httpd* service.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Inventory    | Hostname      | `stapp03` | Alias for App Server 3 |
| Inventory    | ansible_host  | `172.16.238.12` | IP address |
| Inventory    | ansible_user  | `banner`  | SSH user |
| Inventory    | ansible_ssh_pass | `BigGr33n` | SSH password |
| Playbook     | Hosts         | `all`     | Targets inventory hosts |
| Playbook     | Task 1 Module | `yum`     | Installs packages |
| Playbook     | Task 1 Name   | `httpd`   | Apache HTTP Server |
| Playbook     | Task 2 Module | `service` | Manages services |
| Playbook     | Service Name  | `httpd`   | Service to start |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/playbook/inventory
```
**Expected Output**:
```
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```
PLAY [all] ********************************************************************

TASK [Gathering Facts] ********************************************************
ok: [stapp03]

TASK [Install httpd package] **************************************************
changed: [stapp03]

TASK [Start service httpd] ****************************************************
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp03                    : ok=3    changed=2    unreachable=0    failed=0
```

### **Step 3: Verify httpd on App Server 3**  
```bash
ssh banner@stapp03
```
*Password*: `BigGr33n`  
```bash
systemctl status httpd
```
**Expected Output**:
```
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
   Active: active (running)
```

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh banner@172.16.238.12
```
*Expected*: Successful login.  
*Purpose*: Ensure SSH access.

### **Test Playbook Syntax**  
```bash
ansible-playbook -i inventory playbook.yml --syntax-check
```
**Expected Output**:
```
playbook: playbook.yml
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i inventory playbook.yml --check
```
*Purpose*: Simulate execution.

### **Test httpd Installation**  
```bash
ssh banner@stapp03
rpm -q httpd
```
**Expected Output**:
```
httpd-<version>
```
*Purpose*: Confirm *httpd* package installation.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd /home/thor/playbook

# Create inventory
vi inventory
# Content: stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n

# Verify playbook
cat playbook.yml
# Content: (see playbook above)

# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify httpd
ssh banner@stapp03
systemctl status httpd
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1`.  
*Solution*: Check inventory and SSH.  
```bash
cat /home/thor/playbook/inventory
ssh banner@172.16.238.12
```

### **Issue 2: httpd Not Installed**  
*Symptoms*: `rpm -q httpd` returns package not found.  
*Solution*: Verify `yum` task and connectivity.  
```bash
ansible-playbook -i inventory playbook.yml -v
ssh banner@stapp03
rpm -q httpd
```

### **Issue 3: httpd Service Not Running**  
*Symptoms*: `systemctl status httpd` shows inactive.  
*Solution*: Check logs and re-run playbook.  
```bash
ssh banner@stapp03
journalctl -u httpd
ansible-playbook -i inventory playbook.yml
```

### **Issue 4: Syntax Error**  
*Symptoms*: YAML syntax error.  
*Solution*: Validate syntax.  
```bash
ansible-playbook -i inventory playbook.yml --syntax-check
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use Ansible Vault or SSH keys instead of `ansible_ssh_pass`.  
- *Idempotency*: `yum` with `state: installed` and `service` with `state: started` ensure safe re-runs.  
- *Verbose Output*: Use `-v` for detailed logs.  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Dry Run*: Test with `--check`.  
  ```bash
  ansible-playbook -i inventory playbook.yml --check
  ```

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Inventory Setup*: Ensuring correct host details in INI format.  
2. *Privilege Escalation*: Requiring root access for *httpd* installation.  
3. *SSH Access*: Verifying connectivity to *App Server 3*.  
4. *Service Management*: Ensuring *httpd* starts correctly.

### **Solution Approach**  
1. Created inventory with *App Server 3* details.  
2. Used provided playbook with `become: yes` for root access.  
3. Executed with specified command.  
4. Verified *httpd* installation and service status.

### **Key Success Factors**  
- *Inventory*: Accurate SSH credentials.  
- *Playbook*: Correct `yum` and `service` modules.  
- *Execution*: No extra arguments needed.  
- *Verification*: *httpd* installed and running.

### **Critical Details**  
- *Inventory*: `stapp03`, `172.16.238.12`, `banner`, `BigGr33n`  
- *Playbook*: Installs and starts `httpd`  
- *Command*: `ansible-playbook -i inventory playbook.yml`  
- *Service*: `httpd` running on *App Server 3*

### **Ansible Benefits**  
- *Automation*: Simplifies package and service management.  
- *Idempotency*: Safe for repeated runs.  
- *Scalability*: Extensible to multiple servers.  
- *Simplicity*: Clear task structure.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Replace `ansible_ssh_pass` with SSH keys or Ansible Vault.  
- 📊 *Logging*: Enable Ansible logging for auditing.  
- 🛡️ *Service Persistence*: Use `enabled: yes` in `service` module for auto-start.  
- 🌐 *Monitoring*: Add health checks for *httpd*.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Inventory Configuration*: INI format with SSH credentials.  
- *Playbook Creation*: Using `yum` and `service` modules.  
- *Privilege Escalation*: Implementing `become` for root access.  
- *Verification*: Checking package and service status.

**Ansible Features Used**:  
- `yum` module for package installation.  
- `service` module for service management.  
- Inventory with SSH parameters.  
- `become` for privilege escalation.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Created `/home/thor/playbook/inventory`.  
- *Playbook*: Used provided `/home/thor/playbook/playbook.yml`.  
- *Execution*: Ran successfully.  
- *Verification*: Confirmed *httpd* is installed and running.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: *httpd* is installed and running on *App Server 3*, ready for validation.

### **Next Steps**  
- *Testing*: Validate idempotency with multiple runs.  
- *Security*: Use SSH keys or Ansible Vault.  
- *Enhancement*: Add `enabled: yes` to `service` task for auto-start.  
- *Documentation*: Comment playbook for clarity.  
- *Automation*: Integrate into CI/CD pipeline.