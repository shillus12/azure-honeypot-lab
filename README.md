<h1>Azure Sentinel Honeypot Detection & Attack Mapping</h1>

<h2>Description</h2>

This project sets up a Windows 10 virtual machine in Azure as a honeypot to attract failed login attempts. The logs from these attempts are collected, enriched with location data based on IP addresses and visualized in Microsoft Sentinel using a custom attack map.

Core components include:
- Azure VM-based honeypot
- Centralized log collection with Log Analytics Workspace
- IP geolocation enrichment using a Sentinel Watchlist
- Visualization of attacker locations and activity in Sentinel

---

<h2>Technologies Used</h2>

- <b>Microsoft Azure</b>  
- <b>Microsoft Sentinel</b>  
- <b>Log Analytics Workspace</b>  
- <b>Windows 10</b>  
- <b>Kusto Query Language (KQL)</b>

---

<h2>Environments</h2>

- <b>Azure Portal</b>  
- <b>Windows Defender / Event Viewer</b>  
- <b>Microsoft Sentinel (SIEM)</b>

---

<h2>Implementation Overview</h2>

<p align="center">
<b>Unsecure Virtual Machine Deployment</b><br/>
A Windows 10 virtual machine was set up in Azure with open access to the internet to act as a honeypot. The loose firewall settings made it an easy target, attracting login attempts from bots and other attackers.
<br />
<img src="https://i.imgur.com/UZ7a3SF.png" height="80%" width="80%" alt="VM Deployment"/>
<br /><br />

<b>Brute Force Attempts Captured in Logs</b><br/>
The VM’s security logs showed repeated failed login attempts with random usernames, suggesting automated brute force attacks.
<br />
<img src="https://i.imgur.com/DQJViTl.png"/>
<br /><br />

<b>Querying Events in Log Analytics Workspace</b><br/>
Security events from the honeypot were forwarded to a centralized Log Analytics Workspace. This allows querying failed login for deeper analysis.
<br />
<img src="https://i.imgur.com/TPrdQtU.png" height="80%" width="80%" alt="Sentinel Setup"/>
<br /><br />

<b>Geolocation Enrichment</b><br/>
A GeoIP CSV dataset was imported into Microsoft Sentinel as a Watchlist. This allowed location data to be correlated with incoming attacker IPs.
<br />
<img src="https://i.imgur.com/nWp9xxl.png" height="80%" width="80%" alt="GeoIP Watchlist"/>
<br /><br />

<b>Enriched Query with Location Data</b><br/>
KQL was used to combine security events with geolocation data with `ipv4_lookup()`. This reveals the origin countries of failed login attempts.
<br />
<pre>
let GeoIPDB = _GetWatchlist("geoip");
SecurityEvent
| where EventID == 4625
| evaluate ipv4_lookup(GeoIPDB, IpAddress, network)
</pre>
<br />
<img src="https://i.imgur.com/f1FEpYv.png" height="80%" width="80%" alt="Enriched Logs"/>
<br /><br />

<b>Attack Map Visualization</b><br/>
A custom workbook in Microsoft Sentinel was built to visualize the geographic distribution of brute-force attempts. Allowing a real time visualization of attacks.
<br />
<img src="https://i.imgur.com/15RjUmb.png" height="80%" width="80%" alt="Attack Map"/>
</p>

---

<h2>Data Sources</h2>

- <b>SecurityEvent Logs (Event ID 4625)</b>  
- <b>GeoIP Watchlist</b>:  
  [geoip-summarized.csv](https://raw.githubusercontent.com/joshmadakor1/lognpacific-public/refs/heads/main/misc/geoip-summarized.csv)
