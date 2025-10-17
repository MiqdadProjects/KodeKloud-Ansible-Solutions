# 🌟 **TASK05: Copy Files with Conditionals**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* requires an Ansible playbook to copy specific files to app servers in *Stratos DC* using conditionals to ensure each server receives the correct file with appropriate ownership and permissions.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use `/home/thor/ansible/inventory` on the jump host.  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - Copy `/usr/src/dba/blog.txt` to `/opt/dba/blog.txt` on `stapp01` with owner `tony`, group `tony`, and permissions `0644`.  
   - Copy `/usr/src/dba/story.txt` to `/opt/dba/story.txt` on `stapp02` with owner `steve`, group `steve`, and permissions `0644`.  
   - Copy `/usr/src/dba/media.txt` to `/opt/dba/media.txt` on `stapp03` with owner `banner`, group `banner`, and permissions `0644`.  
   - Use `inventory_hostname` with `when` conditionals to target specific hosts.  
   - Target all hosts (`hosts: all`) in the playbook.  
3. **Execution**: Run with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```

👉 **Your Mission**: Write a playbook to copy the specified files to the correct app servers using conditionals, ensuring proper ownership and permissions.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`.  
  - *App Servers*:  
    - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
    - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
    - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Source Files*: `/usr/src/dba/blog.txt`, `/usr/src/dba/story.txt`, `/usr/src/dba/media.txt`  
  - *Destination Files*: `/opt/dba/blog.txt`, `/opt/dba/story.txt`, `/opt/dba/media.txt`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines app servers (`stapp01`, `stapp02`, `stapp03`) for Ansible management.  
- *Playbook*: Uses `file` and `copy` modules with `when` conditionals to deploy files to specific servers.  
- *App Servers*: Each receives a unique file with correct ownership and permissions.  

### **Implementation Strategy**  
1. Verify the inventory file to ensure correct host details.  
2. Create `/home/thor/ansible/playbook.yml` with tasks to:  
   - Create the `/opt/dba` directory on all servers.  
   - Copy files to their respective servers using `inventory_hostname` conditionals.  
3. Execute the playbook and verify file deployment, ownership, and permissions.  

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Source File Missing**:  
   - *Issue*: `/usr/src/dba/*.txt` files do not exist.  
   - *Fix*: Verify source files on app servers before running the playbook.  
2. **Conditional Mismatch**:  
   - *Issue*: Files copied to incorrect servers.  
   - *Fix*: Ensure `when` conditions use correct `inventory_hostname` values.  
3. **Destination Directory Missing**:  
   - *Issue*: `/opt/dba` does not exist, causing copy failure.  
   - *Fix*: Use `file` module to create `/opt/dba` with proper permissions.  
4. **Permission Issues**:  
   - *Issue*: Incorrect ownership or permissions on destination files.  
   - *Fix*: Set `owner`, `group`, and `mode` in `copy` tasks.  
5. **Inventory Errors**:  
   - *Issue*: Playbook fails to connect to hosts.  
   - *Fix*: Validate inventory file and SSH credentials.

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as user `thor`.

### **Step 2: Navigate to Ansible Directory**  
```bash
cd /home/thor/ansible
```
*Purpose*: Move to the directory containing the inventory and playbook.

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
*Purpose*: Confirm inventory details for app servers.

### **Step 4: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Copy files to respective app servers based on hostname
  hosts: all
  become: yes
  gather_facts: yes
  tasks:
    - name: Ensure /opt/dba exists
      file:
        path: /opt/dba
        state: directory
        owner: root
        group: root
        mode: '0755'
    - name: Copy blog.txt to App Server 1
      copy:
        src: /usr/src/dba/blog.txt
        dest: /opt/dba/blog.txt
        owner: tony
        group: tony
        mode: '0644'
      when: inventory_hostname == "stapp01"
    - name: Copy story.txt to App Server 2
      copy:
        src: /usr/src/dba/story.txt
        dest: /opt/dba/story.txt
        owner: steve
        group: steve
        mode: '0644'
      when: inventory_hostname == "stapp02"
    - name: Copy media.txt to App Server 3
      copy:
        src: /usr/src/dba/media.txt
        dest: /opt/dba/media.txt
        owner: banner
        group: banner
        mode: '0644'
      when: inventory_hostname == "stapp03"
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to copy files to specific servers using conditionals.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Copy files to respective app servers based on hostname] *******************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Ensure /opt/dba exists] *************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Copy blog.txt to App Server 1] *******************************************
changed: [stapp01]
skipped: [stapp02]
skipped: [stapp03]

TASK [Copy story.txt to App Server 2] ******************************************
skipped: [stapp01]
changed: [stapp02]
skipped: [stapp03]

