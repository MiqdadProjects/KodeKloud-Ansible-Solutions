# 🌟 **TASK02: Create Files with ACL Permissions**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to create files on all app servers in *Stratos DC* with specific ownership and ACL permissions. The task requires a playbook to automate file creation and ACL configuration using the `file` and `acl` modules.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use existing `/home/thor/ansible/inventory` with app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - Create `/opt/sysops/blog.txt` on `stapp01` (owner: `root`, group: `root`), with ACL `r` for group `tony`.  
   - Create `/opt/sysops/story.txt` on `stapp02` (owner: `root`, group: `root`), with ACL `rw` for user `steve`.  
   - Create `/opt/sysops/media.txt` on `stapp03` (owner: `root`, group: `root`), with ACL `rw` for group `banner`.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Note**: The playbook must be executable by `thor` without extra arguments.

👉 **Your Mission**: Write the playbook to create files, set ownership, apply ACLs, and verify the results.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`  
  - *App Servers*: As listed above  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Files*: `/opt/sysops/blog.txt` (stapp01), `/opt/sysops/story.txt` (stapp02), `/opt/sysops/media.txt` (stapp03)  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Pre-existing with app server details.  
- *Playbook*: Uses `file` module for file and directory creation, and `acl` module for permissions.  
- *App Servers*: Receive files with `root` ownership and specific ACLs.  

### **Implementation Strategy**  
1. Verify the existing inventory file.  
2. Create `/home/thor/ansible/playbook.yml` to create files and set ACLs using conditional tasks.  
3. Run the playbook using the specified command.  
4. Verify file existence, ownership, and ACL permissions.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Directory Missing**:  
   - *Issue*: `/opt/sysops/` does not exist.  
   - *Fix*: Create directory with `file` module.  
2. **ACL Module Not Available**:  
   - *Issue*: `acl` module fails due to missing `setfacl` on app servers.  
   - *Fix*: Install `acl` package using `package` module.  
3. **User/Group Non-Existent**:  
   - *Issue*: `tony`, `steve`, or `banner` do not exist.  
   - *Fix*: Verify with `id <user>`.  
4. **Incorrect ACLs**:  
   - *Issue*: Wrong permissions applied.  
   - *Fix*: Ensure correct `permissions` and `etype` in `acl` module.  

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
- name: Manage files and ACLs on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Install acl package
      package:
        name: acl
        state: present
    - name: Create /opt/sysops directory
      file:
        path: /opt/sysops
        state: directory
        owner: root
        group: root
        mode: '0755'
    - name: Create blog.txt on stapp01
      file:
        path: /opt/sysops/blog.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp01"
    - name: Set ACL for group tony on blog.txt
      acl:
        path: /opt/sysops/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present
      when: inventory_hostname == "stapp01"
    - name: Create story.txt on stapp02
      file:
        path: /opt/sysops/story.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp02"
    - name: Set ACL for user steve on story.txt
      acl:
        path: /opt/sysops/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present
      when: inventory_hostname == "stapp02"
    - name: Create media.txt on stapp03
      file:
        path: /opt/sysops/media.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp03"
    - name: Set ACL for group banner on media.txt
      acl:
        path: /opt/sysops/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
      when: inventory_hostname == "stapp03"
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Manage files and ACLs on all app servers] ********************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Install acl package] ****************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Create /opt/sysops directory] *******************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Create blog.txt on stapp01] *********************************************
changed: [stapp01]
skipped: [stapp02]
skipped: [stapp03]

TASK [Set ACL for group tony on blog.txt] *************************************
changed: [stapp01]
skipped: [stapp02]
skipped: [stapp03]

TASK [Create story.txt on stapp02] ********************************************
skipped: [stapp01]
changed: [stapp02]
skipped: [stapp03]

TASK [Set ACL for user steve on story.txt] ************************************
skipped: [stapp01]
changed: [stapp02]
skipped: [stapp03]

TASK [Create media.txt on stapp03] ********************************************
skipped: [stapp01]
skipped: [stapp02]
changed: [stapp03]

TASK [Set ACL for group banner on media.txt] **********************************
skipped: [stapp01]
skipped: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=4    changed=3    unreachable=0    failed=0
stapp02                    : ok=4    changed=3    unreachable=0    failed=0
stapp03                    : ok=4    changed=3    unreachable=0    failed=0
```

