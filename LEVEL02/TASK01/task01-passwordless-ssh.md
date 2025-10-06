# 🌟 **TASK01: Configure Password-less SSH for App Server 2**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* is preparing to test Ansible playbooks in *Stratos DC* and requires a password-less SSH connection between the **jump host** (Ansible controller) and managed nodes. This task focuses on setting up password-less SSH for **App Server 2** and testing connectivity using an Ansible ping.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Jump Host**: Use the jump host as the Ansible controller, with user `thor`.  
2. **Inventory File**: Use `/home/thor/ansible/inventory` to manage app servers.  
3. **Password-less SSH**: Configure password-less SSH for **App Server 2** (`stapp02`, IP: `172.16.238.11`, User: `steve`).  
4. **Ansible Ping**: Test connectivity to `stapp02` using `ansible stapp02 -i /home/thor/ansible/inventory -m ping`.  
5. **Verification**: Ensure SSH connects without a password prompt.

**Note**: The inventory file already exists and should be updated to remove the password for `stapp02` after setting up password-less SSH.

👉 **Your Mission**: Generate SSH keys, copy them to App Server 2, update the inventory, and verify connectivity with an Ansible ping.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`  
  - *App Server 2*: `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`)  
- **Working Directory**: `/home/thor/ansible`

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *SSH Key*: RSA key pair for password-less authentication.  
- *Inventory*: Defines app servers, with `stapp02` updated for key-based authentication.  
- *Ansible Ping*: Tests connectivity using the `ping` module.

### **Implementation Strategy**  
1. Generate an RSA key pair for `thor` on the jump host.  
2. Copy the public key to `stapp02` using `ssh-copy-id`.  
3. Update `/home/thor/ansible/inventory` to remove the password for `stapp02`.  
4. Test SSH connectivity manually.  
5. Run an Ansible ping to confirm.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **SSH Key Generation Failure**:  
   - *Issue*: Key generation fails due to existing keys.  
   - *Fix*: Remove or backup existing `~/.ssh/id_rsa` before generating.  
2. **SSH Copy Failure**:  
   - *Issue*: `ssh-copy-id` fails due to incorrect password or host key issues.  
   - *Fix*: Verify password (`Am3ric@`) and use `-o StrictHostKeyChecking=no`.  
3. **Inventory Misconfiguration**:  
   - *Issue*: Password remains in inventory for `stapp02`.  
   - *Fix*: Remove `ansible_ssh_pass` for `stapp02`.  
4. **Ping Failure**:  
   - *Issue*: Ansible ping fails due to SSH issues.  
   - *Fix*: Test manual SSH first: `ssh steve@172.16.238.11`.

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as `thor`.

### **Step 2: Generate SSH Key Pair**  
```bash
ssh-keygen -t rsa -N "" -f /home/thor/.ssh/id_rsa
```
*Purpose*: Create an RSA key pair without a passphrase for `thor`.

### **Step 3: Copy Public Key to App Server 2**  
```bash
sshpass -p Am3ric@ ssh-copy-id -i /home/thor/.ssh/id_rsa.pub -o StrictHostKeyChecking=no steve@172.16.238.11
```
*Purpose*: Copy `thor`’s public key to `stapp02` for password-less SSH.

### **Step 4: Update Inventory File**  
```bash
vi /home/thor/ansible/inventory
```
**Content**:
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Remove `ansible_ssh_pass` for `stapp02` to use key-based authentication.

### **Step 5: Test SSH Connectivity**  
In a new terminal:  
```bash
ssh steve@172.16.238.11
```
**Expected**: Connects without prompting for a password.  
*Purpose*: Confirm password-less SSH works.

### **Step 6: Test Ansible Ping**  
```bash
ansible stapp02 -i /home/thor/ansible/inventory -m ping
```
**Expected Output**:
```bash
stapp02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
*Purpose*: Verify Ansible connectivity to `stapp02` using the inventory.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/ansible/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hosts*: `stapp01`, `stapp02`, `stapp03`  
- *Details*: `stapp02` uses key-based authentication; others use passwords.  
*Purpose*: Defines app servers for Ansible management.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Inventory    | Hostname      | `stapp02` | App Server 2 alias |
| Inventory    | ansible_host  | `172.16.238.11` | App Server 2 IP |
| Inventory    | ansible_user  | `steve`   | App Server 2 user |
| SSH Key      | Path          | `/home/thor/.ssh/id_rsa` | RSA private key |
| SSH Key      | Public Key    | `/home/thor/.ssh/id_rsa.pub` | Copied to `stapp02` |

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
stapp02 ansible_host=172.16.238.11 ansible_user=steve
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

