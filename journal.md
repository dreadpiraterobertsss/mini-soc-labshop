# \# Lab journal – Week 1

# 

# \## 1. Two VMs had the same IP

# \*\*Tried:\*\* SSH to 10.10.10.10 for siem-host.

# \*\*Failed:\*\* commands ran on a 40 GB disk machine – it was ubuntu-web, which also had 10.10.10.10 and hostname "banu".

# \*\*Fixed:\*\* powered off ubuntu-web, cleared the Dell's ARP cache, set ubuntu-web to 10.10.10.20 and renamed both hosts.

# \*\*Lesson:\*\* check `hostnamectl` and `lsblk` before running commands.

# 

# \## 2. Network adapters swapped on siem-host

# \*\*Tried:\*\* ping 10.10.10.10 from the Dell.

# \*\*Failed:\*\* "Destination host unreachable".

# \*\*Fixed:\*\* in VMware, adapter 1 was NAT and adapter 2 VMnet1. Swapped them so ens33 = VMnet1.

# \*\*Lesson:\*\* test each layer (VM → network → Docker → Wazuh) before moving on.

# 

# \## 3. Disk smaller than allocated

# \*\*Failed:\*\* the Ubuntu installer used only part of the disk for the root LV.

# \*\*Fixed:\*\* `lvextend -r -l +100%FREE /dev/ubuntu-vg/ubuntu-lv`.

# 

# \## 4. Lost admin password

# \*\*Failed:\*\* forgot the new dashboard password after W3.

# \*\*Fixed:\*\* repeated the documented procedure (new hash + securityadmin.sh, which authenticates with the admin certificate, not the password).

# \*\*Lesson:\*\* store passwords in a password manager immediately.

# 

# \## 5. Clocks after removing NAT

# \*\*Failed:\*\* ubuntu-web showed `^-` / `^?` for 10.10.10.10 and was not synchronised.

# \*\*Fixed:\*\* made siem-host a chrony server (`allow 10.10.10.0/24`), disabled internet sources on ubuntu-web and set `authselectmode ignore` (Ubuntu's chrony requires NTS-authenticated sources by default). win-client uses `w32tm` with 10.10.10.10.

# 

# \## 6. Persistence test (W4)

# \*\*Did:\*\* `docker compose stop` / `start`, then rebooted siem-host.

# \*\*Observed:\*\* both agents Active again within \~1 minute; alerts.json went 615 → 616; dashboard hits did not decrease (before \_\_\_, after \_\_\_). After the reboot I did nothing manually: containers have `restart: always` and Docker is enabled at boot.

# \*\*Lesson:\*\* data lives in Docker volumes – never use `docker compose down -v`.

# 

# \## 7. False positives from Sysmon

# \*\*Observed:\*\* rule 92205 (level 9) "PowerShell created an executable in Windows root folder" right after installing Sysmon.

# \*\*Explained:\*\* Sysmon's installer copies itself into C:\\Windows – the rule worked, the action was benign.