### **Step 6: Verify Files and ACLs**  
```bash
ansible stapp01 -i inventory -m command -a "ls -l /opt/sysops/blog.txt && getfacl /opt/sysops/blog.txt"
ansible stapp02 -i inventory -m command -a "ls -l /opt/sysops/story.txt && getfacl /opt/sysops/story.txt"
ansible stapp03 -i inventory -m command -a "ls -l /opt/sysops/media.txt && getfacl /opt/sysops/media.txt"
```
**Expected Output**:
- For `stapp01`:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  -rw-r--r--+ 1 root root 0 Oct 17 11:31 /opt/sysops/blog.txt
  # file: opt/sysops/blog.txt
  # owner: root
  # group: root
  user::rw-
  group::r--
  group:tony:r--
  mask::r--
  other::r--
  ```
- For `stapp02`:
  ```bash
  stapp02 | CHANGED | rc=0 >>
  -rw-rw-r--+ 1 root root 0 Oct 17 11:31 /opt/sysops/story.txt
  # file: opt/sysops/story.txt
  # owner: root
  # group: root
  user::rw-
  user:steve:rw-
  group::r--
  mask::rw-
  other::r--
  ```
- For `stapp03`:
  ```bash
  stapp03 | CHANGED | rc=0 >>
  -rw-rw-r--+ 1 root root 0 Oct 17 11:31 /opt/sysops/media.txt
  # file: opt/sysops/media.txt
  # owner: root
  # group: root
  user::rw-
  group::r--
  group:banner:rw-
  mask::rw-
  other::r--
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
- name: Manage files and ACLs on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Install acl package
      package:
        name: acl
        state: present
    - name: Create /opt/sysops directory
      file:
        path: /opt/sysops
        state: directory
        owner: root
        group: root
        mode: '0755'
    - name: Create blog.txt on stapp01
      file:
        path: /opt/sysops/blog.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp01"
    - name: Set ACL for group tony on blog.txt
      acl:
        path: /opt/sysops/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present
      when: inventory_hostname == "stapp01"
    - name: Create story.txt on stapp02
      file:
        path: /opt/sysops/story.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp02"
    - name: Set ACL for user steve on story.txt
      acl:
        path: /opt/sysops/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present
      when: inventory_hostname == "stapp02"
    - name: Create media.txt on stapp03
      file:
        path: /opt/sysops/media.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp03"
    - name: Set ACL for group banner on media.txt
      acl:
        path: /opt/sysops/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
      when: inventory_hostname == "stapp03"
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` targets all servers in inventory.  
- *Privilege Escalation*: `become: yes` for root access.  
- *Tasks*:  
  - *ACL Package*: Installs `acl` package for `setfacl`.  
  - *Directory Creation*: Ensures `/opt/sysops/` exists.  
  - *File Creation*: Creates files with `root` ownership.  
  - *ACLs*: Sets specific ACL permissions per server.  
*Purpose*: Creates files and applies ACLs as required.

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
| Playbook     | Task Modules  | `package`, `file`, `acl` | Manages packages, files, and ACLs |
| Playbook     | File (stapp01) | `/opt/sysops/blog.txt` | File to create |
| Playbook     | Ownership (stapp01) | `root:root` | File owner and group |
| Playbook     | ACL (stapp01) | `group:tony:r` | Read permission for group tony |
| Playbook     | File (stapp02) | `/opt/sysops/story.txt` | File to create |
| Playbook     | Ownership (stapp02) | `root:root` | File owner and group |
| Playbook     | ACL (stapp02) | `user:steve:rw` | Read/write for user steve |
| Playbook     | File (stapp03) | `/opt/sysops/media.txt` | File to create |
| Playbook     | Ownership (stapp03) | `root:root` | File owner and group |
| Playbook     | ACL (stapp03) | `group:banner:rw` | Read/write for group banner |

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

### **Step 3: Verify Files and ACLs**  
```bash
ansible stapp01 -i inventory -m command -a "ls -l /opt/sysops/blog.txt && getfacl /opt/sysops/blog.txt"
ansible stapp02 -i inventory -m command -a "ls -l /opt/sysops/story.txt && getfacl /opt/sysops/story.txt"
ansible stapp03 -i inventory -m command -a "ls -l /opt/sysops/media.txt && getfacl /opt/sysops/media.txt"
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

### **Test ACL Package**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "rpm -q acl"
```

### **Test User/Group Existence**  
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

# Verify files and ACLs
ansible stapp01 -i inventory -m command -a "ls -l /opt/sysops/blog.txt && getfacl /opt/sysops/blog.txt"
ansible stapp02 -i inventory -m command -a "ls -l /opt/sysops/story.txt && getfacl /opt/sysops/story.txt"
ansible stapp03 -i inventory -m command -a "ls -l /opt/sysops/media.txt && getfacl /opt/sysops/media.txt"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: ACL Module Fails**  
*Symptoms*: `acl` task fails.  
*Solution*: Ensure `acl` package is installed.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "rpm -q acl"
```

### **Issue 2: Directory Missing**  
*Symptoms*: File creation fails.  
*Solution*: Verify `/opt/sysops/` creation.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -ld /opt/sysops"
```

### **Issue 3: Incorrect ACLs**  
*Symptoms*: Wrong permissions shown in `getfacl`.  
*Solution*: Check `acl` task parameters.  
```bash
cat /home/thor/ansible/playbook.yml
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use Ansible Vault for credentials.  
- *Idempotency*: `file` and `acl` modules ensure safe re-runs.  
- *Verbose Output*: Use `-v` for debugging.  
- *Optimization*: Use variables for paths and ACL settings.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *ACL Configuration*: Setting different ACLs per server.  
2. *Dependency*: Ensuring `acl` package is installed.  
3. *Directory Creation*: Ensuring `/opt/sysops/` exists.  

### **Solution Approach**  
1. Used `when` conditions for server-specific ACLs.  
2. Added `package` task for `acl`.  
3. Created `/opt/sysops/` with `file` module.

### **Key Success Factors**  
- *Inventory*: Verified pre-existing file.  
- *Playbook*: Correct use of `file` and `acl` modules.  
- *Verification*: Confirmed ACLs with `getfacl`.

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *Files*: `/opt/sysops/blog.txt`, `/opt/sysops/story.txt`, `/opt/sysops/media.txt`  
- *Command*: `ansible-playbook -i inventory playbook.yml`

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Restrict ACLs to minimum required permissions.  
- 📊 *Logging*: Log ACL changes for auditing.  
- 🛡️ *Validation*: Verify ACLs before deployment.  
- 🌐 *Scalability*: Extend to additional servers or files.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *ACL Module*: Setting file permissions with Ansible.  
- *Conditional Tasks*: Using `when` for server-specific actions.  
- *File Module*: Creating files and directories.  
- *Verification*: Using `getfacl` to check ACLs.

**Ansible Features Used**:  
- `package` module for dependencies.  
- `file` module for file management.  
- `acl` module for permissions.  
- `when` conditionals for customization.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to create files and set ACLs.  
- *Execution*: Ran successfully with specified command.  
- *Verification*: Confirmed files and ACLs on all servers.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Files with correct ownership and ACLs are created, ready for validation.