TASK [Copy media.txt to App Server 3] ******************************************
skipped: [stapp01]
skipped: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0
stapp02                    : ok=3    changed=2    unreachable=0    failed=0
stapp03                    : ok=3    changed=2    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to copy files to their respective servers.

### **Step 6: Verify Setup**  
```bash
ansible stapp01 -i inventory -m shell -a "ls -l /opt/dba/blog.txt"
ansible stapp02 -i inventory -m shell -a "ls -l /opt/dba/story.txt"
ansible stapp03 -i inventory -m shell -a "ls -l /opt/dba/media.txt"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
-rw-r--r-- 1 tony tony 35 Oct 17 17:04 /opt/dba/blog.txt
stapp02 | CHANGED | rc=0 >>
-rw-r--r-- 1 steve steve 27 Oct 17 17:04 /opt/dba/story.txt
stapp03 | CHANGED | rc=0 >>
-rw-r--r-- 1 banner banner 22 Oct 17 17:04 /opt/dba/media.txt
```
*Purpose*: Confirm files are copied with correct ownership and permissions.

### **Step 7: Verify File Existence and Content**  
```bash
ansible stapp01 -i inventory -m shell -a "cat /opt/dba/blog.txt"
ansible stapp02 -i inventory -m shell -a "cat /opt/dba/story.txt"
ansible stapp03 -i inventory -m shell -a "cat /opt/dba/media.txt"
```
*Purpose*: Ensure files are present and contain expected content.

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
- name: Copy files to respective app servers based on hostname
  hosts: all
  become: yes
  gather_facts: yes
  tasks:
    - name: Ensure /opt/dba exists
      file:
        path: /opt/dba
        state: directory
        owner: root
        group: root
        mode: '0755'
    - name: Copy blog.txt to App Server 1
      copy:
        src: /usr/src/dba/blog.txt
        dest: /opt/dba/blog.txt
        owner: tony
        group: tony
        mode: '0644'
      when: inventory_hostname == "stapp01"
    - name: Copy story.txt to App Server 2
      copy:
        src: /usr/src/dba/story.txt
        dest: /opt/dba/story.txt
        owner: steve
        group: steve
        mode: '0644'
      when: inventory_hostname == "stapp02"
    - name: Copy media.txt to App Server 3
      copy:
        src: /usr/src/dba/media.txt
        dest: /opt/dba/media.txt
        owner: banner
        group: banner
        mode: '0644'
      when: inventory_hostname == "stapp03"
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` targets all app servers.  
- *Privilege Escalation*: `become: yes` for root access.  
- *Tasks*:  
  - *Directory*: Creates `/opt/dba` with `file` module.  
  - *Copy Tasks*: Use `copy` module with `when` conditionals to copy files to specific servers.  
*Purpose*: Automates file copying with conditionals.

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
| Playbook     | Task Modules  | `file`, `copy` | Manages directories and files |
| Playbook     | File Path     | `/opt/dba/blog.txt` | File for stapp01 |
| Playbook     | File Path     | `/opt/dba/story.txt` | File for stapp02 |
| Playbook     | File Path     | `/opt/dba/media.txt` | File for stapp03 |
| Playbook     | Ownership     | `tony:tony`, `steve:steve`, `banner:banner` | Owners and groups |
| Playbook     | Permissions   | `0644`    | File permissions |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/ansible/inventory
```
*Purpose*: Confirm host details.

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i inventory playbook.yml
```
*Purpose*: Ensure successful execution.

### **Step 3: Verify File Deployment**  
```bash
ansible stapp01 -i inventory -m shell -a "ls -l /opt/dba/blog.txt"
ansible stapp02 -i inventory -m shell -a "ls -l /opt/dba/story.txt"
ansible stapp03 -i inventory -m shell -a "ls -l /opt/dba/media.txt"
```
*Purpose*: Confirm files, ownership, and permissions.

### **Step 4: Verify File Content**  
```bash
ansible stapp01 -i inventory -m shell -a "cat /opt/dba/blog.txt"
ansible stapp02 -i inventory -m shell -a "cat /opt/dba/story.txt"
ansible stapp03 -i inventory -m shell -a "cat /opt/dba/media.txt"
```
*Purpose*: Ensure files contain expected content.

### **Step 5: Verify No Incorrect Files**  
```bash
ansible stapp01 -i inventory -m shell -a "ls /opt/dba/ | grep -E 'story.txt|media.txt' || echo 'No incorrect files'"
ansible stapp02 -i inventory -m shell -a "ls /opt/dba/ | grep -E 'blog.txt|media.txt' || echo 'No incorrect files'"
ansible stapp03 -i inventory -m shell -a "ls /opt/dba/ | grep -E 'blog.txt|story.txt' || echo 'No incorrect files'"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
No incorrect files
stapp02 | CHANGED | rc=0 >>
No incorrect files
stapp03 | CHANGED | rc=0 >>
No incorrect files
```
*Purpose*: Ensure only the correct files are copied to each server.

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh tony@172.16.238.10
ssh steve@172.16.238.11
ssh banner@172.16.238.12
```
*Purpose*: Verify SSH access to app servers.

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
*Purpose*: Simulate playbook execution without changes.

