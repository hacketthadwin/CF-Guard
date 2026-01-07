# ExamProctor - Secure Examination Environment System

[![.NET](https://img.shields.io/badge/.NET-10.0-512BD4?logo=dotnet)](https://dotnet.microsoft.com/)
[![Platform](https://img.shields.io/badge/Platform-Windows%2010%2F11-0078D4?logo=windows)](https://www.microsoft.com/windows)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-active-success.svg)](#)

A comprehensive network and application blocking system for secure examination environments. Permits network access exclusively to Codeforces.com while preventing unauthorized application execution during competitive programming assessments.

---

## 🎯 What Makes This Different?

- **OS-Level Security** - Leverages Windows infrastructure without browser plugins or video surveillance
- **User-Mode Implementation** - No expensive EV certificates or kernel drivers required
- **Three-Layer Defense** - Enhanced process verification prevents executable renaming bypasses
- **Dynamic IP Management** - Automatic CDN rotation handling with periodic DNS updates
- **Detection Systems** - Identifies VPN connections and virtual machine environments

---

## ✨ Key Features

### 🔒 Application Blocking (Process Watchdog)

**Three-Layer Defense Against Masquerading:**
- **PID Blacklist** - Tracks terminated processes, prevents race conditions
- **Process Refresh** - Eliminates stale cached data
- **PE Header Verification** - Validates true executable identity

Detects renamed executables (e.g., `cmd.exe` → `msedge.exe`) by reading `OriginalFilename` from Windows PE metadata. Scans every 2 seconds, terminates unauthorized apps including child processes.

### 🌐 Network Filtering (Firewall Manager)

**IP-Based Whitelisting:**
- **Default-Deny Policy** - Blocks all traffic except whitelisted IPs
- **Dynamic DNS Resolution** - Updates Codeforces IPs every 3 minutes
- **CDN Support** - Handles Cloudflare IP rotation automatically
- **Multi-Domain** - Main site, mirrors, static servers, CDN infrastructure

Only HTTP/HTTPS (ports 80/443) allowed to Codeforces IP addresses.

### 🛡️ VPN Detection

**Real-Time Monitoring:**
- Network adapter analysis (TAP/TUN interfaces)
- VPN service detection (OpenVPN, WireGuard, NordVPN, etc.)
- Process scanning for VPN executables

Scans every 2 seconds. Detections logged to Event Viewer for audit.

### 🖥️ Virtual Machine Detection

**Environment Analysis:**
- BIOS manufacturer strings (VirtualBox, VMware, Hyper-V)
- Guest addition processes (vboxservice, vmtoolsd)
- MAC address vendor identification
- System property validation

Runs once at startup. Detections logged to Event Viewer.

### 🔄 Inter-Process Communication

**Two-Pipe Architecture:**
- Separate command/response channels prevent deadlocks
- Named Pipes with access control for Session 0
- Asynchronous command processing
- Simple protocol: `ENTER`, `EXIT`, `STATUS`, `REFRESH`

---

## 🚀 Quick Start

### Prerequisites

- Windows 10 (1809+) or Windows 11 (x64)
- .NET 10.0 Runtime ([Download](https://dotnet.microsoft.com/download))
- Administrator privileges
- **⚠️ Test in Virtual Machine first**

### Usage

1. Navigate to `ExamProctor_2` folder
2. Right-click `ProctorLauncher.exe` → **Run as administrator**
3. Click **"Start Exam"** to activate
4. Click **"Stop Exam"** to deactivate

**During Exam Mode:**
- ✅ Codeforces.com accessible
- ❌ Other websites blocked
- ❌ Unauthorized apps terminated within 2 seconds
- 📋 VPN/VM detections logged

---

## 📂 Project Structure

```
ExamProctor/
├── ExamProctor_2/           # Main application folder
│   └── ProctorLauncher.exe  # Launch this (as admin)
├── ProctorAppGUI/           # GUI source
├── ProctorLauncher/         # Launcher source
├── ProctorService/          # Windows Service source
│   ├── ProcessWatchdog.cs   # App blocking (3-layer)
│   ├── FirewallManager.cs   # Network filtering
│   ├── DynamicIPResolver.cs # DNS updates
│   ├── VPNDetector.cs       # VPN detection
│   └── VMDetector.cs        # VM detection
└── ProctoringSolution.slnx  # VS solution
```

---

## 🛠️ System Architecture

```
ProctorLauncher.exe (GUI)
        ↓ Named Pipes IPC
ProctorService.exe (Windows Service)
    ├── Process Watchdog (3-layer, 2-sec scans)
    ├── Firewall Manager (IP whitelist)
    ├── IP Resolver (3-min DNS updates)
    ├── VPN Detector (2-sec scans)
    └── VM Detector (startup check)
```

---

## ⚙️ Technical Stack

- **.NET 10.0** / C# 10.0
- Windows Service (Session 0, LocalSystem)
- Windows Filtering Platform APIs
- Named Pipes for IPC
- WMI for detection
- FileVersionInfo for PE headers

**Scan Intervals:**
- Process watchdog: 2 seconds
- VPN detection: 2 seconds
- IP resolution: 3 minutes
- VM detection: Once at startup

---

## ⚠️ Important Notes

### Testing Warning

**🔴 Always test in VM first** (VirtualBox, VMware, Hyper-V)

System modifies:
- Windows Firewall (default-deny)
- Process management
- Network configuration

### Current Behavior

- **VPN/VM Detection:** Logged only (not blocked)
- **Service:** Can be stopped by administrators
- **Internet:** Required for DNS resolution

---

## 🤝 Contributing

Contributions welcome for:
- Process whitelist improvements
- Detection enhancements
- Codeforces/Cloudflare issues
- Documentation

---

<div align="center">

**⭐ Star this repo if you find it useful!**

[Report Bug](../../issues) · [Request Feature](../../issues)

</div>
