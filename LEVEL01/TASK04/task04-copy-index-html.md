# 🌟 **TASK04: Copy index.html to All Application Servers**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to automate the process of copying a file from the **jump host** to **all application servers** in *Stratos DC* using **Ansible**. The task involves creating an inventory file to manage the application servers and a playbook to copy a specific file to a designated directory on each server.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Create `/home/thor/ansible/inventory` on the jump host, including all application servers as managed nodes:  
   - *App Server 1*: `stapp01`, IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`  
   - *App Server 2*: `stapp02`, IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`  
   - *App Server 3*: `stapp03`, IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to copy `/usr/src/itadmin/index.html` to `/opt/itadmin/` on all application servers.  
3. **Execution**: Ensure the playbook runs successfully using:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Verification**: Confirm the file `/opt/itadmin/index.html` exists on all application servers and contains the text: `Welcome to xFusionCorp Industries !`.  

**Note**: The `ansible` command is pre-configured on the jump host. No additional arguments are required beyond the specified command.

👉 **Your Mission**: Create the inventory, write the playbook, execute it, and verify the file is copied correctly to all application servers.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: Ansible installed, user `thor`  
  - *Application Servers*:  
    - *App Server 1*: `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
    - *App Server 2*: `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
    - *App Server 3*: `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
  - *Inventory File*: `/home/thor/ansible/inventory`  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Source File*: `/usr/src/itadmin/index.html`  
  - *Destination Path*: `/opt/itadmin/`  
- **Working Directory**: `/home/thor/ansible`

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines all application servers with their respective SSH credentials.  
- *Playbook*: Uses the `copy` module to transfer the `index.html` file to the target directory.  
- *Application Servers*: Three servers (`stapp01`, `stapp02`, `stapp03`) receiving the file.  

### **Implementation Strategy**  
1. Create `/home/thor/ansible/inventory` with details for all three application servers.  
2. Create `/home/thor/ansible/playbook.yml` to copy `/usr/src/itadmin/index.html` to `/opt/itadmin/`.  
3. Run the playbook using the specified command.  
4. Verify the file exists and contains the correct content on each server via SSH.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Inventory Misconfiguration**:  
   - *Issue*: Incorrect hostnames, IPs, users, or passwords.  
   - *Fix*: Verify `stapp01`, `stapp02`, `stapp03` details match the requirements.  
2. **File Copy Failure**:  
   - *Issue*: Source file `/usr/src/itadmin/index.html` missing or destination path `/opt/itadmin/` not writable.  
   - *Fix*: Check source file existence and ensure `become: yes` for root permissions.  
3. **SSH Connectivity Issues**:  
   - *Issue*: Cannot connect to application servers.  
   - *Fix*: Test SSH with `ssh <user>@<ip>` for each server.  
4. **Directory Creation**:  
   - *Issue*: `/opt/itadmin/` does not exist on target servers.  
   - *Fix*: Ensure `copy` module creates parent directories or add a task to create `/opt/itadmin/`.  

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
cd /home/thor/ansible
```
*Purpose*: Move to the directory for inventory and playbook files.

### **Step 3: Create Inventory File**  
```bash
vi inventory
```
**Content**:
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Define all application servers with their SSH credentials.

### **Step 4: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Copy index.html file to application servers
  hosts: all
  become: yes
  tasks:
    - name: Copy index.html to /opt/itadmin/
      copy:
        src: /usr/src/itadmin/index.html
        dest: /opt/itadmin/
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to copy `index.html` to all application servers.

### **Step 5: Run the Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Copy index.html file to application servers] *****************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Copy index.html to /opt/itadmin/] ***************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to copy the file to all servers.

### **Step 6: Verify File on Each Application Server**  
For each server, open a new terminal and SSH:  
- **App Server 1**:  
  ```bash
  ssh tony@stapp01
  ```
  *Password*: `Ir0nM@n`  
  ```bash
  cd /opt/itadmin
  ls
  ```
  **Expected Output**:
  ```bash
  index.html
  ```
  ```bash
  cat index.html
  ```
  **Expected Output**:
  ```bash
  Welcome to xFusionCorp Industries !
  ```

