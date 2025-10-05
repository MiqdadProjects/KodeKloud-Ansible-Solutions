# 🌟 **TASK01: Create an Empty File on App Server 3**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs a **critical Ansible playbook** finalized on the **jump host** to automate a task on **App Server 3** in *Stratos DC*. A team member started the setup, and your role is to complete it by configuring the inventory and creating a playbook to generate an empty file.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Update `/home/thor/ansible/inventory` to target *App Server 3* with:  
   - *Hostname*: `stapp03`  
   - *IP Address*: `172.16.238.12`  
   - *Ansible User*: `banner`  
   - *SSH Password*: `BigGr33n`  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to create an empty file at `/tmp/file.txt` on *App Server 3*.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Verification**: Confirm `/tmp/file.txt` exists on *App Server 3*.  

**Note**: The `ansible` command is pre-configured on the jump host. No extra arguments are needed.  

👉 **Your Mission**: Update the inventory, craft the playbook, execute it, and verify the file creation.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: Configured with Ansible  
  - *Target Server*: App Server 3 (`stapp03`, IP: `172.16.238.12`)  
  - *Inventory File*: `/home/thor/ansible/inventory`  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *File to Create*: `/tmp/file.txt`  
- **Working Directory**: `/home/thor/ansible`

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines *App Server 3* with SSH credentials.  
- *Playbook*: Uses the `file` module to create an empty file.  
- *Target Server*: *App Server 3*, accessed via SSH with user `banner`.  

### **Implementation Strategy**  
1. Update `/home/thor/ansible/inventory` with *App Server 3* details.  
2. Create `/home/thor/ansible/playbook.yml` to generate `/tmp/file.txt`.  
3. Run the playbook with the specified command.  
4. Verify file creation via SSH.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Inventory Misconfiguration**:  
   - *Issue*: Wrong hostname, IP, user, or password.  
   - *Fix*: Verify `stapp03`, `172.16.238.12`, `banner`, `BigGr33n`.  
2. **Playbook Syntax Error**:  
   - *Issue*: Invalid YAML or module parameters.  
   - *Fix*: Check YAML syntax and `file` module with `state: touch`.  
3. **SSH Connectivity Issues**:  
   - *Issue*: Cannot connect to *App Server 3*.  
   - *Fix*: Test SSH with `ssh banner@172.16.238.12`.  
4. **Permission Issues**:  
   - *Issue*: User `banner` lacks write access to `/tmp`.  
   - *Fix*: Confirm permissions with `ls -ld /tmp`.

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host to run Ansible commands.

### **Step 2: Navigate to Working Directory**  
```bash
cd /home/thor/ansible
```
*Purpose*: Move to the directory for inventory and playbook files.

### **Step 3: Update Inventory File**  
```bash
vi inventory
```
**Content**:
```
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Configure *App Server 3* for Ansible.

### **Step 4: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Create an empty file on App Server 3
  hosts: stapp03
  tasks:
    - name: Create file /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Define a playbook to create an empty file.

### **Step 5: Run the Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```
PLAY [Create an empty file on App Server 3] ************************************

TASK [Gathering Facts] ********************************************************
ok: [stapp03]

TASK [Create file /tmp/file.txt] **********************************************
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to create the file.

### **Step 6: Verify File Creation**  
Open a new terminal and SSH into *App Server 3*:  
```bash
ssh banner@stapp03
```
*Password*: `BigGr33n`  
Check the file:  
```bash
cd /tmp
ls -l
```
**Expected Output**:
```
total 8
-rw-r--r-- 1 banner banner    0 Oct  5 16:16 file.txt
...
```
*Purpose*: Confirm the file `/tmp/file.txt` exists.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/ansible/inventory`)  
```
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hostname*: `stapp03`  
- *IP Address*: `172.16.238.12`  
- *Ansible User*: `banner`  
- *SSH Password*: `BigGr33n`  
*Purpose*: Defines *App Server 3* for Ansible.

