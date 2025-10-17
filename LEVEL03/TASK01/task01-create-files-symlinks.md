# 🌟 **TASK01: Create Files and Symbolic Links on App Servers**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* is practicing Ansible file module usage to create soft links on all app servers in *Stratos DC*. The task requires creating a playbook to automate the creation of specific files and symbolic links on each app server, with defined ownership.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use existing `/home/thor/ansible/inventory` with app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - On `stapp01`: Create empty file `/opt/dba/blog.txt` (owner: `tony`, group: `tony`), and create a symbolic link from `/opt/dba` to `/var/www/html`.  
   - On `stapp02`: Create empty file `/opt/dba/story.txt` (owner: `steve`, group: `steve`), and create a symbolic link from `/opt/dba` to `/var/www/html`.  
   - On `stapp03`: Create empty file `/opt/dba/media.txt` (owner: `banner`, group: `banner`), and create a symbolic link from `/opt/dba` to `/var/www/html`.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Note**: The playbook must be executable by `thor` without extra arguments.

👉 **Your Mission**: Write the playbook to create files and symbolic links, set ownership, and verify the results on all app servers.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`  
  - *App Servers*: As listed above  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Files*: `/opt/dba/blog.txt` (stapp01), `/opt/dba/story.txt` (stapp02), `/opt/dba/media.txt` (stapp03)  
  - *Symbolic Link*: `/var/www/html` → `/opt/dba` on all app servers  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Pre-existing with app server details.  
- *Playbook*: Uses `file` module to create files and symbolic links with specific ownership.  
- *App Servers*: Receive files and symbolic links with correct configurations.  

### **Implementation Strategy**  
1. Verify the existing inventory file.  
2. Create `/home/thor/ansible/playbook.yml` to create files and symbolic links using conditional tasks.  
3. Run the playbook using the specified command.  
4. Verify file existence, ownership, and symbolic links on each app server.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Directory Missing**:  
   - *Issue*: `/opt/dba/` does not exist, causing file creation failure.  
   - *Fix*: Create `/opt/dba/` using `file` module with `state: directory`.  
2. **Symbolic Link Failure**:  
   - *Issue*: `/var/www/html` already exists as a directory.  
   - *Fix*: Remove or rename existing `/var/www/html` before creating the link.  
3. **Ownership Issues**:  
   - *Issue*: Users `tony`, `steve`, or `banner` do not exist.  
   - *Fix*: Verify user existence with `id <user>`.  
4. **Incorrect Link Path**:  
   - *Issue*: Symbolic link points to the wrong source.  
   - *Fix*: Ensure `src: /opt/dba` and `dest: /var/www/html` in `file` module.  

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
- name: Create files and symbolic links on app servers
  hosts: stapp01,stapp02,stapp03
  become: true
  tasks:
    - name: Ensure /opt/dba directory exists
      file:
        path: /opt/dba
        state: directory
        mode: '0755'
    - name: Create /opt/dba/blog.txt on stapp01
      file:
        path: /opt/dba/blog.txt
        state: touch
        owner: tony
        group: tony
      when: inventory_hostname == "stapp01"
    - name: Create /opt/dba/story.txt on stapp02
      file:
        path: /opt/dba/story.txt
        state: touch
        owner: steve
        group: steve
      when: inventory_hostname == "stapp02"
    - name: Create /opt/dba/media.txt on stapp03
      file:
        path: /opt/dba/media.txt
        state: touch
        owner: banner
        group: banner
      when: inventory_hostname == "stapp03"
    - name: Create symbolic link /var/www/html to /opt/dba
      file:
        src: /opt/dba
        dest: /var/www/html
        state: link
        force: yes
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to create files, set ownership, and create symbolic links.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Create files and symbolic links on app servers] ***************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Ensure /opt/dba directory exists] ***************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Create /opt/dba/blog.txt on stapp01] ************************************
changed: [stapp01]
skipped: [stapp02]
skipped: [stapp03]

TASK [Create /opt/dba/story.txt on stapp02] ***********************************
skipped: [stapp01]
changed: [stapp02]
skipped: [stapp03]

TASK [Create /opt/dba/media.txt on stapp03] ***********************************
skipped: [stapp01]
skipped: [stapp02]
changed: [stapp03]

TASK [Create symbolic link /var/www/html to /opt/dba] *************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0
stapp02                    : ok=3    changed=2    unreachable=0    failed=0
stapp03                    : ok=3    changed=2    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to create files and links.

### **Step 6: Verify Files and Links**  
```bash
ansible stapp01 -i inventory -m command -a "ls -l /opt/dba/blog.txt /var/www/html"
ansible stapp02 -i inventory -m command -a "ls -l /opt/dba/story.txt /var/www/html"
ansible stapp03 -i inventory -m command -a "ls -l /opt/dba/media.txt /var/www/html"
```
**Expected Output**:
- For `stapp01`:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  -rw-rw-r-- 1 tony tony 0 Oct 17 11:31 /opt/dba/blog.txt
  lrwxrwxrwx 1 root root 8 Oct 17 11:31 /var/www/html -> /opt/dba
  ```