### **Step 2: Verify SSH Connectivity**  
```bash
ssh steve@172.16.238.11
```
**Expected**: Connects without password prompt.

### **Step 3: Verify Ansible Ping**  
```bash
ansible stapp02 -i /home/thor/ansible/inventory -m ping
```
**Expected Output**:
```bash
stapp02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Key Existence**  
```bash
ls -l /home/thor/.ssh/id_rsa /home/thor/.ssh/id_rsa.pub
```
*Purpose*: Confirm key pair exists.

### **Test Authorized Keys on App Server 2**  
```bash
ssh steve@172.16.238.11 'cat ~/.ssh/authorized_keys'
```
*Purpose*: Verify `thor`’s public key is present.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Generate SSH key
ssh-keygen -t rsa -N "" -f /home/thor/.ssh/id_rsa

# Copy public key to stapp02
sshpass -p Am3ric@ ssh-copy-id -i /home/thor/.ssh/id_rsa.pub -o StrictHostKeyChecking=no steve@172.16.238.11

# Edit inventory
vi /home/thor/ansible/inventory

# Test SSH
ssh steve@172.16.238.11

# Test Ansible ping
ansible stapp02 -i /home/thor/ansible/inventory -m ping
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: SSH Key Copy Failure**  
*Symptoms*: `ssh-copy-id` fails.  
*Solution*: Verify password (`Am3ric@`) and network connectivity.  
```bash
ping 172.16.238.11
sshpass -p Am3ric@ ssh steve@172.16.238.11
```

### **Issue 2: Ping Failure**  
*Symptoms*: `ansible ping` returns `unreachable`.  
*Solution*: Check inventory and SSH keys.  
```bash
cat /home/thor/ansible/inventory
ssh steve@172.16.238.11
```

### **Issue 3: Existing Key Conflict**  
*Symptoms*: `ssh-keygen` prompts to overwrite.  
*Solution*: Backup or remove existing keys.  
```bash
mv /home/thor/.ssh/id_rsa /home/thor/.ssh/id_rsa.bak
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys for all servers in production.  
- *Automation*: Script key distribution for multiple hosts.  
- *Verification*: Always test manual SSH before Ansible commands.  
- *Inventory Management*: Use groups for better organization.  

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Password-less SSH*: Setting up key-based authentication for one server.  
2. *Inventory Update*: Removing password for `stapp02` only.  
3. *Connectivity Verification*: Ensuring Ansible ping works.

### **Solution Approach**  
1. Generated SSH key pair for `thor`.  
2. Copied public key to `stapp02`.  
3. Updated inventory to remove `stapp02` password.  
4. Verified with SSH and Ansible ping.

### **Key Success Factors**  
- *SSH Keys*: Correctly generated and copied.  
- *Inventory*: Accurate update for `stapp02`.  
- *Verification*: Successful SSH and ping tests.

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Server*: `stapp02` (IP: `172.16.238.11`, User: `steve`)  
- *Command*: `ansible stapp02 -i /home/thor/ansible/inventory -m ping`

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Avoid passwords; extend SSH keys to all servers.  
- 📊 *Logging*: Log SSH key distribution for auditing.  
- 🛡️ *Backup*: Save original inventory before changes.  
- 🌐 *Scalability*: Apply to other servers for consistency.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *SSH Key Setup*: Generating and distributing SSH keys.  
- *Inventory Management*: Configuring password-less authentication.  
- *Ansible Ping*: Testing connectivity with the `ping` module.

**Ansible Features Used**:  
- `ping` module for connectivity testing.  
- Inventory configuration for SSH authentication.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *SSH Keys*: Generated and copied to `stapp02`.  
- *Inventory*: Updated to remove `stapp02` password.  
- *Verification*: Confirmed password-less SSH and Ansible ping.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Password-less SSH is configured for `stapp02`, and Ansible ping confirms connectivity.