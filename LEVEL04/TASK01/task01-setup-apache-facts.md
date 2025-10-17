# 🌟 **TASK01: Setup Apache Web Server and Generate Facts File**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* aims to deploy a simple Apache web server on all app servers in *Stratos DC* using Ansible, along with a sample text file containing system architecture information derived from Ansible facts.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use `/home/thor/playbooks/inventory` on the jump host.  
2. **Playbook**: Create `/home/thor/playbooks/index.yml` to:  
   - Enable facts gathering to collect system information.  
   - Use the `blockinfile` module to create `/root/facts.txt` on all app servers with the content:  
     ```
     Ansible managed node architecture is <architecture>
     ```  
     (Use `ansible_architecture` variable with Jinja2 syntax.)  
   - Install the `httpd` package.  
   - Copy `/root/facts.txt` to `/var/www/html/index.html`.  
   - Start and enable the `httpd` service.  
3. **Execution**: Run the playbook with:  
   ```bash
   ansible-playbook -i /home/thor/playbooks/inventory /home/thor/playbooks/index.yml
   ```  
4. **Note**: Do not create a separate role; include all tasks in `index.yml`.

👉 **Your Mission**: Deploy `httpd`, create `facts.txt` with system architecture, copy it as `index.html`, and ensure the service is running.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/playbooks/inventory`.  
  - *App Servers*: `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`), `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`), `stapp03` (IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`).  
  - *Playbook File*: `/home/thor/playbooks/index.yml`  
  - *Files*: `/root/facts.txt`, `/var/www/html/index.html`  
  - *Service*: `httpd`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines app servers for Ansible management.  
- *Playbook*: Uses `blockinfile`, `package`, `file`, `copy`, and `service` modules to create files, install `httpd`, and manage services.  
- *App Servers*: Run `httpd` with `index.html` containing system architecture information.

### **Implementation Strategy**  
1. Verify the inventory file at `/home/thor/playbooks/inventory`.  
2. Create `/home/thor/playbooks/index.yml` to gather facts, create `facts.txt`, install `httpd`, copy the file, and start the service.  
3. Execute the playbook and verify the file content and service status on all app servers.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Facts Not Gathered**:  
   - *Issue*: `ansible_architecture` variable is undefined.  
   - *Fix*: Set `gather_facts: true` in the playbook.  
2. **Directory Missing**:  
   - *Issue*: `/var/www/html/` does not exist, causing file copy failure.  
   - *Fix*: Use `file` module to create the directory.  
3. **Service Not Running**:  
   - *Issue*: `httpd` service fails to start.  
   - *Fix*: Ensure `service` module uses `state: started` and `enabled: true`.  
4. **Incorrect File Content**:  
   - *Issue*: `facts.txt` or `index.html` lacks the correct block format.  
   - *Fix*: Use `blockinfile` with proper Jinja2 syntax (`{{ ansible_architecture }}`).  
5. **Permission Issues**:  
   - *Issue*: Ansible cannot write to `/root/facts.txt` or `/var/www/html/index.html`.  
   - *Fix*: Use `become: true` for root privileges.

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as user `thor`.

### **Step 2: Navigate to Playbooks Directory**  
```bash
cd /home/thor/playbooks
```
*Purpose*: Move to the directory containing the inventory and playbook.

### **Step 3: Verify Inventory File**  
```bash
cat /home/thor/playbooks/inventory
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
vi /home/thor/playbooks/index.yml
```
**Content**:
```yaml
---
- name: Setup Apache web server and generate facts file
  hosts: all
  become: true
  gather_facts: true
  tasks:
    - name: Create /root/facts.txt with system architecture info
      blockinfile:
        path: /root/facts.txt
        create: true
        block: |
          Ansible managed node architecture is {{ ansible_architecture }}
    - name: Install httpd package
      package:
        name: httpd
        state: present
    - name: Ensure /var/www/html directory exists
      file:
        path: /var/www/html
        state: directory
        owner: apache
        group: apache
        mode: '0755'
    - name: Copy facts.txt as index.html
      copy:
        src: /root/facts.txt
        dest: /var/www/html/index.html
        remote_src: true
        owner: apache
        group: apache
        mode: '0644'
    - name: Ensure httpd service is started and enabled
      service:
        name: httpd
        state: started
        enabled: true
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to deploy `httpd`, create `facts.txt`, and copy it to `index.html`.

### **Step 5: Run Playbook**  
```bash
ansible-playbook -i inventory index.yml
```
**Expected Output**:
```bash
PLAY [Setup Apache web server and generate facts file] **************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Create /root/facts.txt with system architecture info] ********************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Install httpd package] ***************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Ensure /var/www/html directory exists] ***********************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Copy facts.txt as index.html] ********************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Ensure httpd service is started and enabled] *****************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************
stapp01                    : ok=6    changed=5    unreachable=0    failed=0
stapp02                    : ok=6    changed=5    unreachable=0    failed=0
stapp03                    : ok=6    changed=5    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to apply the configuration.

