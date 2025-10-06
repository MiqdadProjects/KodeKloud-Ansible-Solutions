# 🌟 **TASK05: Deploy httpd Web Server with Sample Page**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* requires an `httpd` web server to be deployed on all app servers in *Stratos DC* with a sample web page created using Ansible’s `blockinfile` module. The web server must be running, and the page must have specific content, ownership, and permissions, all automated via a playbook executed by the `thor` user on the jump host.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use existing `/home/thor/ansible/inventory` with app servers:  
   - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
   - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
   - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
2. **Playbook**: Create `/home/thor/ansible/playbook.yml` to:  
   - Install the `httpd` package.  
   - Ensure the `httpd` service is running and enabled on boot.  
   - Create `/var/www/html/index.html` with the following content using `blockinfile` (without custom or empty markers):  
     ```
     Welcome to XfusionCorp!
     This is Nautilus sample file, created using Ansible!
     Please do not modify this file manually!
     ```  
   - Set ownership of `/var/www/html/index.html` to `apache:apache`.  
   - Set permissions of `/var/www/html/index.html` to `0644`.  
3. **Execution**: Ensure the playbook runs with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Note**: No extra arguments should be needed for execution, and the playbook must be run as `thor`.

👉 **Your Mission**: Create the playbook to deploy `httpd`, configure the sample web page, ensure the service is running, and verify all requirements on each app server.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`  
  - *App Servers*:  
    - `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`)  
    - `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
    - `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`)  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Web File*: `/var/www/html/index.html`  
  - *Service*: `httpd`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Pre-existing with app server details for SSH access.  
- *Playbook*: Uses `package`, `service`, `file`, and `blockinfile` modules to install `httpd`, manage the service, create the web directory, and configure `index.html`.  
- *App Servers*: Run `httpd` with a sample web page accessible at `/var/www/html/index.html`.  

### **Implementation Strategy**  
1. Verify the existing `/home/thor/ansible/inventory` file.  
2. Create `/home/thor/ansible/playbook.yml` to install `httpd`, start/enable the service, ensure the web directory exists, and configure `index.html` with the specified content, ownership, and permissions.  
3. Execute the playbook using the specified command.  
4. Verify the `httpd` service status, file content, ownership, and permissions on each app server.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Service Not Running**:  
   - *Issue*: `httpd` service fails to start.  
   - *Fix*: Ensure `service` module uses `state: started` and `enabled: true`.  
2. **File Content Incorrect**:  
   - *Issue*: `/var/www/html/index.html` does not contain the exact content.  
   - *Fix*: Verify `blockinfile` block matches the required text exactly.  
3. **Ownership/Permissions Issues**:  
   - *Issue*: `apache` user/group or `0644` permissions not set.  
   - *Fix*: Check `owner`, `group`, and `mode` in `blockinfile` task.  
4. **Directory Missing**:  
   - *Issue*: `/var/www/html/` does not exist, causing file creation failure.  
   - *Fix*: Use `file` module to create the directory.  
5. **Blockinfile Markers**:  
   - *Issue*: Custom or empty markers cause validation failure.  
   - *Fix*: Avoid specifying `marker` in `blockinfile`.  

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
cd /home/thor/ansible
```
*Purpose*: Move to the directory for the inventory and playbook.

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
- name: Deploy httpd web server and configure index.html
  hosts: stapp01,stapp02,stapp03
  become: true
  vars:
    deploy_file: /var/www/html/index.html
    mode: "0644"
  tasks:
    - name: Install httpd package
      package:
        name: httpd
        state: present
    - name: Enable and start httpd service
      service:
        name: httpd
        state: started
        enabled: true
    - name: Ensure /var/www/html directory exists
      file:
        path: /var/www/html
        state: directory
        owner: apache
        group: apache
        mode: "0755"
    - name: Add content to index.html
      blockinfile:
        path: "{{ deploy_file }}"
        create: true
        owner: apache
        group: apache
        mode: "{{ mode }}"
        block: |
          Welcome to XfusionCorp!
          This is Nautilus sample file, created using Ansible!
          Please do not modify this file manually!
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to install `httpd`, manage the service, and configure `index.html`.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Deploy httpd web server and configure index.html] *************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Install httpd package] ***************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Enable and start httpd service] ******************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Ensure /var/www/html directory exists] ***********************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Add content to index.html] ***********************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=5    changed=4    unreachable=0    failed=0
stapp02                    : ok=5    changed=4    unreachable=0    failed=0
stapp03                    : ok=5    changed=4    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to deploy `httpd` and configure the web page.

### **Step 6: Verify Service and File**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /var/www/html/index.html"
ansible all -i /home/thor/ansible/inventory -m command -a "cat /var/www/html/index.html"
ansible all -i /home/thor/ansible/inventory -m command -a "systemctl status httpd"
```
**Expected Output**:
- File permissions and ownership:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  -rw-r--r-- 1 apache apache <size> Oct 6 10:41 /var/www/html/index.html
  stapp02 | CHANGED | rc=0 >>
  -rw-r--r-- 1 apache apache <size> Oct 6 10:41 /var/www/html/index.html
  stapp03 | CHANGED | rc=0 >>
  -rw-r--r-- 1 apache apache <size> Oct 6 10:41 /var/www/html/index.html
  ```
- File content:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  # BEGIN ANSIBLE MANAGED BLOCK
  Welcome to XfusionCorp!
  This is Nautilus sample file, created using Ansible!
  Please do not modify this file manually!
  # END ANSIBLE MANAGED BLOCK
  ```
