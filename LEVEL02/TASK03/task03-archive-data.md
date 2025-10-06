# 🌟 **TASK03: Archive and Copy Data with Specific Ownership**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to archive data from `/usr/src/itadmin/` on each app server in *Stratos DC* and copy the archive to `/opt/itadmin/` with specific ownership. The task requires creating a playbook to automate this process using the `archive` and `file` modules.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use existing `/home/thor/ansible/inventory` with app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - Create a `demo.tar.gz` archive of `/usr/src/itadmin/` on each app server.  
   - Copy the archive to `/opt/itadmin/`.  
   - Set ownership: `tony:tony` (stapp01), `steve:steve` (stapp02), `banner:banner` (stapp03).  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Note**: The archive format must be `tar.gz`, and no extra arguments should be needed.

👉 **Your Mission**: Write the playbook to create and copy the archive, set ownership, and verify the results on all app servers.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`  
  - *App Servers*: As listed above  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Source Directory*: `/usr/src/itadmin/`  
  - *Destination Directory*: `/opt/itadmin/`  
  - *Archive File*: `/opt/itadmin/demo.tar.gz`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Pre-existing with app server details.  
- *Playbook*: Uses `archive` module to create `tar.gz` and `file` module for ownership.  
- *App Servers*: Receive the archive with specific ownership settings.  

### **Implementation Strategy**  
1. Verify the existing inventory file.  
2. Create `/home/thor/ansible/playbook.yml` to archive `/usr/src/itadmin/` and set ownership.  
3. Run the playbook using the specified command.  
4. Verify the archive’s existence, ownership, and contents on each app server.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Source Directory Missing**:  
   - *Issue*: `/usr/src/itadmin/` does not exist.  
   - *Fix*: Verify directory presence with `ls -ld /usr/src/itadmin/`.  
2. **Destination Directory Missing**:  
   - *Issue*: `/opt/itadmin/` does not exist, causing archive failure.  
   - *Fix*: Create directory using `file` module with `state: directory`.  
3. **Ownership Failure**:  
   - *Issue*: Users `tony`, `steve`, or `banner` do not exist.  
   - *Fix*: Check user existence with `id <user>`.  
4. **Incorrect Archive Format**:  
   - *Issue*: Archive is not `tar.gz`.  
   - *Fix*: Use `format: gz` in `archive` module.  

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
*Purpose*: Confirm inventory details (assumed pre-existing as per task).

### **Step 4: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Archive /usr/src/itadmin and copy to /opt/itadmin on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Ensure destination directory exists
      file:
        path: /opt/itadmin
        state: directory
        mode: '0755'
    - name: Create demo.tar.gz archive of /usr/src/itadmin
      archive:
        path: /usr/src/itadmin
        dest: /opt/itadmin/demo.tar.gz
        format: gz
    - name: Set ownership to tony on stapp01
      file:
        path: /opt/itadmin/demo.tar.gz
        owner: tony
        group: tony
      when: inventory_hostname == "stapp01"
    - name: Set ownership to steve on stapp02
      file:
        path: /opt/itadmin/demo.tar.gz
        owner: steve
        group: steve
      when: inventory_hostname == "stapp02"
    - name: Set ownership to banner on stapp03
      file:
        path: /opt/itadmin/demo.tar.gz
        owner: banner
        group: banner
      when: inventory_hostname == "stapp03"
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to archive data and set ownership.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Archive /usr/src/itadmin and copy to /opt/itadmin on all app servers] ****

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Ensure destination directory exists] *************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Create demo.tar.gz archive of /usr/src/itadmin] **************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Set ownership to tony on stapp01] ***************************************
changed: [stapp01]
skipped: [stapp02]
skipped: [stapp03]

TASK [Set ownership to steve on stapp02] **************************************
skipped: [stapp01]
changed: [stapp02]
skipped: [stapp03]

TASK [Set ownership to banner on stapp03] *************************************
skipped: [stapp01]
skipped: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0
stapp02                    : ok=3    changed=2    unreachable=0    failed=0
stapp03                    : ok=3    changed=2    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to create and copy the archive.

### **Step 6: Verify Archive**  
For each server:  
- **App Server 1**:  
  ```bash
  ssh tony@172.16.238.10 'ls -l /opt/itadmin/demo.tar.gz && echo "---- contents ----" && tar -tzf /opt/itadmin/demo.tar.gz | head -n 20'
  ```
  *Password*: `Ir0nM@n`  
- **App Server 2**:  
  ```bash
  ssh steve@172.16.238.11 'ls -l /opt/itadmin/demo.tar.gz && echo "---- contents ----" && tar -tzf /opt/itadmin/demo.tar.gz | head -n 20'
  ```
  *Password*: `Am3ric@`  
- **App Server 3**:  
  ```bash
  ssh banner@172.16.238.12 'ls -l /opt/itadmin/demo.tar.gz && echo "---- contents ----" && tar -tzf /opt/itadmin/demo.tar.gz | head -n 20'
  ```
  *Password*: `BigGr33n`  
**Expected Output (example for stapp01)**:
```bash
-rw-rw-r-- 1 tony tony <size> Oct 6 09:41 /opt/itadmin/demo.tar.gz
---- contents ----
<list of files in /usr/src/itadmin/>
```
*Purpose*: Confirm archive exists, ownership is correct, and contents are valid.

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
- name: Archive /usr/src/itadmin and copy to /opt/itadmin on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Ensure destination directory exists
      file:
        path: /opt/itadmin
        state: directory
        mode: '0755'
    - name: Create demo.tar.gz archive of /usr/src/itadmin
      archive:
        path: /usr/src/itadmin
        dest: /opt/itadmin/demo.tar.gz
        format: gz
    - name: Set ownership to tony on stapp01
      file:
        path: /opt/itadmin/demo.tar.gz
        owner: tony
        group: tony
      when: inventory_hostname == "stapp01"
    - name: Set ownership to steve on stapp02
      file:
        path: /opt/itadmin/demo.tar.gz
        owner: steve
        group: steve
      when: inventory_hostname == "stapp02"
    - name: Set ownership to banner on stapp03
      file:
        path: /opt/itadmin/demo.tar.gz
        owner: banner
        group: banner
      when: inventory_hostname == "stapp03"
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` targets all servers.  
- *Privilege Escalation*: `become: yes` for root access.  
- *Tasks*:  
  - *Directory Creation*: `file` module ensures `/opt/itadmin/` exists.  
  - *Archive*: `archive` module creates `demo.tar.gz`.  
  - *Ownership*: `file` module with `when` conditions sets ownership.  
