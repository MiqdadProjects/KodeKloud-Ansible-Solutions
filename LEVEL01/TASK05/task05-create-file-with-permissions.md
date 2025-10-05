# 🌟 **TASK05: Create File with Permissions and Ownership on App Servers**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* is testing **Ansible modules** for file management on servers in *Stratos DC*. The focus is on creating a file with specific permissions and ownership on all application servers using Ansible. The task requires setting up an inventory and playbook to automate this process.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Create `~/playbook/inventory` on the jump host, including all app servers:  
   - *App Server 1*: `stapp01`, IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`  
   - *App Server 2*: `stapp02`, IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`  
   - *App Server 3*: `stapp03`, IP: `172.16.238.12`, User: `banner`, Password: `BigGr33n`  
2. **Playbook**: Create `~/playbook/playbook.yml` to:  
   - Create a blank file `/usr/src/app.txt` on all app servers.  
   - Set permissions to `0777` (read, write, execute for all).  
   - Set ownership to:  
     - `tony:tony` on *App Server 1*  
     - `steve:steve` on *App Server 2*  
     - `banner:banner` on *App Server 3*  
3. **Execution**: Ensure the playbook runs successfully using:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```  
4. **Verification**: Confirm `/usr/src/app.txt` exists on all app servers with correct permissions (`-rwxrwxrwx`) and ownership.  

**Note**: The `ansible` command is pre-configured on the jump host. No additional arguments are required beyond the specified command.

👉 **Your Mission**: Create the inventory, write the playbook to create and configure the file, execute it, and verify the file’s permissions and ownership on all app servers.

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
  - *Inventory File*: `~/playbook/inventory`  
  - *Playbook File*: `~/playbook/playbook.yml`  
  - *Target File*: `/usr/src/app.txt`  
- **Working Directory**: `~/playbook`

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines all app servers with their SSH credentials.  
- *Playbook*: Uses the `file` module to create a blank file, set permissions, and configure ownership conditionally.  
- *Application Servers*: Three servers (`stapp01`, `stapp02`, `stapp03`) receiving the file with specific ownership.  

### **Implementation Strategy**  
1. Create `~/playbook/inventory` with details for all app servers.  
2. Create `~/playbook/playbook.yml` to:  
   - Create `/usr/src/app.txt` with `0777` permissions.  
   - Set ownership conditionally based on `inventory_hostname`.  
3. Run the playbook using the specified command.  
4. Verify file existence, permissions, and ownership on each server via SSH.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Inventory Misconfiguration**:  
   - *Issue*: Incorrect hostnames, IPs, users, or passwords.  
   - *Fix*: Verify details for `stapp01`, `stapp02`, `stapp03`.  
2. **Permission Denied**:  
   - *Issue*: Cannot create `/usr/src/app.txt` without root access.  
   - *Fix*: Ensure `become: yes` in playbook.  
3. **Ownership Errors**:  
   - *Issue*: Users `tony`, `steve`, or `banner` don’t exist or `when` conditions fail.  
   - *Fix*: Validate user existence and `inventory_hostname` conditions.  
4. **Incorrect Permissions**:  
   - *Issue*: Permissions not set to `0777`.  
   - *Fix*: Use `mode: '0777'` in `file` module.  

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
cd ~/playbook
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
*Purpose*: Define all app servers with their SSH credentials.

### **Step 4: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Create /usr/src/app.txt on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Create /usr/src/app.txt with 0777 permissions
      file:
        path: /usr/src/app.txt
        state: touch
        mode: '0777'
    - name: Set ownership to tony on stapp01
      file:
        path: /usr/src/app.txt
        owner: tony
        group: tony
      when: inventory_hostname == "stapp01"
    - name: Set ownership to steve on stapp02
      file:
        path: /usr/src/app.txt
        owner: steve
        group: steve
      when: inventory_hostname == "stapp02"
    - name: Set ownership to banner on stapp03
      file:
        path: /usr/src/app.txt
        owner: banner
        group: banner
      when: inventory_hostname == "stapp03"
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to generate the file, set permissions, and assign ownership.

### **Step 5: Run the Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Create /usr/src/app.txt on all app servers] ******************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Create /usr/src/app.txt with 0777 permissions] **************************
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
*Purpose*: Execute the playbook to create and configure the file.

### **Step 6: Verify File on Each App Server**  
For each server, open a new terminal and SSH:  
- **App Server 1**:  
  ```bash
  ssh tony@stapp01
  ```
  *Password*: `Ir0nM@n`  
  ```bash
  ls -la /usr/src
  ```
  **Expected Output**:
  ```bash
  total 16
  drwxr-xr-x 1 root  root  4096 Oct  5 20:42 .
  drwxr-xr-x 1 root  root  4096 Jul 15 2024 ..
  -rwxrwxrwx 1 tony  tony     0 Oct  5 20:42 app.txt
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 debug
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 kernels
  ```

- **App Server 2**:  
  ```bash
  ssh steve@stapp02
  ```
  *Password*: `Am3ric@`  
  ```bash
  ls -la /usr/src
  ```
  **Expected Output**:
  ```bash
  total 16
  drwxr-xr-x 1 root  root  4096 Oct  5 20:42 .
  drwxr-xr-x 1 root  root  4096 Jul 15 2024 ..
  -rwxrwxrwx 1 steve steve    0 Oct  5 20:42 app.txt
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 debug
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 kernels
  ```

- **App Server 3**:  
  ```bash
  ssh banner@stapp03
  ```
  *Password*: `BigGr33n`  
  ```bash
  ls -la /usr/src
  ```
  **Expected Output**:
  ```bash
  total 16
  drwxr-xr-x 1 root  root  4096 Oct  5 20:42 .
  drwxr-xr-x 1 root  root  4096 Jul 15 2024 ..
  -rwxrwxrwx 1 banner banner  0 Oct  5 20:42 app.txt
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 debug
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 kernels
  ```
*Purpose*: Confirm `/usr/src/app.txt` exists with permissions `-rwxrwxrwx` and correct ownership.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`~/playbook/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hosts*: `stapp01`, `stapp02`, `stapp03`  
- *Details*: IP addresses, usernames, and passwords for SSH access.  
*Purpose*: Defines all app servers for Ansible management.

