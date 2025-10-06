# 🌟 **TASK04: Unzip Archive with Permissions and Ownership**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to extract a zip archive (`/usr/src/dba/devops.zip`) from the jump host and copy its contents to `/opt/dba/` on all app servers in *Stratos DC*. The extracted data must have specific ownership and permissions, automated using an Ansible playbook.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use existing `/home/thor/ansible/inventory` with app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - Unzip `/usr/src/dba/devops.zip` to `/opt/dba/` on all app servers.  
   - Set ownership: `tony:tony` (stapp01), `steve:steve` (stapp02), `banner:banner` (stapp03).  
   - Set permissions: `0777` for extracted data.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Note**: The zip file is on the jump host, and no extra arguments should be needed.

👉 **Your Mission**: Write the playbook to unzip the archive, set permissions and ownership, and verify the results on all app servers.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, zip file at `/usr/src/dba/devops.zip`  
  - *App Servers*: As listed above  
  - *Inventory File*: `/home/thor/ansible/inventory`  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Destination Directory*: `/opt/dba/`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Pre-existing with app server details.  
- *Playbook*: Uses `unarchive` module to extract the zip and `file` module for directory creation.  
- *App Servers*: Receive extracted data with specified ownership and permissions.  

### **Implementation Strategy**  
1. Verify the existing inventory file.  
2. Create `/home/thor/ansible/playbook.yml` to unzip the archive and configure ownership/permissions.  
3. Run the playbook using the specified command.  
4. Verify the extracted data’s existence, ownership, and permissions.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Zip File Missing**:  
   - *Issue*: `/usr/src/dba/devops.zip` not found on jump host.  
   - *Fix*: Verify file existence with `ls -l /usr/src/dba/devops.zip`.  
2. **Destination Directory Missing**:  
   - *Issue*: `/opt/dba/` does not exist.  
   - *Fix*: Create directory using `file` module.  
3. **Ownership Failure**:  
   - *Issue*: Users `tony`, `steve`, or `banner` do not exist.  
   - *Fix*: Check user existence with `id <user>`.  
4. **Permission Issues**:  
   - *Issue*: Extracted data does not have `0777` permissions.  
   - *Fix*: Ensure `mode: "0777"` in `unarchive` module.  

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
- hosts: stapp01,stapp02,stapp03
  gather_facts: false
  become: true
  vars:
    archive_file: /usr/src/dba/devops.zip
    archive_dest: /opt/dba/
    owners:
      stapp01: tony
      stapp02: steve
      stapp03: banner
  tasks:
    - name: Ensure destination directory exists
      file:
        path: "{{ archive_dest }}"
        state: directory
        mode: "0777"
    - name: Unarchive devops.zip to /opt/dba with correct owner, group, and permissions
      unarchive:
        src: "{{ archive_file }}"
        dest: "{{ archive_dest }}"
        remote_src: no
        owner: "{{ owners[inventory_hostname] }}"
        group: "{{ owners[inventory_hostname] }}"
        mode: "0777"
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to unzip the archive and set ownership/permissions.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Unarchive devops.zip to /opt/dba] ***************************************

TASK [Ensure destination directory exists] *************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Unarchive devops.zip to /opt/dba with correct owner, group, and permissions] ***
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=2    changed=2    unreachable=0    failed=0
stapp02                    : ok=2    changed=2    unreachable=0    failed=0
stapp03                    : ok=2    changed=2    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to extract the zip file.

### **Step 6: Verify Extracted Data**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
total 4
drwxrwxrwx 2 tony tony 4096 Oct 6 09:41 unarchive
stapp02 | CHANGED | rc=0 >>
total 4
drwxrwxrwx 2 steve steve 4096 Oct 6 09:41 unarchive
stapp03 | CHANGED | rc=0 >>
total 4
drwxrwxrwx 2 banner banner 4096 Oct 6 09:41 unarchive
```
*Purpose*: Confirm extracted data exists with correct ownership and permissions.

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
- hosts: stapp01,stapp02,stapp03
  gather_facts: false
  become: true
  vars:
    archive_file: /usr/src/dba/devops.zip
    archive_dest: /opt/dba/
    owners:
      stapp01: tony
      stapp02: steve
      stapp03: banner
  tasks:
    - name: Ensure destination directory exists
      file:
        path: "{{ archive_dest }}"
        state: directory
        mode: "0777"
    - name: Unarchive devops.zip to /opt/dba with correct owner, group, and permissions
      unarchive:
        src: "{{ archive_file }}"
        dest: "{{ archive_dest }}"
        remote_src: no
        owner: "{{ owners[inventory_hostname] }}"
        group: "{{ owners[inventory_hostname] }}"
        mode: "0777"
```
- *Play Name*: Implicit (hosts listed explicitly).  
- *Hosts*: Explicitly lists `stapp01,stapp02,stapp03`.  
- *Privilege Escalation*: `become: true` for root access.  
- *Variables*: `archive_file`, `archive_dest`, `owners` for dynamic configuration.  
- *Tasks*:  
  - *Directory Creation*: `file` module ensures `/opt/dba/` exists.  
  - *Unarchive*: `unarchive` module extracts zip with ownership and permissions.  