*Purpose*: Archives data and configures ownership per server.

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
| Playbook     | Task Modules  | `file`, `archive` | Manages directories and archives |
| Playbook     | Source Path   | `/usr/src/itadmin/` | Directory to archive |
| Playbook     | Archive Path  | `/opt/itadmin/demo.tar.gz` | Destination archive |
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
**Expected Output**: As shown in Code Analysis.

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml
```
**Expected Output**: As shown in Step 5.

### **Step 3: Verify Archive on Each Server**  
```bash
ssh tony@172.16.238.10 'ls -l /opt/itadmin/demo.tar.gz && echo "---- contents ----" && tar -tzf /opt/itadmin/demo.tar.gz | head -n 20'
ssh steve@172.16.238.11 'ls -l /opt/itadmin/demo.tar.gz && echo "---- contents ----" && tar -tzf /opt/itadmin/demo.tar.gz | head -n 20'
ssh banner@172.16.238.12 'ls -l /opt/itadmin/demo.tar.gz && echo "---- contents ----" && tar -tzf /opt/itadmin/demo.tar.gz | head -n 20'
```
**Expected Output**: Archive exists with correct ownership and contents.

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh tony@172.16.238.10
ssh steve@172.16.238.11
ssh banner@172.16.238.12
```
*Purpose*: Ensure SSH access.