### **Playbook** (`~/playbook/playbook.yml`)  
```yaml
---
- name: Create /usr/src/app.txt on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Create /usr/src/app.txt with 0777 permissions
      file:
        path: /usr/src/app.txt
        state: touch
        mode: '0777'
    - name: Set ownership to tony on stapp01
      file:
        path: /usr/src/app.txt
        owner: tony
        group: tony
      when: inventory_hostname == "stapp01"
    - name: Set ownership to steve on stapp02
      file:
        path: /usr/src/app.txt
        owner: steve
        group: steve
      when: inventory_hostname == "stapp02"
    - name: Set ownership to banner on stapp03
      file:
        path: /usr/src/app.txt
        owner: banner
        group: banner
      when: inventory_hostname == "stapp03"
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: `all` (targets all servers in inventory).  
- *Privilege Escalation*: `become: yes` for root access.  
- *Tasks*:  
  - *Create File*: Uses `file` module with `state: touch` and `mode: '0777'`.  
  - *Set Ownership*: Uses `file` module with conditional `when` clauses.  
*Purpose*: Creates and configures `/usr/src/app.txt` with specific permissions and ownership.

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
| Playbook     | Task Module   | `file`    | Manages files |
| Playbook     | File Path     | `/usr/src/app.txt` | Target file |
| Playbook     | Permissions   | `0777`    | Read, write, execute for all |
| Playbook     | Ownership (stapp01) | `tony:tony` | User and group |
| Playbook     | Ownership (stapp02) | `steve:steve` | User and group |
| Playbook     | Ownership (stapp03) | `banner:banner` | User and group |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat ~/playbook/inventory
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
PLAY [Create /usr/src/app.txt on all app servers] ******************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [Create /usr/src/app.txt with 0777 permissions] **************************
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

### **Step 3: Verify File on Each Server**  
- **App Server 1**:  
  ```bash
  ssh tony@stapp01
  ```
  *Password*: `Ir0nM@n`  
  ```bash
  ls -la /usr/src
  ```
  **Expected Output**:
  ```bash
  total 16
  drwxr-xr-x 1 root  root  4096 Oct  5 20:42 .
  drwxr-xr-x 1 root  root  4096 Jul 15 2024 ..
  -rwxrwxrwx 1 tony  tony     0 Oct  5 20:42 app.txt
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 debug
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 kernels
  ```

- **App Server 2**:  
  ```bash
  ssh steve@stapp02
  ```
  *Password*: `Am3ric@`  
  ```bash
  ls -la /usr/src
  ```
  **Expected Output**:
  ```bash
  total 16
  drwxr-xr-x 1 root  root  4096 Oct  5 20:42 .
  drwxr-xr-x 1 root  root  4096 Jul 15 2024 ..
  -rwxrwxrwx 1 steve steve    0 Oct  5 20:42 app.txt
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 debug
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 kernels
  ```

- **App Server 3**:  
  ```bash
  ssh banner@stapp03
  ```
  *Password*: `BigGr33n`  
  ```bash
  ls -la /usr/src
  ```
  **Expected Output**:
  ```bash
  total 16
  drwxr-xr-x 1 root  root  4096 Oct  5 20:42 .
  drwxr-xr-x 1 root  root  4096 Jul 15 2024 ..
  -rwxrwxrwx 1 banner banner  0 Oct  5 20:42 app.txt
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 debug
  drwxr-xr-x 1 root  root  4096 Jun 25 2024 kernels
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
*Purpose*: Simulate file creation and configuration.