- **App Server 2**:  
  ```bash
  ssh steve@stapp02
  ```
  *Password*: `Am3ric@`  
  ```bash
  cd /opt/itadmin
  ls
  ```
  **Expected Output**:
  ```bash
  index.html
  ```
  ```bash
  cat index.html
  ```
  **Expected Output**:
  ```bash
  Welcome to xFusionCorp Industries !
  ```

- **App Server 3**:  
  ```bash
  ssh banner@stapp03
  ```
  *Password*: `BigGr33n`  
  ```bash
  cd /opt/itadmin
  ls
  ```
  **Expected Output**:
  ```bash
  index.html
  ```
  ```bash
  cat index.html
  ```
  **Expected Output**:
  ```bash
  Welcome to xFusionCorp Industries !
  ```
*Purpose*: Confirm the file is copied and contains the correct content on all servers.

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
*Purpose*: Defines all application servers for Ansible management.

### **Playbook** (`/home/thor/ansible/playbook.yml`)  
```yaml
---
- name: Copy index.html file to application servers
  hosts: all
  become: yes
  tasks:
    - name: Copy index.html to /opt/itadmin/
      copy:
        src: /usr/src/itadmin/index.html
        dest: /opt/itadmin/
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` (targets all servers in inventory).  
- *Privilege Escalation*: `become: yes` for root access.  
- *Task*: Uses `copy` module to transfer `index.html`.  
*Purpose*: Copies the file to `/opt/itadmin/` on all servers.

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
| Playbook     | Task Module   | `copy`    | Copies files |
| Playbook     | Source Path   | `/usr/src/itadmin/index.html` | File to copy |
| Playbook     | Destination Path | `/opt/itadmin/` | Target directory |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/ansible/inventory
```
**Expected Output**:
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Copy index.html file to application servers] *****************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Copy index.html to /opt/itadmin/] ***************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0
stapp02                    : ok=2    changed=1    unreachable=0    failed=0
stapp03                    : ok=2    changed=1    unreachable=0    failed=0
```

### **Step 3: Verify File on Each Server**  
- **App Server 1**:  
  ```bash
  ssh tony@stapp01
  ```
  *Password*: `Ir0nM@n`  
  ```bash
  ls /opt/itadmin
  cat /opt/itadmin/index.html
  ```
  **Expected Output**:
  ```bash
  index.html
  Welcome to xFusionCorp Industries !
  ```

- **App Server 2**:  
  ```bash
  ssh steve@stapp02
  ```
  *Password*: `Am3ric@`  
  ```bash
  ls /opt/itadmin
  cat /opt/itadmin/index.html
  ```
  **Expected Output**:
  ```bash
  index.html
  Welcome to xFusionCorp Industries !
  ```