*Purpose*: Extracts zip file and configures ownership/permissions.

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
| Playbook     | Hosts         | `stapp01,stapp02,stapp03` | Explicit server list |
| Playbook     | Task Modules  | `file`, `unarchive` | Manages directories and zip extraction |
| Playbook     | Source Path   | `/usr/src/dba/devops.zip` | Zip file on jump host |
| Playbook     | Destination Path | `/opt/dba/` | Extraction directory |
| Playbook     | Permissions   | `0777`    | Permissions for extracted data |
| Playbook     | Ownership (stapp01) | `tony:tony` | User and group |
| Playbook     | Ownership (stapp02) | `steve:steve` | User and group |
| Playbook     | Ownership (stapp03) | `banner:banner` | User and group |

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

### **Step 3: Verify Extracted Data**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba"
```
**Expected Output**: As shown in Step 6.

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

### **Test Zip File Existence**  
```bash
ls -l /usr/src/dba/devops.zip
```
*Purpose*: Confirm zip file exists on jump host.

### **Test User Existence**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "id tony"
ansible all -i /home/thor/ansible/inventory -m command -a "id steve"
ansible all -i /home/thor/ansible/inventory -m command -a "id banner"
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

# Verify extracted data
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1`.  
*Solution*: Verify inventory and SSH.  
```bash
cat /home/thor/ansible/inventory
ssh tony@172.16.238.10
```

### **Issue 2: Zip File Missing**  
*Symptoms*: `unarchive` task fails.  
*Solution*: Check zip file path.  
```bash
ls -l /usr/src/dba/devops.zip
```

### **Issue 3: Incorrect Ownership/Permissions**  
*Symptoms*: Wrong owner or permissions.  
*Solution*: Validate `owners` variable and `mode`.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba"
```

### **Issue 4: Extraction Fails**  
*Symptoms*: No files in `/opt/dba/`.  
*Solution*: Ensure `remote_src: no` and `become: true`.  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml -v
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use Ansible Vault for sensitive paths.  
- *Idempotency*: `unarchive` module ensures safe re-runs.  
- *Verbose Output*: Use `-v` for debugging.  
- *Optimization*: Use variables for paths and ownership.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Dynamic Ownership*: Setting different owners per server.  
2. *Remote Source*: Copying zip from jump host.  
3. *Permissions*: Ensuring `0777` for extracted data.  
4. *Directory Creation*: Ensuring `/opt/dba/` exists.

### **Solution Approach**  
1. Used `vars` with `owners` dictionary for dynamic ownership.  
2. Set `remote_src: no` to copy zip from jump host.  
3. Used `mode: "0777"` for permissions.  
4. Created `/opt/dba/` with `file` module.

### **Key Success Factors**  
- *Inventory*: Verified pre-existing file.  
- *Playbook*: Correct use of `unarchive` module.  
- *Verification*: Confirmed ownership and permissions.

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *Zip File*: `/usr/src/dba/devops.zip`  
- *Command*: `ansible-playbook -i inventory playbook.yml`

### **Ansible Benefits**  
- *Automation*: Simplifies zip extraction across servers.  
- *Flexibility*: Variables for dynamic configurations.  
- *Idempotency*: Safe for repeated runs.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Avoid `0777` permissions; use restrictive settings.  
- 📊 *Logging*: Log extraction operations.  
- 🛡️ *Validation*: Verify zip contents before extraction.  
- 🌐 *Scalability*: Extend to additional servers or archives.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Unarchive Module*: Extracting zip files with Ansible.  
- *Variables*: Using dictionaries for dynamic ownership.  
- *File Module*: Managing directories and permissions.  
- *Verification*: Checking extracted data properties.

**Ansible Features Used**:  
- `unarchive` module for zip extraction.  
- `file` module for directory creation.  
- Variables for dynamic configurations.  
- `become` for privilege escalation.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to unzip archive.  
- *Execution*: Ran successfully with specified command.  
- *Verification*: Confirmed extracted data, ownership, and permissions.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: `devops.zip` is extracted to `/opt/dba/` with correct ownership and permissions.