### **Step 6: Verify Setup**  
```bash
ansible all -i inventory -m command -a "cat /var/www/html/index.html"
ansible all -i inventory -m service -a "name=httpd state=started"
```
**Expected Output**:
- File content:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  # BEGIN ANSIBLE MANAGED BLOCK
  Ansible managed node architecture is x86_64
  # END ANSIBLE MANAGED BLOCK
  stapp02 | CHANGED | rc=0 >>
  # BEGIN ANSIBLE MANAGED BLOCK
  Ansible managed node architecture is x86_64
  # END ANSIBLE MANAGED BLOCK
  stapp03 | CHANGED | rc=0 >>
  # BEGIN ANSIBLE MANAGED BLOCK
  Ansible managed node architecture is x86_64
  # END ANSIBLE MANAGED BLOCK
  ```
- Service status:
  ```bash
  stapp01 | SUCCESS => {
      "changed": false,
      "name": "httpd",
      "state": "started"
  }
  ```
*Purpose*: Confirm `index.html` content and `httpd` service status.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/playbooks/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hosts*: `stapp01`, `stapp02`, `stapp03`  
- *Details*: IP addresses, usernames, and passwords for SSH access.  
*Purpose*: Defines app servers for Ansible management.

### **Playbook** (`/home/thor/playbooks/index.yml`)  
```yaml
---
- name: Setup Apache web server and generate facts file
  hosts: all
  become: true
  gather_facts: true
  tasks:
    - name: Create /root/facts.txt with system architecture info
      blockinfile:
        path: /root/facts.txt
        create: true
        block: |
          Ansible managed node architecture is {{ ansible_architecture }}
    - name: Install httpd package
      package:
        name: httpd
        state: present
    - name: Ensure /var/www/html directory exists
      file:
        path: /var/www/html
        state: directory
        owner: apache
        group: apache
        mode: '0755'
    - name: Copy facts.txt as index.html
      copy:
        src: /root/facts.txt
        dest: /var/www/html/index.html
        remote_src: true
        owner: apache
        group: apache
        mode: '0644'
    - name: Ensure httpd service is started and enabled
      service:
        name: httpd
        state: started
        enabled: true
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` targets all app servers.  
- *Privilege Escalation*: `become: true` for root access.  
- *Tasks*:  
  - *Facts*: Creates `facts.txt` using `blockinfile` with `ansible_architecture`.  
  - *Package*: Installs `httpd`.  
  - *Directory*: Creates `/var/www/html/`.  
  - *Copy*: Copies `facts.txt` to `index.html` with correct ownership.  
  - *Service*: Starts and enables `httpd`.  
*Purpose*: Automates `httpd` deployment and file configuration.

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
| Playbook     | Task Modules  | `blockinfile`, `package`, `file`, `copy`, `service` | Manages files, packages, and services |
| Playbook     | File Path     | `/root/facts.txt` | Facts file location |
| Playbook     | File Path     | `/var/www/html/index.html` | Web page location |
| Playbook     | Service       | `httpd`   | Web server service |
| Playbook     | Ownership     | `apache:apache` | Owner and group for `index.html` |
| Playbook     | Permissions   | `0644`    | Permissions for `index.html` |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/playbooks/inventory
```
**Expected Output**: As shown in Code Analysis.

### **Step 2: Verify Playbook Execution**  
```bash
ansible-playbook -i /home/thor/playbooks/inventory index.yml
```
**Expected Output**: As shown in Step 5.

### **Step 3: Verify File Content and Service**  
```bash
ansible all -i inventory -m command -a "cat /var/www/html/index.html"
ansible all -i inventory -m service -a "name=httpd state=started"
ansible all -i inventory -m command -a "ls -l /var/www/html/index.html"
```
**Expected Output**:
- File content: As shown in Step 6.
- Service status: As shown in Step 6.
- File permissions:
  ```bash
  stapp01 | CHANGED | rc=0 >>
  -rw-r--r-- 1 apache apache <size> Oct 17 17:04 /var/www/html/index.html
  ```

