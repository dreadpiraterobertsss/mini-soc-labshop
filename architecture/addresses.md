# Address table

Host-only lab network: 10.10.10.0/24 (VMware VMnet1, DHCP off).
NAT network: VMware VMnet8, 192.168.121.0/24 (siem-host only).

| Name | IP | RAM | vCPU | Disk | Adapters | OS |
|---|---|---|---|---|---|---|
| Dell (host) | 10.10.10.1 | – | – | – | VMnet1 host adapter | Windows + VMware Workstation Pro |
| siem-host | 10.10.10.10 | 16 GB | 6 | 250 GB | VMnet1 + NAT | Ubuntu Server 26.04.1 LTS |
| ubuntu-web | 10.10.10.20 | 2 GB | 2 | 40 GB | VMnet1 only | Ubuntu Server 26.04.1 LTS |
| win-client | 10.10.10.30 | 4 GB | 2 | 80 GB | VMnet1 only | Windows 10 Pro 22H2 |

## Ports on siem-host

| Port | Purpose |
|---|---|
| 1514/tcp | Agent events |
| 1515/tcp | Agent enrolment |
| 55000/tcp | Wazuh API |
| 443/tcp | Dashboard (HTTPS) |
| 9200/tcp | Indexer (used by the manager's Filebeat) |
| 123/udp | NTP for the lab (chrony) |
