# 🌟 **TASK03: Configure Default SSH User in Ansible**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* is streamlining **Ansible operations** by adopting a **common sudo user**, *james*, for SSH connections across all servers in *Stratos DC*. Ansible is pre-installed on the **jump host** via `yum`. The task requires updating the default Ansible configuration to set *james* as the default SSH user for all hosts, ensuring changes are made only in the existing configuration file.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Configuration File**: Modify the default Ansible configuration at `/etc/ansible/ansible.cfg` on the jump host.  
2. **Default SSH User**: Set `remote_user = james` to apply globally for all hosts.  
3. **No New Files**: Use only the existing `/etc/ansible/ansible.cfg`; do not create new configuration files.  
4. **Privilege Escalation**: Use sudo on the jump host (password for user `thor`: `mjolnir123`).  
5. **Verification**: Ensure the configuration change is applied correctly.  

**Note**: The change must be made in the global Ansible configuration to affect all future playbook runs without specifying the user in inventories or playbooks.

👉 **Your Mission**: Elevate privileges, update the Ansible configuration to set *james* as the default SSH user, and verify the change.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: Ansible installed via `yum`, user `thor` (sudo password: `mjolnir123`)  
  - *Configuration File*: `/etc/ansible/ansible.cfg` (default Ansible config)  
  - *Default SSH User*: `james` (to be set globally)  
- **Working Directory**: `/etc/ansible`

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Ansible Config*: Global configuration file controlling default settings like remote user.  
- *remote_user Directive*: Specifies the default SSH user for Ansible connections.  
- *Jump Host*: Central management node requiring sudo for system-wide config edits.  

### **Implementation Strategy**  
1. Log in to the jump host and elevate to root using `sudo su -`.  
2. Navigate to `/etc/ansible` and edit `ansible.cfg`.  
3. Add or modify `remote_user = james` under the `[defaults]` section or at the end of the file.  
4. Save the changes and verify the configuration.  

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Permission Denied**:  
   - *Issue*: Cannot edit `/etc/ansible/ansible.cfg` without root privileges.  
   - *Fix*: Use `sudo su -` with password `mjolnir123` or `sudo vi`.  
2. **Incorrect Config Placement**:  
   - *Issue*: Adding `remote_user` outside `[defaults]` may not apply globally.  
   - *Fix*: Ensure the directive is under `[defaults]` or standalone at the file’s end.  
3. **Syntax Error in Config**:  
   - *Issue*: Typos or incorrect formatting (e.g., missing spaces).  
   - *Fix*: Use exact format: `remote_user = james`.  
4. **Missing Config File**:  
   - *Issue*: `/etc/ansible/ansible.cfg` not found.  
   - *Fix*: Verify Ansible installation with `ansible --version`.  

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host to manage Ansible configuration.

### **Step 2: Elevate Privileges**  
```bash
sudo su -
```
*Password*: `mjolnir123`  
*Purpose*: Gain root access to edit system-wide configuration files.

### **Step 3: Navigate to Ansible Config Directory**  
```bash
cd /etc/ansible
```
*Purpose*: Move to the directory containing `ansible.cfg`.