### **Test User Existence**  
On each server:  
```bash
ssh <user>@<server>
id <user>
```
*Purpose*: Confirm users `tony`, `steve`, and `banner` exist.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd ~/playbook

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
ls -la /usr/src

ssh steve@stapp02
ls -la /usr/src

ssh banner@stapp03
ls -la /usr/src
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Playbook Fails to Connect**  
*Symptoms*: `unreachable=1` in output.  
*Solution*: Verify inventory and SSH connectivity.  
```bash
cat ~/playbook/inventory
ssh tony@172.16.238.10
ssh steve@172.16.238.11
ssh banner@172.16.238.12
```

### **Issue 2: File Not Created**  
*Symptoms*: `/usr/src/app.txt` missing.  
*Solution*: Check `become: yes` and directory permissions.  
```bash
ansible-playbook -i inventory playbook.yml -v
ssh <user>@<server>
ls -ld /usr/src
```

### **Issue 3: Incorrect Ownership**  
*Symptoms*: Wrong user or group on `/usr/src/app.txt`.  
*Solution*: Validate `when` conditions and user existence.  
```bash
ansible-playbook -i inventory playbook.yml -v
ssh <user>@<server>
id <user>
```

### **Issue 4: Incorrect Permissions**  
*Symptoms*: Permissions not `-rwxrwxrwx`.  
*Solution*: Check `mode: '0777'` in playbook.  
```bash
cat ~/playbook/playbook.yml
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Avoid plain-text passwords; use Ansible Vault or SSH keys.  
- *Idempotency*: `file` module with `state: touch` and `mode` ensures safe re-runs.  
- *Verbose Output*: Use `-v` for detailed logs.  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Optimization*: Combine file creation and ownership in a single task per host if preferred.

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Multiple Hosts*: Managing three servers with unique credentials.  
2. *Conditional Ownership*: Setting different owners per server.  
3. *Permissions*: Ensuring `0777` across all servers.  
4. *Privilege Escalation*: Requiring root access for `/usr/src`.

### **Solution Approach**  
1. Created inventory with all app servers’ details.  
2. Wrote playbook with `file` module and conditional `when` clauses.  
3. Used `become: yes` for root access.  
4. Verified file, permissions, and ownership via SSH.

### **Key Success Factors**  
- *Inventory*: Accurate credentials for all servers.  
- *Playbook*: Correct `file` module usage with conditions.  
- *Execution*: No extra arguments needed.  
- *Verification*: Confirmed file properties on all servers.

### **Critical Details**  
- *Inventory*: Three servers with unique users and passwords  
- *Playbook*: Creates `/usr/src/app.txt` with `0777` and specific ownership  
- *Command*: `ansible-playbook -i inventory playbook.yml`  
- *File Properties*: `-rwxrwxrwx`, owned by `tony:tony`, `steve:steve`, `banner:banner`

### **Ansible Benefits**  
- *Automation*: Simplifies file creation and configuration.  
- *Idempotency*: Safe for repeated runs.  
- *Flexibility*: Conditional tasks for per-host customization.  
- *Scalability*: Handles multiple servers efficiently.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use SSH keys or Ansible Vault instead of `ansible_ssh_pass`.  
- 📊 *Logging*: Enable Ansible logging for auditing.  
- 🛡️ *Permissions*: Avoid `0777` in production; use restrictive permissions.  
- 🌐 *Scalability*: Extend to additional servers or files as needed.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Inventory Management*: Configuring multiple hosts with credentials.  
- *Playbook Creation*: Using `file` module with permissions and ownership.  
- *Conditional Tasks*: Implementing `when` for host-specific actions.  
- *Verification*: Checking file attributes across servers.

**Ansible Features Used**:  
- `file` module for file creation and configuration.  
- `when` conditionals for host-specific tasks.  
- Inventory with multiple hosts.  
- `become` for privilege escalation.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Created `~/playbook/inventory` with all app servers.  
- *Playbook*: Created `~/playbook/playbook.yml` to configure `/usr/src/app.txt`.  
- *Execution*: Ran successfully with no extra arguments.  
- *Verification*: Confirmed file, permissions, and ownership on all servers.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: The file `/usr/src/app.txt` is created on all app servers with `-rwxrwxrwx` permissions and correct ownership, ready for validation.

### **Next Steps**  
- *Testing*: Validate idempotency with multiple runs.  
- *Security*: Replace passwords with SSH keys or Ansible Vault.  
- *Enhancement*: Combine tasks for efficiency if needed.  
- *Documentation*: Add comments to playbook for clarity.  
- *Automation*: Integrate into CI/CD pipeline.