### **Step 4: Verify Web Access (Optional)**  
```bash
ansible all -i inventory -m command -a "curl http://localhost/index.html"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
# BEGIN ANSIBLE MANAGED BLOCK
Ansible managed node architecture is x86_64
# END ANSIBLE MANAGED BLOCK
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
ansible-playbook -i inventory index.yml --syntax-check
```
**Expected Output**:
```bash
playbook: index.yml
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i inventory index.yml --check
```
*Purpose*: Simulate playbook execution without changes.

### **Test Package Availability**  
```bash
ansible all -i inventory -m command -a "yum list httpd"
```
*Purpose*: Confirm `httpd` package is available.

### **Test Service Status**  
```bash
ansible all -i inventory -m command -a "systemctl status httpd"
```
*Purpose*: Verify `httpd` is running.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd /home/thor/playbooks
# Create playbook
vi index.yml
# Run playbook
ansible-playbook -i inventory index.yml
# Verify
ansible all -i inventory -m command -a "cat /var/www/html/index.html"
ansible all -i inventory -m service -a "name=httpd state=started"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1` in output.  
*Solution*: Verify inventory details and SSH access.  
```bash
cat /home/thor/playbooks/inventory
ssh tony@172.16.238.10
```

### **Issue 2: Facts Not Gathered**  
*Symptoms*: `ansible_architecture` undefined.  
*Solution*: Check `gather_facts: true` in playbook.  
```bash
cat index.yml
```

### **Issue 3: Service Not Running**  
*Symptoms*: `httpd` fails to start.  
*Solution*: Check logs and `service` task.  
```bash
ansible all -i inventory -m command -a "journalctl -u httpd"
```

### **Issue 4: Directory Missing**  
*Symptoms*: `/var/www/html/` not found.  
*Solution*: Verify `file` task in playbook.  
```bash
ansible all -i inventory -m command -a "ls -ld /var/www/html"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys instead of passwords.  
- *Idempotency*: All modules ensure safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory index.yml -v
  ```  
- *Web Access*: Test with `curl` or browser in production.  

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Facts Gathering*: Ensuring `ansible_architecture` is available.  
2. *File Copy*: Ensuring `/var/www/html/` exists and `facts.txt` is copied correctly.  
3. *Service Management*: Ensuring `httpd` starts and persists.

### **Solution Approach**  
1. Enabled `gather_facts: true`.  
2. Added `file` task to create `/var/www/html/`.  
3. Used `service` module with `state: started` and `enabled: true`.

### **Key Success Factors**  
- *Inventory*: Verified server details.  
- *Playbook*: Correct use of `blockinfile` and `ansible_architecture`.  
- *Verification*: Confirmed file content and service status.

### **Critical Details**  
- *Inventory*: `/home/thor/playbooks/inventory`  
- *Playbook*: `/home/thor/playbooks/index.yml`  
- *Files*: `/root/facts.txt`, `/var/www/html/index.html`  
- *Service*: `httpd`  

### **Ansible Benefits**  
- *Automation*: Simplifies `httpd` deployment and file management.  
- *Consistency*: Ensures uniform configuration across servers.  

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Configure `httpd` with SSL and disable directory listing.  
- 📊 *Logging*: Enable Ansible and `httpd` logging:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ansible all -i inventory -m command -a "tail /var/log/httpd/access_log"
  ```  
- 🛡️ *Permissions*: Use restrictive permissions in production.  
- 🌐 *Scalability*: Extend to additional servers as needed.  

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Blockinfile Module*: Managing content blocks in files.  
- *Facts Gathering*: Using `ansible_architecture` with Jinja2.  
- *Service Management*: Starting and enabling services.  

**Ansible Features Used**:  
- `blockinfile` for file content.  
- `package`, `file`, `copy`, `service` modules.  
- `gather_facts` for system information.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/playbooks/inventory`.  
- *Playbook*: Created `/home/thor/playbooks/index.yml` to deploy `httpd` and configure files.  
- *Execution*: Ran successfully.  
- *Verification*: Confirmed file content and service status.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: `httpd` is running, and `index.html` contains the correct architecture info on all app servers.