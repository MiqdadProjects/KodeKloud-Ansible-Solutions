# 🌟 **TASK05: Replace File Content on App Servers**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to update specific text in files on all app servers in *Stratos DC* to reflect recent changes. The task requires a playbook to automate content replacement using Ansible’s `replace` module.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use existing `/home/thor/ansible/inventory` with app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - On `stapp01`: Replace `xFusionCorp` with `Nautilus` in `/opt/itadmin/blog.txt`.  
   - On `stapp02`: Replace `Nautilus` with `KodeKloud` in `/opt/itadmin/story.txt`.  
   - On `stapp03`: Replace `KodeKloud` with `xFusionCorp Industries` in `/opt/itadmin/media.txt`.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Note**: The playbook must be executable by `thor` without extra arguments.

👉 **Your Mission**: Write a playbook to replace file content on each app server and verify the changes.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`  
  - *App Servers*: As listed above  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Files*: `/opt/itadmin/blog.txt` (stapp01), `/opt/itadmin/story.txt` (stapp02), `/opt/itadmin/media.txt` (stapp03)  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Pre-existing with app server details.  
- *Playbook*: Uses `replace` module with host-specific tasks to update file content.  
- *App Servers*: Have updated file content as specified.  

### **Implementation Strategy**  
1. Verify the existing inventory file.  
2. Create `/home/thor/ansible/playbook.yml` to perform content replacement using `replace` module with host-specific tasks.  
3. Run the playbook using the specified command.  
4. Verify the updated content in each file on the respective app servers.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **File Not Found**:  
   - *Issue*: Target files (`blog.txt`, `story.txt`, `media.txt`) do not exist.  
   - *Fix*: Ensure files exist or add a task to create them if necessary.  
2. **String Not Found**:  
   - *Issue*: `replace` module fails because the target string is not present.  
   - *Fix*: Verify file content before replacement.  
3. **Permission Issues**:  
   - *Issue*: Ansible cannot write to files due to permissions.  
   - *Fix*: Use `become: true` for root access.  
4. **Incorrect Host Targeting**:  
   - *Issue*: Wrong replacements applied to servers.  
   - *Fix*: Use explicit host targeting in the playbook.  

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as `thor`.

### **Step 2: Navigate to Working Directory**  
```bash
cd /home/thor/ansible
```
*Purpose*: Move to the directory for the playbook.

### **Step 3: Verify Inventory File**  
```bash
cat /home/thor/ansible/inventory
```
**Expected Content**:
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
*Purpose*: Confirm inventory details.

### **Step 4: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Replace content in files on app server 1
  hosts: stapp01
  become: true
  tasks:
    - name: Replace xFusionCorp with Nautilus in blog.txt
      replace:
        path: /opt/itadmin/blog.txt
        regexp: 'xFusionCorp'
        replace: 'Nautilus'

- name: Replace content in files on app server 2
  hosts: stapp02
  become: true
  tasks:
    - name: Replace Nautilus with KodeKloud in story.txt
      replace:
        path: /opt/itadmin/story.txt
        regexp: 'Nautilus'
        replace: 'KodeKloud'

- name: Replace content in files on app server 3
  hosts: stapp03
  become: true
  tasks:
    - name: Replace KodeKloud with xFusionCorp Industries in media.txt
      replace:
        path: /opt/itadmin/media.txt
        regexp: 'KodeKloud'
        replace: 'xFusionCorp Industries'
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to replace specific strings in files on each app server.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Replace content in files on app server 1] *********************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]

TASK [Replace xFusionCorp with Nautilus in blog.txt] ***************************
changed: [stapp01]

PLAY [Replace content in files on app server 2] *********************************

TASK [Gathering Facts] ********************************************************
ok: [stapp02]

TASK [Replace Nautilus with KodeKloud in story.txt] ****************************
changed: [stapp02]

PLAY [Replace content in files on app server 3] *********************************

TASK [Gathering Facts] ********************************************************
ok: [stapp03]

TASK [Replace KodeKloud with xFusionCorp Industries in media.txt] **************
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to replace file content.

### **Step 6: Verify File Content**  
```bash
ansible stapp01 -i inventory -m command -a "cat /opt/itadmin/blog.txt"
ansible stapp02 -i inventory -m command -a "cat /opt/itadmin/story.txt"
ansible stapp03 -i inventory -m command -a "cat /opt/itadmin/media.txt"
```
**Expected Output**:
- For `stapp01`:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  Welcome to Nautilus Industries !
  ```
