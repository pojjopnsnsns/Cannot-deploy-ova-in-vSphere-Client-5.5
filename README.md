# 🚨 Fix: Unable to Deploy OVA on vSphere Client 5.5

![vmware](https://img.shields.io/badge/vSphere-5.5-blue?style=for-the-badge&logo=vmware)
![fortisiem](https://img.shields.io/badge/FortiSIEM-Guide-green?style=for-the-badge&logo=fortinet)

---

## ❌ Problem / Error

When deploying a `.ova` file, you may encounter the following error:

```
The OVF package is invalid and cannot be deployed.
The following manifest file entry (line 1) is invalid: SHA256 (xxxxxxx.ovf)
```

📷 Reference image:

<p align="center">
<img width="1219" height="603" alt="image" src="https://github.com/user-attachments/assets/40bebd46-3af5-4e9d-9b53-6ddb39db4ec6" />
</p>
  
---

## 🧩 Root Cause (Summary)

- The OVA uses SHA256 checksum, but **vSphere Client 5.5** only supports SHA1  
- Deployment fails due to checksum mismatch  

---

## 👉 Solution

- Extract `.ova` → Edit `.ovf` → Re-pack → Deploy again

---

## 🛠️ Step-by-step Fix

### 1️⃣ Extract `.ova` using 7-Zip

- Download 7-Zip: [https://www.7-zip.org/](https://www.7-zip.org/)  
- You’ll get 4 main files:  
  - 📄 `*.mf`  
  - 🔒 `*.cert`  
  - 📝 `*.ovf`  
  - 💽 `*-disk1.vmdk`

📷 Reference image:
<p align="center">
<img width="1068" height="227" alt="image" src="https://github.com/user-attachments/assets/71f5881f-d554-4f55-98be-b113fcbc386c" />
</p>

---

### 2️⃣ Edit `.ovf` (VM Compatibility)

- Open the `.ovf` file with Notepad or VSCode  
- Search for `vmx-15` → Replace with `vmx-8`  
- Save the file

```xml
<!-- Before -->
<vmw:vmx-15>...</vmw:vmx-15>

<!-- After -->
<vmw:vmx-8>...</vmw:vmx-8>
```

📷 Reference image
<p align="center">
<img width="1026" height="166" alt="image" src="https://github.com/user-attachments/assets/eb728c21-e4e1-454f-b1b5-51d18e165b12" />
</p>

---

### 3️⃣ Open Command Prompt and navigate to folder

```bat
cd C:\Users\nasak\Downloads\FSM_Full_All_ESX_7.3.2_build0374
dir
```

📷 Reference image:
<p align="center">
<img width="1293" height="342" alt="image" src="https://github.com/user-attachments/assets/3904a03d-9b60-4a96-8fde-362fd0bf4a36" />
</p>

---

### 4️⃣ Re-pack files into `.ova`

```bat
tar -cvf FortiSIEM-VA-7.3.2.0374.ova \
  FortiSIEM-VA-7.3.2.0374.ovf \
  FortiSIEM-VA-7.3.2.0374.mf \
  FortiSIEM-VA-7.3.2.0374.cert \
  FortiSIEM-VA-7.3.2.0374-disk1.vmdk
```

📷 Reference image: 
<p align="center">
<img width="1818" height="257" alt="image" src="https://github.com/user-attachments/assets/32c40fab-e3cc-414e-9dd8-fc75e63a692a" />
</p>

💡 **Note:**  
If your Windows system doesn’t support native `tar`, you can use **WSL**, **Git Bash**, or **7-Zip** to create a `.tar` archive and then rename the file extension to `.ova`.

📌 **Important:**  
When creating the `.tar` archive, make sure the `.ovf` file is listed **first** in the packing order. The `.ovf` must be the **first file** inside the archive, otherwise vSphere may fail to read the virtual machine descriptor correctly during deployment.

✅ Example (correct order):

```bash
tar -cvf FortiSIEM-VA.ova \
  FortiSIEM-VA.ovf \
  FortiSIEM-VA.mf \
  FortiSIEM-VA.cert \
  FortiSIEM-VA-disk1.vmdk
```

❌ Incorrect order (may cause deployment failure):

```bash
tar -cvf FortiSIEM-VA.ova \
  FortiSIEM-VA-disk1.vmdk \
  FortiSIEM-VA.ovf \
  FortiSIEM-VA.mf \
  FortiSIEM-VA.cert
```
---

### 5️⃣ Deploy `.ova` on vSphere 5.5

- Choose Disk Provision → ✅ Thick Provision Lazy Zeroed  
- Do not choose ❌ Thin Provision

🔗 Guide: Deploy OVA/OVF [InterWorks Blog](https://interworks.com/blog/ijahanshahi/2014/08/06/how-deploy-ova-ovf-template-using-vmware-vsphere-desktop-client/)

---

## ⚠️ Troubleshooting

### 📁 Disk Not Found
<p align="center">
<img width="1206" height="278" alt="image" src="https://github.com/user-attachments/assets/714b2283-7a6f-4482-8174-7f30c376555d" />
</p>

- Cause: `.vmdk` filename doesn’t match the reference in `.ovf`
- Example: You have `*-disk1-001.vmdk` but `.ovf` refers to `*-disk1.vmdk`
- Fix:

```bat
rename FortiSIEM-VA-7.3.2.0374-disk1-001.vmdk FortiSIEM-VA-7.3.2.0374-disk1.vmdk
```

Then repeat Step 4 to re-pack the `.ova`

> If you still encounter checksum/manifest issues → use [VMware OVF Tool](https://developer.vmware.com/tool/ovf/)

---

## 📚 References

- 7-Zip: [https://www.7-zip.org/](https://www.7-zip.org/)
- Deploy OVA/OVF Guide: [InterWorks Blog](https://interworks.com/blog/ijahanshahi/2014/08/06/how-deploy-ova-ovf-template-using-vmware-vsphere-desktop-client/)

---

## 📝 License

MIT — Free to use / modify / share 🎉