- Service status:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  ● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
     Active: active (running) since Mon 2025-10-06 10:41:XX UTC; ...
  ```
*Purpose*: Confirm `httpd` is running, `index.html` exists with correct content, ownership, and permissions.

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
- name: Deploy httpd web server and configure index.html
  hosts: stapp01,stapp02,stapp03
  become: true
  vars:
    deploy_file: /var/www/html/index.html
    mode: "0644"
  tasks:
    - name: Install httpd package
      package:
        name: httpd
        state: present
    - name: Enable and start httpd service
      service:
        name: httpd
        state: started
        enabled: true
    - name: Ensure /var/www/html directory exists
      file:
        path: /var/www/html
        state: directory
        owner: apache
        group: apache
        mode: "0755"
    - name: Add content to index.html
      blockinfile:
        path: "{{ deploy_file }}"
        create: true
        owner: apache
        group: apache
        mode: "{{ mode }}"
        block: |
          Welcome to XfusionCorp!
          This is Nautilus sample file, created using Ansible!
          Please do not modify this file manually!
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: Explicitly lists `stapp01,stapp02,stapp03` for precision.  
- *Privilege Escalation*: `become: true` for root access.  
- *Variables*: `deploy_file` and `mode` for reusable configuration.  
- *Tasks*:  
  - *Install*: `package` module installs `httpd`.  
  - *Service*: `service` module ensures `httpd` is running and enabled.  
  - *Directory*: `file` module creates `/var/www/html/`.  
  - *Content*: `blockinfile` module adds specified content to `index.html`.  
*Purpose*: Deploys `httpd` and configures the sample web page.

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
| Playbook     | Task Modules  | `package`, `service`, `file`, `blockinfile` | Manages package, service, directory, and file content |
| Playbook     | Package       | `httpd`   | Web server package |
| Playbook     | Service       | `httpd`   | Web server service |
| Playbook     | File Path     | `/var/www/html/index.html` | Sample web page |
| Playbook     | Ownership     | `apache:apache` | User and group for `index.html` |
| Playbook     | Permissions   | `0644`    | Permissions for `index.html` |

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

### **Step 3: Verify File Properties and Service**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /var/www/html/index.html"
ansible all -i /home/thor/ansible/inventory -m command -a "cat /var/www/html/index.html"
ansible all -i /home/thor/ansible/inventory -m command -a "systemctl status httpd"
```
**Expected Output**: As shown in Step 6.

### **Step 4: Verify Web Page Access (Optional)**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "curl http://localhost/index.html"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
Welcome to XfusionCorp!
This is Nautilus sample file, created using Ansible!
Please do not modify this file manually!
```
*Purpose*: Confirm the web page is accessible.

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

### **Test Package Availability**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "yum list httpd"
```
*Purpose*: Confirm `httpd` is available in repositories.

### **Test Service Status**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "systemctl status httpd"
```
*Purpose*: Confirm `httpd` service is running.

### **Test User Existence**  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "id apache"
```
*Purpose*: Verify `apache` user exists.

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