- For `stapp02`:
  ```bash
  stapp02 | CHANGED | rc=0 >>
  -rw-rw-r-- 1 steve steve 0 Oct 17 11:31 /opt/dba/story.txt
  lrwxrwxrwx 1 root root 8 Oct 17 11:31 /var/www/html -> /opt/dba
  ```
- For `stapp03`:
  ```bash
  stapp03 | CHANGED | rc=0 >>
  -rw-rw-r-- 1 banner banner 0 Oct 17 11:31 /opt/dba/media.txt
  lrwxrwxrwx 1 root root 8 Oct 17 11:31 /var/www/html -> /opt/dba
  ```
*Purpose*: Confirm files exist with correct ownership and `/var/www/html` is a symlink to `/opt/dba`.

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
- name: Create files and symbolic links on app servers
  hosts: stapp01,stapp02,stapp03
  become: true
  tasks:
    - name: Ensure /opt/dba directory exists
      file:
        path: /opt/dba
        state: directory
        mode: '0755'
    - name: Create /opt/dba/blog.txt on stapp01
      file:
        path: /opt/dba/blog.txt
        state: touch
        owner: tony
        group: tony
      when: inventory_hostname == "stapp01"
    - name: Create /opt/dba/story.txt on stapp02
      file:
        path: /opt/dba/story.txt
        state: touch
        owner: steve
        group: steve
      when: inventory_hostname == "stapp02"
    - name: Create /opt/dba/media.txt on stapp03
      file:
        path: /opt/dba/media.txt
        state: touch
        owner: banner
        group: banner
      when: inventory_hostname == "stapp03"
    - name: Create symbolic link /var/www/html to /opt/dba
      file:
        src: /opt/dba
        dest: /var/www/html
        state: link
        force: yes
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: Explicitly lists `stapp01,stapp02,stapp03`.  
- *Privilege Escalation*: `become: true` for root access.  
- *Tasks*:  
  - *Directory Creation*: Ensures `/opt/dba/` exists.  
  - *File Creation*: Creates files with specific ownership using `state: touch`.  
  - *Symbolic Link*: Creates `/var/www/html` as a symlink to `/opt/dba` with `force: yes` to overwrite existing directories.  
*Purpose*: Creates files and symbolic links per requirements.

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
| Playbook     | Task Module   | `file`    | Manages files and symlinks |
| Playbook     | File (stapp01) | `/opt/dba/blog.txt` | File to create |
| Playbook     | Ownership (stapp01) | `tony:tony` | File owner and group |
| Playbook     | File (stapp02) | `/opt/dba/story.txt` | File to create |
| Playbook     | Ownership (stapp02) | `steve:steve` | File owner and group |
| Playbook     | File (stapp03) | `/opt/dba/media.txt` | File to create |
| Playbook     | Ownership (stapp03) | `banner:banner` | File owner and group |
| Playbook     | Symlink       | `/var/www/html -> /opt/dba` | Symbolic link |

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

