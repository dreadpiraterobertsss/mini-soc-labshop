# Lab journal – Week 1

## 1. Two VMs had the same IP

- **Tried:** SSH to 10.10.10.10 to configure siem-host.
- **Failed:** the commands ran on a machine with a 40 GB disk. It was ubuntu-web, which also had 10.10.10.10 and the hostname "banu".
- **Fixed:** powered off ubuntu-web, cleared the Dell's ARP cache, moved ubuntu-web to 10.10.10.20 and renamed both hosts.
- **Lesson:** check `hostnamectl` and `lsblk` before running commands on a VM.

## 2. Network adapters swapped on siem-host

- **Tried:** ping 10.10.10.10 from the Dell.
- **Failed:** "Destination host unreachable".
- **Fixed:** in VMware, adapter 1 was NAT and adapter 2 was VMnet1. I swapped them so that ens33 is on VMnet1.
- **Lesson:** test each layer (VM → network → Docker → Wazuh) before moving on.

## 3. Disk smaller than allocated

- **Failed:** the Ubuntu installer used only part of the disk for the root logical volume.
- **Fixed:** `sudo lvextend -r -l +100%FREE /dev/ubuntu-vg/ubuntu-lv`.

## 4. Lost admin password

- **Failed:** I forgot the new dashboard password after W3.
- **Fixed:** repeated the documented procedure (new hash + securityadmin.sh, which authenticates with the admin certificate, not the password).
- **Lesson:** store passwords in a password manager immediately.

## 5. Clocks after removing NAT

- **Failed:** ubuntu-web showed `^-` / `^?` for 10.10.10.10 and was not synchronised.
- **Fixed:** made siem-host a chrony server (`allow 10.10.10.0/24`), disabled the internet sources on ubuntu-web and set `authselectmode ignore`, because Ubuntu's chrony prefers NTS-authenticated sources by default. win-client uses `w32tm` with 10.10.10.10.
- **Lesson:** an isolated lab still needs an internal time source, or alert timestamps drift.

## 6. Persistence test (W4)

- **Did:** `docker compose stop` / `start`, then rebooted siem-host.
- **Observed:** both agents were Active again within about 1 minute; alerts.json went from 615 to 616; dashboard hits did not decrease (before 609, after 616). After the reboot I did nothing manually: the containers have `restart: always` and Docker is enabled at boot. The win-client agent reconnected 25 seconds after siem-host booted.
- **Lesson:** Wazuh data lives in Docker volumes, so never use `docker compose down -v`.

## 7. False positives from Sysmon

- **Observed:** rule 92205 (level 9) "PowerShell process created an executable file in Windows root folder" right after installing Sysmon.
- **Explained:** the Sysmon installer copies itself into C:\Windows. The rule worked correctly; the action was benign.
- **Lesson:** a real SOC must triage alerts against what actually happened on the machine.

## 8. Notepad broke the Markdown files

- **Failed:** links and bold text did not render on GitHub.
- **Cause:** the Windows Notepad formatting mode escaped Markdown characters (`\[`, `\*`, `\_`).
- **Fixed:** replaced the files with clean copies and edited them with plain-text tools.