- For `stapp02`:
  ```bash
  stapp02 | CHANGED | rc=0 >>
  Welcome to KodeKloud Group !
  ```
- For `stapp03`:
  ```bash
  stapp03 | CHANGED | rc=0 >>
  Welcome to xFusionCorp Industries !
  ```
*Purpose*: Confirm the correct string replacements in each file.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/ansible/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hosts*: `stapp01`, `stapp02`, `stapp03`  
- *Details*: IP addresses, usernames, and passwords for SSH access.  
*Purpose*: Defines app servers for Ansible management.

### **Playbook** (`/home/thor/ansible/playbook.yml`)  
```yaml
---
- name: Replace content in files on app server 1
  hosts: stapp01
  become: true
  tasks:
    - name: Replace xFusionCorp with Nautilus in blog.txt
      replace:
        path: /opt/itadmin/blog.txt
        regexp: 'xFusionCorp'
        replace: 'Nautilus'
- name: Replace content in files on app server 2
  hosts: stapp02
  become: true
  tasks:
    - name: Replace Nautilus with KodeKloud in story.txt
      replace:
        path: /opt/itadmin/story.txt
        regexp: 'Nautilus'
        replace: 'KodeKloud'
- name: Replace content in files on app server 3
  hosts: stapp03
  become: true
  tasks:
    - name: Replace KodeKloud with xFusionCorp Industries in media.txt
      replace:
        path: /opt/itadmin/media.txt
        regexp: 'KodeKloud'
        replace: 'xFusionCorp Industries'
```
- *Play Name*: Descriptive for each host-specific play.  
- *Hosts*: Explicitly targets `stapp01`, `stapp02`, `stapp03` for respective tasks.  
- *Privilege Escalation*: `become: true` for root access.  
- *Tasks*:  
  - *Replace*: Uses `replace` module to update file content with specific strings.  
*Purpose*: Updates file content on each app server as required.

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
| Playbook     | Hosts (Play 1) | `stapp01` | Targets app server 1 |
| Playbook     | Hosts (Play 2) | `stapp02` | Targets app server 2 |
| Playbook     | Hosts (Play 3) | `stapp03` | Targets app server 3 |
| Playbook     | Task Module   | `replace` | Manages file content replacement |
| Playbook     | File (stapp01) | `/opt/itadmin/blog.txt` | File to update |
| Playbook     | Replacement (stapp01) | `xFusionCorp` → `Nautilus` | String replacement |
| Playbook     | File (stapp02) | `/opt/itadmin/story.txt` | File to update |
| Playbook     | Replacement (stapp02) | `Nautilus` → `KodeKloud` | String replacement |
| Playbook     | File (stapp03) | `/opt/itadmin/media.txt` | File to update |
| Playbook     | Replacement (stapp03) | `KodeKloud` → `xFusionCorp Industries` | String replacement |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/ansible/inventory
```
**Expected Output**: As shown in Code Analysis.

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml
```
**Expected Output**: As shown in Step 5.

### **Step 3: Verify File Content**  
```bash
ansible stapp01 -i /home/thor/ansible/inventory -m command -a "cat /opt/itadmin/blog.txt"
ansible stapp02 -i /home/thor/ansible/inventory -m command -a "cat /opt/itadmin/story.txt"
ansible stapp03 -i /home/thor/ansible/inventory -m command -a "cat /opt/itadmin/media.txt"
```
**Expected Output**: As shown in Step 6.

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh tony@172.16.238.10  # Password: Ir0nM@n
ssh steve@172.16.238.11  # Password: Am3ric@
ssh banner@172.16.238.12  # Password: BigGr33n
```
*Purpose*: Ensure SSH access to all app servers.

### **Test Playbook Syntax**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml --syntax-check
```
**Expected Output**:
```bash
playbook: playbook.yml
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml --check
```
*Purpose*: Simulate playbook execution.