### **Step 4: Edit ansible.cfg**  
```bash
vi ansible.cfg
```
Add or modify the following line under `[defaults]` or at the end:  
```ini
remote_user = james
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Set *james* as the default SSH user for all Ansible operations.

### **Step 5: Verify Configuration Change**  
```bash
cat /etc/ansible/ansible.cfg | grep remote_user
```
**Expected Output**:
```ini
remote_user = james
```
*Purpose*: Confirm the `remote_user` setting is correctly added.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Ansible Configuration File** (`/etc/ansible/ansible.cfg`)  
Excerpt after modification:  
```ini
[defaults]
# ... existing settings ...
remote_user = james
```
- *Section*: `[defaults]` (global Ansible settings).  
- *Directive*: `remote_user`  
- *Value*: `james` (default SSH user).  
*Purpose*: Ensures all Ansible connections default to user *james*.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Config File  | Path          | `/etc/ansible/ansible.cfg` | Default Ansible config |
| Config File  | Section       | `[defaults]` | Global settings section |
| Config       | Directive     | `remote_user` | Specifies default SSH user |
| Config       | Value         | `james`   | Default SSH login user |
| Jump Host    | User          | `thor`    | Initial login user |
| Jump Host    | Sudo Password | `mjolnir123` | For root access |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Config Content**  
```bash
cat /etc/ansible/ansible.cfg | grep remote_user
```
**Expected Output**:
```ini
remote_user = james
```

### **Step 2: Check File Permissions**  
```bash
ls -l /etc/ansible/ansible.cfg
```
**Expected Output** (example):
```bash
-rw-r--r-- 1 root root ... ansible.cfg
```
*Purpose*: Ensure the file is accessible and owned by root.

### **Step 3: Verify Ansible Config**  
```bash
ansible-config dump | grep REMOTE_USER
```
**Expected Output**:
```bash
REMOTE_USER(default) = james
```
*Purpose*: Confirm Ansible recognizes the default user setting.

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test Config Syntax**  
*INI files are simple; manual inspection avoids syntax errors.*  

### **Test Ansible Config Application**  
If an inventory with hosts accessible by *james* exists:  
```bash
ansible all -m ping -i /path/to/inventory
```
*Expected*: Successful ping without specifying `--user`.  
*Purpose*: Verify `remote_user` is applied.

### **Backup Config** (Recommended)  
```bash
cp /etc/ansible/ansible.cfg /etc/ansible/ansible.cfg.bak
```
*Purpose*: Preserve original config before edits.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Elevate privileges
sudo su -  # Password: mjolnir123

# Navigate to config directory
cd /etc/ansible

# Edit config
vi ansible.cfg
# Add: remote_user = james

# Verify change
cat ansible.cfg | grep remote_user

# Check effective config
ansible-config dump | grep REMOTE_USER
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Permission Denied**  
*Symptoms*: Cannot save `ansible.cfg`.  
*Solution*: Use sudo or root shell.  
```bash
sudo vi /etc/ansible/ansible.cfg
```

### **Issue 2: Setting Not Applied**  
*Symptoms*: Ansible uses default OS user instead of *james*.  
*Solution*: Check `[defaults]` section and restart shell.  
```bash
cat /etc/ansible/ansible.cfg
ansible-config dump | grep REMOTE_USER
```

### **Issue 3: Config File Missing**  
*Symptoms*: `/etc/ansible/ansible.cfg` not found.  
*Solution*: Verify Ansible installation.  
```bash
ansible --version
```

### **Issue 4: Incorrect Sudo Password**  
*Symptoms*: `sudo su -` fails.  
*Solution*: Use correct password `mjolnir123`.

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Ensure *james* has secure SSH access (e.g., SSH keys); avoid passwords in production.  
- *Best Practice*: Add `remote_user` under `[defaults]` for clarity, not at file end.  
- *Config Inspection*: Use `ansible-config view` for full config.  
  ```bash
  ansible-config view
  ```  
- *Scope*: Global change affects all users on the jump host; use project-specific configs for isolation if needed.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *System-Wide Edit*: Requires root access to modify `/etc/ansible/ansible.cfg`.  
2. *Correct Placement*: Ensuring `remote_user` is effective globally.  
3. *No New Files*: Adhering to using only the default config.  
4. *Verification*: Confirming the setting without running playbooks.

### **Solution Approach**  
1. Elevated privileges with `sudo su -` and password `mjolnir123`.  
2. Edited `ansible.cfg` to add `remote_user = james`.  
3. Verified with `cat` and `ansible-config dump`.  
4. Ensured no new config files were created.

### **Key Success Factors**  
- *Privileges*: Correct use of sudo.  
- *Config Accuracy*: Precise directive syntax.  
- *Verification*: Used Ansible tools for confirmation.  
- *Compliance*: Modified only default config.

### **Critical Details**  
- *File*: `/etc/ansible/ansible.cfg`  
- *Directive*: `remote_user = james`  
- *Sudo Password*: `mjolnir123`  
- *Scope*: Global for all Ansible runs

### **Ansible Benefits**  
- *Standardization*: Uniform SSH user across tasks.  
- *Efficiency*: Eliminates per-playbook user specs.  
- *Simplicity*: Single-line config change.  
- *Scalability*: Applies to all future operations.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use SSH keys or Ansible Vault for *james* credentials.  
- 📊 *Auditing*: Track config changes in a changelog.  
- 🛡️ *Backups*: Always back up `ansible.cfg` before edits.  
- 🌐 *Multi-User*: Global config impacts all users; consider roles for shared hosts.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Ansible Configuration*: Modifying `/etc/ansible/ansible.cfg`.  
- *remote_user Directive*: Setting default SSH users globally.  
- *Sudo Usage*: Elevating privileges on Linux.  
- *Verification Tools*: Using `ansible-config` for validation.

**Ansible Features Used**:  
- Global `ansible.cfg` configuration.  
- `remote_user` directive in `[defaults]`.  
- `ansible-config` for verification.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Privileges*: Used `sudo su -` with `mjolnir123`.  
- *Configuration*: Added `remote_user = james` to `/etc/ansible/ansible.cfg`.  
- *Verification*: Confirmed via `cat` and `ansible-config dump`.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Ansible now defaults to *james* as the SSH user for all hosts, enabling standardized operations for the Nautilus DevOps team.

### **Next Steps**  
- *Testing*: Run a playbook to confirm *james* is used.  
- *Security*: Configure SSH keys for *james*.  
- *Enhancement*: Add other defaults (e.g., `become: yes`).  
- *Documentation*: Update team wiki with config details.  
- *Automation*: Script config validation in CI/CD.