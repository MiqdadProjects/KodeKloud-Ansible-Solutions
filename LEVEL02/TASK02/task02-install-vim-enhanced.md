# 🌟 **TASK02: Install vim-enhanced on All App Servers**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus Application Development team* requires the `vim-enhanced` package to be installed on all app servers in *Stratos DC* to support application testing. The task involves creating an inventory and playbook to automate the installation using Ansible’s `yum` module, ensuring the playbook runs as the `thor` user on the jump host.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Create `/home/thor/playbook/inventory` with all app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/playbook/playbook.yml` to install `vim-enhanced` on all app servers using the `yum` module.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **User**: Playbook must be executable by `thor` without extra arguments.

**Note**: Validation will use the specified command, so no additional arguments should be required.

👉 **Your Mission**: Create the inventory, write the playbook to install `vim-enhanced`, execute it, and verify the package installation on all app servers.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, working directory `/home/thor/playbook`  
  - *App Servers*:  
    - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
    - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
    - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
  - *Inventory File*: `/home/thor/playbook/inventory`  
  - *Playbook File*: `/home/thor/playbook/playbook.yml`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines all app servers with SSH credentials for Ansible management.  
- *Playbook*: Uses the `yum` module to install the `vim-enhanced` package.  
- *App Servers*: Receive the package installation, requiring root privileges.  

### **Implementation Strategy**  
1. Create `/home/thor/playbook/inventory` with details for all app servers.  
2. Write `/home/thor/playbook/playbook.yml` to install `vim-enhanced` using `yum`.  
3. Execute the playbook using the specified command.  
4. Verify the package installation on each app server.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Inventory Misconfiguration**:  
   - *Issue*: Incorrect hostnames, IPs, users, or passwords in the inventory.  
   - *Fix*: Verify details against task requirements.  
2. **Package Installation Failure**:  
   - *Issue*: `yum` fails due to lack of root privileges or unavailable package.  
   - *Fix*: Ensure `become: true` in the playbook and check repository access.  
3. **Playbook Syntax Error**:  
   - *Issue*: Invalid YAML syntax causes execution failure.  
   - *Fix*: Run `ansible-playbook --syntax-check` to validate.  
4. **Connectivity Issues**:  
   - *Issue*: Ansible cannot connect to app servers.  
   - *Fix*: Test SSH connectivity manually (e.g., `ssh tony@172.16.238.10`).  

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as `thor` to execute Ansible commands.

### **Step 2: Navigate to Working Directory**  
```bash
cd /home/thor/playbook
```
*Purpose*: Move to the directory for inventory and playbook files.

### **Step 3: Create Inventory File**  
```bash
cat > /home/thor/playbook/inventory <<EOF
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
EOF
```
*Purpose*: Define all app servers with their SSH credentials.

### **Step 4: Create Playbook**  
```bash
vi /home/thor/playbook/playbook.yml
```
**Content**:
```yaml
---
- name: Install vim-enhanced on all app servers
  hosts: all
  become: true
  tasks:
    - name: Install vim-enhanced package
      yum:
        name: vim-enhanced
        state: present
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to install `vim-enhanced` on all app servers.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Install vim-enhanced on all app servers] *********************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Install vim-enhanced package] ********************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to install the package.

### **Step 6: Verify Installation**  
```bash
ansible all -i /home/thor/playbook/inventory -m command -a "rpm -q vim-enhanced"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
vim-enhanced-<version>
stapp02 | CHANGED | rc=0 >>
vim-enhanced-<version>
stapp03 | CHANGED | rc=0 >>
vim-enhanced-<version>
```
*Purpose*: Confirm `vim-enhanced` is installed on all app servers.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/playbook/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hosts*: `stapp01`, `stapp02`, `stapp03`  
- *Details*: IP addresses, usernames, and passwords for SSH access.  
*Purpose*: Defines app servers for Ansible management.

### **Playbook** (`/home/thor/playbook/playbook.yml`)  
```yaml
---
- name: Install vim-enhanced on all app servers
  hosts: all
  become: true
  tasks:
    - name: Install vim-enhanced package
      yum:
        name: vim-enhanced
        state: present
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` targets all servers in the inventory.  
- *Privilege Escalation*: `become: true` for root access.  
- *Task*: Uses `yum` module with `state: present` for idempotent installation.  
*Purpose*: Installs `vim-enhanced` on all app servers.

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
| Playbook     | Task Module   | `yum`     | Installs packages |
| Playbook     | Package       | `vim-enhanced` | Package to install |
| Playbook     | State         | `present` | Ensures package is installed |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/playbook/inventory
```
**Expected Output**:
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i /home/thor/playbook/inventory playbook.yml
```
**Expected Output**: As shown in Step 5 above.

### **Step 3: Verify Package Installation**  
```bash
ansible all -i /home/thor/playbook/inventory -m command -a "rpm -q vim-enhanced"
```
**Expected Output**: Package version returned for each server.

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh tony@172.16.238.10  # Password: Ir0nM@n
ssh steve@172.16.238.11  # Password: Am3ric@
ssh banner@172.16.238.12  # Password: BigGr33n
```
*Expected*: Successful login for each server.  
*Purpose*: Ensure SSH access to all app servers.

