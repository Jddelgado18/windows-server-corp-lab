# windows-server-corp-lab
Active Directory Lab Practice to show knowledgeable use of Windows Server Active Directory.
This repository will demonstrate my knowledge and continued practice with Active Directory including scrreenshot of VM setup with VirtualBox, AD promotion, Users and Computer console.
Steps followed and Lessons learned. 
## Lab Environment - VirtualBox Setup

### Virtual Machine Configuration
- **Hypervisor**: Oracle VirtualBox
- **Guest OS**: Windows Server 2022 (Evaluation)
- **RAM**: 8 GB
- **CPU**: 4 cores
- **Storage**: Dynamic VDI disk (60-80 GB recommended)
- **Network**: NAT (or Host-only for better lab networking)
- **Guest Additions**: Installed (for auto-resize + better integration)

### Installation Steps
1. Downloaded official `SERVER_EVAL_x64FRE_en-us.iso` from Microsoft Evaluation Center
2. Created new VM with and attached ISO to the disk drive. 
3. Removed Floppy controller (common fix for license terms error)
4. Installed Windows Server 2022 successfully
5. Installed VirtualBox Guest Additions
6. Configured static IP: `192.168.56.10`

### Screenshots
![Windows Server 2022 VM in VirtualBox]
<img width="1839" height="1025" alt="image" src="https://github.com/user-attachments/assets/a2962891-11ec-4db4-88a4-9c22233a324e" />
<img width="1189" height="758" alt="Screenshot 2026-07-01 090817" src="https://github.com/user-attachments/assets/d3c68a66-a9af-4403-9392-d5fbe2ed0981" />
<img width="1992" height="959" alt="Screenshot 2026-07-01 105651" src="https://github.com/user-attachments/assets/f9228430-c1f9-4abd-9bf5-f9d41da0491f" />


![Guest Additions Installed](images/guest-additions.png)

### Lessons Learned
- Always verify ISO hash
- Remove virtual floppy drive for Server installs
- Guest Additions are essential for usability