### **Test File Existence**  
```bash
ansible stapp01 -i /home/thor/ansible/inventory -m command -a "ls -l /opt/itadmin/blog.txt"
ansible stapp02 -i /home/thor/ansible/inventory -m command -a "ls -l /opt/itadmin/story.txt"
ansible stapp03 -i /home/thor/ansible/inventory -m command -a "ls -l /opt/itadmin/media.txt"
```
*Purpose*: Confirm files exist.

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

# Verify file content
ansible stapp01 -i inventory -m command -a "cat /opt/itadmin/blog.txt"
ansible stapp02 -i inventory -m command -a "cat /opt/itadmin/story.txt"
ansible stapp03 -i inventory -m command -a "cat /opt/itadmin/media.txt"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1` in output.  
*Solution*: Verify inventory details and SSH connectivity.  
```bash
cat /home/thor/ansible/inventory
ssh tony@172.16.238.10
ssh steve@172.16.238.11
ssh banner@172.16.238.12
```

### **Issue 2: File Not Found**  
*Symptoms*: `replace` task fails due to missing files.  
*Solution*: Verify file existence or create files if needed.  
```bash
ansible stapp01 -i /home/thor/ansible/inventory -m command -a "ls -l /opt/itadmin/blog.txt"
```

### **Issue 3: String Not Replaced**  
*Symptoms*: Target string not found in file.  
*Solution*: Check file content and `regexp` in `replace` task.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "cat /opt/itadmin/*.txt"
```

### **Issue 4: Permission Denied**  
*Symptoms*: `replace` task fails due to permissions.  
*Solution*: Ensure `become: true` is used.  
```bash
cat /home/thor/ansible/playbook.yml
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys or Ansible Vault instead of `ansible_ssh_pass`.  
- *Idempotency*: `replace` module ensures safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Optimization*: Use variables for file paths and strings to improve maintainability.  
- *Backup*: Use `backup: yes` in `replace` module for production to preserve original files.  

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Host-Specific Replacements*: Applying different replacements per server.  
2. *File Existence*: Ensuring target files exist.  
3. *String Matching*: Ensuring accurate `regexp` for replacements.  

### **Solution Approach**  
1. Used separate plays for each host to ensure precise targeting.  
2. Assumed files exist as per task context; added verification step.  
3. Used exact `regexp` and `replace` values from requirements.  

### **Key Success Factors**  
- *Inventory*: Verified pre-existing file.  
- *Playbook*: Correct use of `replace` module with host-specific plays.  
- *Verification*: Confirmed updated file content with `cat`.  

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *Files*: `/opt/itadmin/blog.txt`, `/opt/itadmin/story.txt`, `/opt/itadmin/media.txt`  
- *Command*: `ansible-playbook -i inventory playbook.yml`

### **Ansible Benefits**  
- *Automation*: Simplifies content replacement across multiple servers.  
- *Precision*: Host-specific plays ensure accurate updates.  
- *Idempotency*: Safe for repeated runs.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use Ansible Vault for sensitive data.  
- 📊 *Logging*: Enable Ansible logging for auditing:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ```  
- 🛡️ *Backup*: Use `backup: yes` in `replace` module to avoid data loss.  
- 🌐 *Scalability*: Extend to additional servers or files as needed.  
- 🚀 *Validation*: Verify file content before and after replacement.  

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Replace Module*: Updating file content with Ansible.  
- *Host-Specific Plays*: Using multiple plays for targeted tasks.  
- *Privilege Escalation*: Using `become: true` for file access.  
- *Verification*: Checking file content with `cat`.  

**Ansible Features Used**:  
- `replace` module for content updates.  
- `become` for privilege escalation.  
- Multiple plays for host-specific tasks.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to replace file content.  
- *Execution*: Ran successfully with specified command.  
- *Verification*: Confirmed updated content in files on all servers.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Files on all app servers have the correct content replacements, ready for validation.