### **Test Playbook Syntax**  
```bash
ansible-playbook -i /home/thor/playbook/inventory playbook.yml --syntax-check
```
**Expected Output**:
```bash
playbook: playbook.yml
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i /home/thor/playbook/inventory playbook.yml --check
```
*Purpose*: Simulate package installation.

### **Test Package Availability**  
```bash
ansible all -i /home/thor/playbook/inventory -m command -a "yum list vim-enhanced"
```
*Purpose*: Confirm `vim-enhanced` is available in repositories.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Create inventory
cat > /home/thor/playbook/inventory <<EOF
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
EOF

# Create playbook
vi /home/thor/playbook/playbook.yml

# Run playbook
cd /home/thor/playbook
ansible-playbook -i inventory playbook.yml

# Verify installation
ansible all -i /home/thor/playbook/inventory -m command -a "rpm -q vim-enhanced"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1` in output.  
*Solution*: Verify inventory details and SSH connectivity.  
```bash
cat /home/thor/playbook/inventory
ssh tony@172.16.238.10
ssh steve@172.16.238.11
ssh banner@172.16.238.12
```

### **Issue 2: Package Installation Fails**  
*Symptoms*: `yum` task fails.  
*Solution*: Check `become: true` and repository configuration.  
```bash
ansible-playbook -i /home/thor/playbook/inventory playbook.yml -v
ansible all -i /home/thor/playbook/inventory -m command -a "yum repolist"
```

### **Issue 3: Syntax Error in Playbook**  
*Symptoms*: Playbook fails with YAML errors.  
*Solution*: Validate syntax.  
```bash
ansible-playbook -i /home/thor/playbook/inventory playbook.yml --syntax-check
```

### **Issue 4: Package Not Found**  
*Symptoms*: `vim-enhanced` not available.  
*Solution*: Ensure correct package name and repository access.  
```bash
ansible all -i /home/thor/playbook/inventory -m command -a "yum search vim-enhanced"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Replace plain-text passwords with SSH keys or Ansible Vault in production.  
- *Idempotency*: `state: present` ensures the playbook is safe to re-run.  
- *Verbose Output*: Use `-v` for detailed debugging:  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Optimization*: Consider using package groups for multiple installations.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Multiple Servers*: Installing on three servers with different credentials.  
2. *Root Privileges*: Package installation requires `sudo`.  
3. *Correct Package Name*: Ensuring `vim-enhanced` is specified correctly.

### **Solution Approach**  
1. Created inventory with accurate server details.  
2. Used `yum` module with `become: true` for installation.  
3. Verified package presence using `rpm -q`.

### **Key Success Factors**  
- *Inventory*: Correctly defined all app servers.  
- *Playbook*: Used `yum` module with `state: present`.  
- *Verification*: Confirmed installation on all servers.

### **Critical Details**  
- *Inventory*: `/home/thor/playbook/inventory`  
- *Playbook*: `/home/thor/playbook/playbook.yml`  
- *Package*: `vim-enhanced`  
- *Command*: `ansible-playbook -i inventory playbook.yml`

### **Ansible Benefits**  
- *Automation*: Simplifies package installation across multiple servers.  
- *Idempotency*: Safe for repeated runs.  
- *Scalability*: Easily extends to additional servers or packages.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use SSH keys or Ansible Vault instead of `ansible_ssh_pass`.  
- 📊 *Logging*: Enable Ansible logging for auditing:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ```  
- 🛡️ *Package Management*: Verify package versions for compatibility.  
- 🌐 *Scalability*: Use inventory groups for larger environments.  

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Inventory Management*: Configuring multiple hosts with credentials.  
- *Yum Module*: Installing packages using Ansible.  
- *Privilege Escalation*: Using `become: true` for root access.  
- *Verification*: Checking package installation with `rpm`.

**Ansible Features Used**:  
- `yum` module for package management.  
- `become` for privilege escalation.  
- Inventory with multiple hosts.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Created `/home/thor/playbook/inventory` with all app servers.  
- *Playbook*: Created `/home/thor/playbook/playbook.yml` to install `vim-enhanced`.  
- *Execution*: Ran successfully with specified command.  
- *Verification*: Confirmed package installation on all servers.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: `vim-enhanced` is installed on all app servers, ready for validation.