- **App Server 3**:  
  ```bash
  ssh banner@stapp03
  ```
  *Password*: `BigGr33n`  
  ```bash
  ls /opt/itadmin
  cat /opt/itadmin/index.html
  ```
  **Expected Output**:
  ```bash
  index.html
  Welcome to xFusionCorp Industries !
  ```

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh tony@172.16.238.10  # Password: Ir0nM@n
ssh steve@172.16.238.11  # Password: Am3ric@
ssh banner@172.16.238.12  # Password: BigGr33n
```
*Expected*: Successful login for each.  
*Purpose*: Ensure SSH access to all servers.

### **Test Playbook Syntax**  
```bash
ansible-playbook -i inventory playbook.yml --syntax-check
```
**Expected Output**:
```bash
playbook: playbook.yml
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i inventory playbook.yml --check
```
*Purpose*: Simulate file copy without changes.

### **Test Source File Existence**  
```bash
ls -l /usr/src/itadmin/index.html
```
*Purpose*: Confirm source file exists on jump host.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd /home/thor/ansible

# Create inventory
vi inventory
# Content: (see inventory above)

# Create playbook
vi playbook.yml
# Content: (see playbook above)

# Run playbook
ansible-playbook -i inventory playbook.yml

# Verify on each server
ssh tony@stapp01
ls /opt/itadmin
cat /opt/itadmin/index.html

ssh steve@stapp02
ls /opt/itadmin
cat /opt/itadmin/index.html

ssh banner@stapp03
ls /opt/itadmin
cat /opt/itadmin/index.html
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

### **Issue 2: File Not Copied**  
*Symptoms*: `/opt/itadmin/index.html` missing.  
*Solution*: Check source file, destination permissions, and `become`.  
```bash
ls /usr/src/itadmin/index.html
ansible-playbook -i inventory playbook.yml -v
ssh <user>@<server>
ls -ld /opt/itadmin
```

### **Issue 3: Syntax Error**  
*Symptoms*: YAML syntax error.  
*Solution*: Validate playbook syntax.  
```bash
ansible-playbook -i inventory playbook.yml --syntax-check
```

### **Issue 4: Destination Directory Missing**  
*Symptoms*: Copy fails due to missing `/opt/itadmin/`.  
*Solution*: Add task to create directory or rely on `copy` module’s auto-creation.  
```yaml
- name: Ensure /opt/itadmin exists
  file:
    path: /opt/itadmin
    state: directory
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Avoid plain-text passwords; use Ansible Vault or SSH keys.  
- *Idempotency*: `copy` module ensures file is updated only if changed.  
- *Verbose Output*: Use `-v` for detailed logs.  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Directory Creation*: `copy` module creates parent dirs; add explicit `file` task for clarity if needed.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Inventory Setup*: Managing multiple servers with different credentials.  
2. *Privilege Escalation*: Ensuring write access to `/opt/itadmin/`.  
3. *File Verification*: Checking file content across three servers.  
4. *SSH Access*: Handling unique users and passwords.

### **Solution Approach**  
1. Created inventory with all three servers’ details.  
2. Wrote playbook with `become: yes` to copy file as root.  
3. Executed playbook with specified command.  
4. Verified file presence and content via SSH.

### **Key Success Factors**  
- *Inventory*: Accurate credentials for all servers.  
- *Playbook*: Correct `copy` module usage with privilege escalation.  
- *Execution*: No extra arguments needed.  
- *Verification*: File exists with correct content.

### **Critical Details**  
- *Inventory*: Three servers with unique users and passwords  
- *Playbook*: Copies `index.html` to `/opt/itadmin/`  
- *Command*: `ansible-playbook -i inventory playbook.yml`  
- *File Content*: `Welcome to xFusionCorp Industries !`

### **Ansible Benefits**  
- *Automation*: Efficiently copies files to multiple servers.  
- *Idempotency*: Safe for repeated runs.  
- *Scalability*: Handles multiple hosts seamlessly.  
- *Simplicity*: Minimal configuration for file transfer.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use SSH keys or Ansible Vault instead of `ansible_ssh_pass`.  
- 📊 *Logging*: Enable Ansible logging for auditing.  
- 🛡️ *Permissions*: Set explicit file permissions in `copy` module for production.  
- 🌐 *Scalability*: Extend to additional servers or files as needed.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Inventory Management*: Configuring multiple hosts with unique credentials.  
- *Playbook Creation*: Using `copy` module for file transfers.  
- *Privilege Escalation*: Implementing `become: yes` for root access.  
- *Verification*: Checking file presence and content across servers.

**Ansible Features Used**:  
- `copy` module for file transfer.  
- Inventory with multiple hosts and SSH parameters.  
- `become` for privilege escalation.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Created `/home/thor/ansible/inventory` with all app servers.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to copy `index.html`.  
- *Execution*: Ran successfully with no extra arguments.  
- *Verification*: Confirmed file on all servers with correct content.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: The file `/opt/itadmin/index.html` is copied to all application servers, containing `Welcome to xFusionCorp Industries !`, ready for validation.

### **Next Steps**  
- *Testing*: Validate idempotency with multiple runs.  
- *Security*: Replace passwords with SSH keys or Ansible Vault.  
- *Enhancement*: Add file permission settings in `copy` module.  
- *Documentation*: Comment playbook for clarity.  
- *Automation*: Integrate into CI/CD pipeline.