The primary and most versatile PowerShell cmdlet for replacing `telnet`, `netstat`, and `ping` is **`Test-NetConnection`**, which is part of the NetTCPIP module.

---

## 💻 PowerShell Alternatives

| Traditional Command | PowerShell Alternative | Description |
| :--- | :--- | :--- |
| **`telnet`** (port check) | **`Test-NetConnection -Port`** or **`New-Object System.Net.Sockets.TcpClient`** | **`Test-NetConnection`** can check ICMP connectivity and a specific TCP port. The `New-Object` method offers a more direct, low-level TCP connection check. |
| **`netstat`** | **`Get-NetTCPConnection`** and **`Get-NetUDPEndpoint`** | These two cmdlets provide the object-based network connection and endpoint information that `netstat` provides in text format. |
| **`ping`** | **`Test-NetConnection`** or **`Test-Connection`** | Both cmdlets send ICMP echo requests. `Test-NetConnection` is the modern, more powerful replacement, while `Test-Connection` is an older, similar cmdlet. |

---

## 🔍 Examples and Usage

### 1. Telnet Alternative (`Test-NetConnection -Port`)

`telnet` is primarily used to check if a specific **TCP port** on a remote host is open and reachable.

| Task | PowerShell Command |
| :--- | :--- |
| **Check connectivity to port 80** on a remote host (e.g., `www.google.com`). | `Test-NetConnection -ComputerName www.google.com -Port 80` |
| **Quiet check for port 3389 (RDP)**, returning only `True` or `False`. | `(Test-NetConnection -ComputerName MyServer -Port 3389).TcpTestSucceeded` |
| **Check a common port (HTTP)** and get a detailed response. | `Test-NetConnection -ComputerName WebServer01 -CommonTCPPort HTTP -InformationLevel Detailed` |

> 💡 **Note:** If `TcpTestSucceeded` is **True**, the port is open and reachable. If **False**, it's either closed or blocked by a firewall.

### 2. Netstat Alternatives (`Get-NetTCPConnection` / `Get-NetUDPEndpoint`)

`netstat` displays active network connections, routing tables, and interface statistics. PowerShell uses dedicated cmdlets that return structured objects, which can be easily filtered and manipulated.

| Task | PowerShell Command |
| :--- | :--- |
| **Display all active TCP connections** (similar to `netstat`). | `Get-NetTCPConnection` |
| **Display ports in the 'Listen' state** (what's listening). | `Get-NetTCPConnection -State Listen` |
| **Show established connections with the process name** (similar to `netstat -b`). | `Get-NetTCPConnection -State Established | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, @{N='ProcessName'; E={(Get-Process -Id $_.OwningProcess).ProcessName}}` |
| **Display all UDP endpoints** (needed because TCP and UDP are separate). | `Get-NetUDPEndpoint` |

### 3. Ping Alternatives (`Test-NetConnection` / `Test-Connection`)

| Task | PowerShell Command |
| :--- | :--- |
| **Basic ICMP ping test** to a host. | `Test-NetConnection -ComputerName 8.8.8.8` |
| **Ping with a specific count** (e.g., 5 times, similar to `ping -n 5`). | `Test-Connection -TargetName www.microsoft.com -Count 5` |
| **Perform a traceroute** (similar to `tracert`). | `Test-NetConnection -ComputerName ServerName -TraceRoute` |

Would you like to see how to wrap one of these commands into a simple function to make it feel even more like the classic command-line tools?

Sure! PowerShell has several built-in cmdlets that replace or extend what **nslookup** does—often with much more structured output.

Here are the best **PowerShell alternatives to nslookup**:

---

# ✅ **1. Resolve-DnsName** (The direct replacement)

This is the *modern* and recommended cmdlet.

### Example – simple A record lookup

```powershell
Resolve-DnsName google.com
```

### Example – specific record type

```powershell
Resolve-DnsName google.com -Type MX
```

### Example – query a specific DNS server

```powershell
Resolve-DnsName microsoft.com -Server 1.1.1.1
```

### Example – full detailed output

```powershell
Resolve-DnsName cloudflare.com -DnsOnly
```

---

# ✅ **2. Test-NetConnection** (Quick checks)

Useful to check DNS AND connectivity.

### Example – DNS lookup + port test

```powershell
Test-NetConnection google.com
```

### Example – test DNS specifically

```powershell
Test-NetConnection -ComputerName google.com -Port 53
```

---

# ✅ **3. System.Net.Dns .NET class** (Fast & simple)

Good for quick scripts.

### Example – basic lookup

```powershell
[System.Net.Dns]::GetHostAddresses("google.com")
```

### Example – reverse lookup

```powershell
[System.Net.Dns]::GetHostEntry("8.8.8.8")
```

---

# ✅ **4. Using `nslookup` inside PowerShell**

Just in case you still need it:

```powershell
nslookup google.com
```

Runs the classic tool.