### **Playbook** (`/home/thor/ansible/playbook.yml`)  
```yaml
---
- name: Create an empty file on App Server 3
  hosts: stapp03
  tasks:
    - name: Create file /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: Targets `stapp03`.  
- *Task*: Uses `file` module with `state: touch`.  
*Purpose*: Creates an empty file at `/tmp/file.txt`.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Inventory    | Hostname      | `stapp03` | Alias for App Server 3 |
| Inventory    | ansible_host  | `172.16.238.12` | IP address |
| Inventory    | ansible_user  | `banner`  | SSH user |
| Inventory    | ansible_ssh_pass | `BigGr33n` | SSH password |
| Playbook     | Hosts         | `stapp03` | Target host |
| Playbook     | Task Module   | `file`    | Manages files |
| Playbook     | File Path     | `/tmp/file.txt` | File location |
| Playbook     | File State    | `touch`   | Creates empty file |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/ansible/inventory
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
PLAY [Create an empty file on App Server 3] ************************************

TASK [Gathering Facts] ********************************************************
ok: [stapp03]

TASK [Create file /tmp/file.txt] **********************************************
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```

### **Step 3: Verify File on App Server 3**  
```bash
ssh banner@stapp03
```
*Password*: `BigGr33n`  
```bash
ls -l /tmp
```
**Expected Output**:
```
total 8
-rw-r--r-- 1 banner banner    0 Oct  5 16:16 file.txt
...
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

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd /home/thor/ansible

# Update inventory
vi inventory
# Content: stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n

# Create playbook
vi playbook.yml
# Content: (see playbook above)

# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify file
ssh banner@stapp03
ls -l /tmp
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1`.  
*Solution*: Check inventory and SSH.  
```bash
cat /home/thor/ansible/inventory
ssh banner@172.16.238.12
```

### **Issue 2: File Not Created**  
*Symptoms*: `/tmp/file.txt` missing.  
*Solution*: Check playbook and permissions.  
```bash
ansible-playbook -i inventory playbook.yml -v
ssh banner@stapp03
ls -l /tmp
```

### **Issue 3: Syntax Error**  
*Symptoms*: YAML syntax error.  
*Solution*: Validate syntax.  
```bash
ansible-playbook -i inventory playbook.yml --syntax-check
```

### **Issue 4: Permission Denied**  
*Symptoms*: Task fails due to permissions.  
*Solution*: Check `/tmp` permissions.  
```bash
ssh banner@stapp03
ls -ld /tmp
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Avoid plain-text passwords; use Ansible Vault or SSH keys.  
- *Idempotency*: `file` module with `state: touch` ensures safe re-runs.  
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
1. *Inventory Setup*: Ensuring accurate host details.  
2. *Playbook Simplicity*: Writing a concise task.  
3. *SSH Access*: Verifying connectivity.  
4. *Permissions*: Ensuring `banner` can write to `/tmp`.

### **Solution Approach**  
1. Configured inventory with *App Server 3* details.  
2. Created a simple playbook using `file` module.  
3. Executed with specified command.  
4. Verified file creation via SSH.

### **Key Success Factors**  
- *Inventory*: Correct SSH credentials.  
- *Playbook*: Idempotent file creation.  
- *Execution*: No extra arguments needed.  
- *Verification*: File exists on target.

### **Critical Details**  
- *Inventory*: `stapp03`, `172.16.238.12`, `banner`, `BigGr33n`  
- *Playbook*: Creates `/tmp/file.txt`  
- *Command*: `ansible-playbook -i inventory playbook.yml`  
- *File*: `/tmp/file.txt` (empty, owned by `banner`)

### **Ansible Benefits**  
- *Automation*: Simplifies file creation.  
- *Idempotency*: Safe for repeated runs.  
- *Simplicity*: Minimal configuration.  
- *Reusability*: Extensible for similar tasks.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use SSH keys or Ansible Vault.  
- 📊 *Logging*: Enable Ansible logging.  
- 🛡️ *Permissions*: Verify user access in production.  
- 🌐 *Scalability*: Extend to multiple hosts.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Inventory Configuration*: Defining hosts and credentials.  
- *Playbook Creation*: Using `file` module.  
- *Execution*: Running playbooks with inventory.  
- *Verification*: Checking remote server outcomes.  

**Ansible Features Used**:  
- `file` module with `state: touch`.  
- Inventory with SSH parameters.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Updated `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml`.  
- *Execution*: Ran successfully.  
- *Verification*: Confirmed `/tmp/file.txt` exists.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Empty file `/tmp/file.txt` created on *App Server 3*, ready for validation.

### **Next Steps**  
- *Testing*: Validate idempotency.  
- *Security*: Replace `ansible_ssh_pass` with SSH keys.  
- *Documentation*: Add playbook comments.  
- *Automation*: Integrate into CI/CD.