### **Test Playbook Syntax**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml --syntax-check
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml --check
```
*Purpose*: Simulate archive creation.

### **Test Source Directory**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -ld /usr/src/itadmin"
```
*Purpose*: Confirm source directory exists.

### **Test User Existence**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "id tony"
ansible all -i /home/thor/ansible/inventory -m command -a "id steve"
ansible all -i /home/thor/ansible/inventory -m command -a "id banner"
```
*Purpose*: Verify users exist.

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

# Verify archive
ssh tony@172.16.238.10 'ls -l /opt/itadmin/demo.tar.gz'
ssh steve@172.16.238.11 'ls -l /opt/itadmin/demo.tar.gz'
ssh banner@172.16.238.12 'ls -l /opt/itadmin/demo.tar.gz'
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1` in output.  
*Solution*: Verify inventory and SSH connectivity.  
```bash
cat /home/thor/ansible/inventory
ssh tony@172.16.238.10
ssh steve@172.16.238.11
ssh banner@172.16.238.12
```

### **Issue 2: Archive Creation Fails**  
*Symptoms*: `/opt/itadmin/demo.tar.gz` not created.  
*Solution*: Check source directory and `become: yes`.  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml -v
ansible all -i /home/thor/ansible/inventory -m command -a "ls -ld /usr/src/itadmin"
```

### **Issue 3: Incorrect Ownership**  
*Symptoms*: Wrong user or group on archive.  
*Solution*: Validate `when` conditions and user existence.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /opt/itadmin/demo.tar.gz"
ansible all -i /home/thor/ansible/inventory -m command -a "id tony"
```

### **Issue 4: Incorrect Archive Format**  
*Symptoms*: Archive not `tar.gz`.  
*Solution*: Ensure `format: gz` in `archive` module.  
```bash
cat /home/thor/ansible/playbook.yml
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys or Ansible Vault instead of passwords.  
- *Idempotency*: `archive` module ensures safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Optimization*: Consider using variables for paths to improve maintainability.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Conditional Ownership*: Setting different owners per server.  
2. *Directory Creation*: Ensuring `/opt/itadmin/` exists.  
3. *Archive Format*: Creating a `tar.gz` archive.  
4. *Privilege Escalation*: Requiring root access for file operations.

### **Solution Approach**  
1. Used `file` module to create `/opt/itadmin/`.  
2. Used `archive` module with `format: gz` for `tar.gz`.  
3. Applied `when` conditions for host-specific ownership.  
4. Included `become: yes` for root access.

### **Key Success Factors**  
- *Inventory*: Pre-existing and verified.  
- *Playbook*: Correct use of `archive` and `file` modules.  
- *Verification*: Confirmed archive and ownership.

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *Archive*: `/opt/itadmin/demo.tar.gz`  
- *Command*: `ansible-playbook -i inventory playbook.yml`

### **Ansible Benefits**  
- *Automation*: Simplifies archiving across multiple servers.  
- *Flexibility*: Conditional tasks for per-host customization.  
- *Idempotency*: Safe for repeated runs.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use SSH keys or Ansible Vault for credentials.  
- 📊 *Logging*: Log archive operations for auditing:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ```  
- 🛡️ *Permissions*: Avoid permissive directory permissions in production.  
- 🌐 *Scalability*: Extend to additional servers or directories as needed.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Archive Module*: Creating `tar.gz` archives with Ansible.  
- *Conditional Tasks*: Using `when` for host-specific actions.  
- *File Module*: Managing directories and ownership.  
- *Verification*: Checking archive contents and properties.

**Ansible Features Used**:  
- `archive` module for creating `tar.gz`.  
- `file` module for directory and ownership management.  
- `when` conditionals for host-specific tasks.  
- `become` for privilege escalation.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified existing `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to archive and set ownership.  
- *Execution*: Ran successfully with specified command.  
- *Verification*: Confirmed archive and ownership on all servers.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: `demo.tar.gz` is created in `/opt/itadmin/` with correct ownership, ready for validation.