### **Step 3: Verify Files and Symlinks**  
```bash
ansible stapp01 -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba/blog.txt /var/www/html"
ansible stapp02 -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba/story.txt /var/www/html"
ansible stapp03 -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba/media.txt /var/www/html"
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
*Purpose*: Simulate playbook execution.

### **Test Directory Existence**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -ld /opt/dba"
```
*Purpose*: Confirm `/opt/dba/` exists.

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

# Verify files and symlinks
ansible stapp01 -i inventory -m command -a "ls -l /opt/dba/blog.txt /var/www/html"
ansible stapp02 -i inventory -m command -a "ls -l /opt/dba/story.txt /var/www/html"
ansible stapp03 -i inventory -m command -a "ls -l /opt/dba/media.txt /var/www/html"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1`.  
*Solution*: Verify inventory and SSH connectivity.  
```bash
cat /home/thor/ansible/inventory
ssh tony@172.16.238.10
```

### **Issue 2: Directory Missing**  
*Symptoms*: File creation fails.  
*Solution*: Ensure `/opt/dba/` is created.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -ld /opt/dba"
```

### **Issue 3: Symlink Failure**  
*Symptoms*: `/var/www/html` is not a symlink.  
*Solution*: Check for existing directory and use `force: yes`.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -ld /var/www/html"
```

### **Issue 4: Ownership Incorrect**  
*Symptoms*: Wrong owner or group on files.  
*Solution*: Verify `owner` and `group` in playbook.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /opt/dba"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys or Ansible Vault for credentials.  
- *Idempotency*: `file` module ensures safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Optimization*: Use variables for file paths to improve maintainability.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Conditional Tasks*: Creating different files per server.  
2. *Symbolic Links*: Ensuring `/var/www/html` links to `/opt/dba`.  
3. *Ownership*: Setting correct owners per server.  
4. *Existing Directories*: Handling pre-existing `/var/www/html`.  

### **Solution Approach**  
1. Used `when` conditions for server-specific tasks.  
2. Used `file` module with `state: link` and `force: yes`.  
3. Set `owner` and `group` in `file` tasks.  
4. Added `force: yes` to overwrite existing `/var/www/html`.  

### **Key Success Factors**  
- *Inventory*: Verified pre-existing file.  
- *Playbook*: Correct use of `file` module with conditionals.  
- *Verification*: Confirmed file ownership and symlink creation.  

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *Files*: `/opt/dba/blog.txt`, `/opt/dba/story.txt`, `/opt/dba/media.txt`  
- *Symlink*: `/var/www/html -> /opt/dba`  
- *Command*: `ansible-playbook -i inventory playbook.yml`

### **Ansible Benefits**  
- *Automation*: Simplifies file and symlink creation.  
- *Flexibility*: Conditional tasks for per-server customization.  
- *Idempotency*: Safe for repeated runs.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use SSH keys or Ansible Vault for credentials.  
- 📊 *Logging*: Log file operations for auditing:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ```  
- 🛡️ *Permissions*: Avoid permissive directory permissions in production.  
- 🌐 *Scalability*: Extend to additional servers or files as needed.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *File Module*: Creating files and symbolic links with Ansible.  
- *Conditional Tasks*: Using `when` for host-specific actions.  
- *Privilege Escalation*: Using `become: true` for root access.  
- *Verification*: Checking file properties and symlinks.

**Ansible Features Used**:  
- `file` module for file and symlink management.  
- `when` conditionals for host-specific tasks.  
- `become` for privilege escalation.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to create files and symlinks.  
- *Execution*: Ran successfully with specified command.  
- *Verification*: Confirmed files and symlinks on all servers.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Files and symbolic links are configured on all app servers, ready for validation.