### **Test Source Files**  
```bash
ansible all -i inventory -m shell -a "ls -l /usr/src/dba/"
```
*Purpose*: Confirm source files exist.

### **Test Directory Existence**  
```bash
ansible all -i inventory -m shell -a "ls -ld /opt/dba"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
drwxr-xr-x 2 root root 4096 Oct 17 17:04 /opt/dba
```
*Purpose*: Verify `/opt/dba` directory.

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
# Verify
ansible stapp01 -i inventory -m shell -a "ls -l /opt/dba/blog.txt"
ansible stapp02 -i inventory -m shell -a "ls -l /opt/dba/story.txt"
ansible stapp03 -i inventory -m shell -a "ls -l /opt/dba/media.txt"
ansible all -i inventory -m shell -a "ls /opt/dba/ | grep -E 'blog.txt|story.txt|media.txt' || echo 'No incorrect files'"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1` in output.  
*Solution*: Verify inventory and SSH credentials.  
```bash
cat /home/thor/ansible/inventory
ssh tony@172.16.238.10
```

### **Issue 2: Source File Not Found**  
*Symptoms*: `copy` task fails with file not found error.  
*Solution*: Check source file paths.  
```bash
ansible all -i inventory -m shell -a "ls -l /usr/src/dba/"
```

### **Issue 3: Incorrect File Copied**  
*Symptoms*: Wrong files on servers.  
*Solution*: Verify `when` conditions.  
```bash
cat playbook.yml
ansible-playbook -i inventory playbook.yml -v
```

### **Issue 4: Directory Missing**  
*Symptoms*: `/opt/dba` not found.  
*Solution*: Ensure `file` task creates directory.  
```bash
ansible all -i inventory -m shell -a "ls -ld /opt/dba"
```

### **Issue 5: Ownership/Permissions Incorrect**  
*Symptoms*: Files have wrong owner or permissions.  
*Solution*: Check `owner`, `group`, and `mode` in `copy` tasks.  
```bash
ansible all -i inventory -m shell -a "ls -l /opt/dba/"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys instead of passwords for production.  
- *Idempotency*: `file` and `copy` modules ensure safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Validation*: Always verify source files before execution.  
- *Conditionals*: Use `inventory_hostname` for precise targeting.  

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Conditional Targeting*: Ensuring files are copied to the correct servers.  
2. *Directory Setup*: Ensuring `/opt/dba` exists before copying.  
3. *Ownership Accuracy*: Setting correct user and group for each file.  

### **Solution Approach**  
1. Used `when: inventory_hostname == "<hostname>"` for precise targeting.  
2. Added `file` task to create `/opt/dba` with `root:root` ownership and `0755` permissions.  
3. Set `owner` and `group` in `copy` tasks to match user accounts (`tony`, `steve`, `banner`).  

### **Key Success Factors**  
- *Inventory*: Verified hostnames and credentials.  
- *Conditionals*: Correctly used `inventory_hostname` for filtering.  
- *Verification*: Checked file presence, ownership, and permissions.  

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *Files*: `/opt/dba/blog.txt` (stapp01), `/opt/dba/story.txt` (stapp02), `/opt/dba/media.txt` (stapp03)  
- *Conditionals*: Based on `inventory_hostname`  

### **Ansible Benefits**  
- *Automation*: Simplifies file deployment across multiple servers.  
- *Precision*: Conditionals ensure accurate file placement.  
- *Consistency*: Uniform permissions and ownership.  

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Restrict access to `/opt/dba` in production (e.g., `0700` permissions).  
- 📊 *Logging*: Enable Ansible logging for auditing:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ```  
- 🛡️ *Permissions*: Use group-based access control if files are shared.  
- 🌐 *Scalability*: Extend playbook to additional servers by updating inventory.  

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Conditionals*: Using `when` with `inventory_hostname` for targeted tasks.  
- *File Management*: Copying files with specific ownership and permissions.  
- *Directory Setup*: Creating directories with `file` module.  

**Ansible Features Used**:  
- `file` module for directory creation.  
- `copy` module for file deployment.  
- `when` conditionals for host-specific tasks.  
- `gather_facts` for accessing `inventory_hostname`.  

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` with conditional file copying.  
- *Execution*: Ran successfully, copying files to correct servers.  
- *Verification*: Confirmed file presence, ownership, and permissions.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Files `blog.txt`, `story.txt`, and `media.txt` are correctly deployed to `stapp01`, `stapp02`, and `stapp03`, respectively, with specified ownership and permissions.