# Verify file and service
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /var/www/html/index.html"
ansible all -i /home/thor/ansible/inventory -m command -a "cat /var/www/html/index.html"
ansible all -i /home/thor/ansible/inventory -m command -a "systemctl status httpd"
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

### **Issue 2: Service Not Running**  
*Symptoms*: `httpd` service fails to start.  
*Solution*: Check `service` module and system logs.  
```bash
ansible-playbook -i /home/thor/ansible/inventory playbook.yml -v
ansible all -i /home/thor/ansible/inventory -m command -a "journalctl -u httpd"
```

### **Issue 3: File Content Incorrect**  
*Symptoms*: `index.html` does not match required content.  
*Solution*: Verify `blockinfile` block.  
```bash
cat /home/thor/ansible/playbook.yml
ansible all -i /home/thor/ansible/inventory -m command -a "cat /var/www/html/index.html"
```

### **Issue 4: Ownership/Permissions Incorrect**  
*Symptoms*: Wrong owner or permissions on `index.html`.  
*Solution*: Check `owner`, `group`, and `mode` in `blockinfile`.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -l /var/www/html/index.html"
ansible all -i /home/thor/ansible/inventory -m command -a "id apache"
```

### **Issue 5: Directory Missing**  
*Symptoms*: `/var/www/html/` not found.  
*Solution*: Ensure `file` module creates directory.  
```bash
ansible all -i /home/thor/ansible/inventory -m command -a "ls -ld /var/www/html"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys or Ansible Vault instead of `ansible_ssh_pass`.  
- *Idempotency*: All modules (`package`, `service`, `file`, `blockinfile`) ensure safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Web Access*: Test the web page with `curl` or a browser in production.  
- *Optimization*: Use templates instead of `blockinfile` for complex files.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Service Management*: Ensuring `httpd` is running and enabled.  
2. *File Content*: Adding exact content without custom markers.  
3. *Ownership/Permissions*: Setting `apache:apache` and `0644` correctly.  
4. *Directory Creation*: Ensuring `/var/www/html/` exists.  

### **Solution Approach**  
1. Used `package` and `service` modules for `httpd` installation and management.  
2. Used `blockinfile` with exact content and no custom markers.  
3. Set `owner`, `group`, and `mode` in `blockinfile`.  
4. Created `/var/www/html/` with `file` module.

### **Key Success Factors**  
- *Inventory*: Verified pre-existing file.  
- *Playbook*: Correct use of modules and variables.  
- *Verification*: Confirmed service, file content, ownership, and permissions.  

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *File*: `/var/www/html/index.html`  
- *Service*: `httpd`  
- *Command*: `ansible-playbook -i inventory playbook.yml`

### **Ansible Benefits**  
- *Automation*: Simplifies web server deployment across multiple servers.  
- *Consistency*: Ensures identical configuration on all servers.  
- *Idempotency*: Safe for repeated runs.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Configure `httpd` with secure settings (e.g., disable directory listing, use SSL).  
- 📊 *Logging*: Enable Ansible and `httpd` logging for auditing:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ansible all -i /home/thor/ansible/inventory -m command -a "tail /var/log/httpd/access_log"
  ```  
- 🛡️ *Permissions*: Use restrictive permissions (e.g., `0640`) in production.  
- 🌐 *Scalability*: Extend to additional servers or web pages as needed.  
- 🚀 *Firewall*: Ensure HTTP port (80) is open if needed:  
  ```bash
  ansible all -i /home/thor/ansible/inventory -m command -a "firewall-cmd --list-ports"
  ```

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Package Module*: Installing software packages with Ansible.  
- *Service Module*: Managing system services.  
- *File Module*: Creating directories with specific permissions.  
- *Blockinfile Module*: Managing file content with Ansible.  
- *Verification*: Checking service status and file properties.

**Ansible Features Used**:  
- `package` module for software installation.  
- `service` module for service management.  
- `file` module for directory creation.  
- `blockinfile` module for file content management.  
- `become` for privilege escalation.  
- Variables for reusable configuration.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory`.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to deploy `httpd` and configure `index.html`.  
- *Execution*: Ran successfully with specified command.  
- *Verification*: Confirmed `httpd` service, `index.html` content, ownership, and permissions.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: `httpd` is running on all app servers, and `/var/www/html/index.html` is configured with the required content, ownership, and permissions, ready for validation.