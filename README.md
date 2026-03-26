# MikroTik RouterOS Scripts

Collection of scripts and configurations for **MikroTik RouterOS Version 7.x**

> **⚠️ IMPORTANT:** These scripts have been tested and developed for RouterOS version 7.x. Compatibility with other versions is not guaranteed.

## 📋 Overview

This repository contains scripts and configurations for MikroTik RouterOS designed to simplify common networking tasks such as backups, BGP monitoring, and advanced firewall rules for edge routers.

All scripts are based on:
- Official MikroTik documentation
- Community best practices
- ISP and network operator guidelines

## 📁 Repository Contents

### 1. **mikrotik-backup**
Automated backup script for RouterOS configurations and system state.

#### Features:
- 🔄 System backup generation (.backup file)
- 📝 Configuration export (.rsc file)
- ☁️ Cloud backup upload to MikroTik cloud storage
- 📅 Automatic timestamp naming (YYYY-MM-DD-HH:MM:SS format)
- 📊 Detailed logging of backup process

#### Key Variables:
- `cloudpwd` - Strong password for cloud backup encryption
- `cloudbkpname` - Cloud backup file name

#### Functionality:
1. Generates system backup file with timestamp
2. Exports router configuration as script file
3. Uploads backup to MikroTik cloud storage
4. Logs each backup step for monitoring

**Usage:** Schedule this script as a scheduled task in RouterOS for regular backups.

---

### 2. **mikrotik-bgp-monit-v7**
BGP Peer monitoring and email alerting system.

> **Adapted from:** Cha0s's original RouterOS 6 BGP monitoring script, updated for v7

#### Features:
- 👁️ Real-time BGP peer status monitoring
- 📧 Email alerts on peer state changes
- ⚡ Automatic down/up notifications
- 🔄 Global state tracking using arrays
- 🐛 Debug logging support

#### Key Variables:
- `arrEmails` - Array of email addresses for alert recipients
- `debug` - Boolean flag for enabling debug logging
- `arrBgpState` - Global array storing peer states
- `hostNameX` - Router hostname for email context

#### Monitoring Logic:
1. Initializes global state tracking array
2. Iterates through all configured BGP peers
3. Checks if peer is enabled and retrieves connection status
4. Detects peer state changes (down/up)
5. Sends email alerts to configured recipients
6. Updates global state array

**Usage:** Schedule as a periodic task (e.g., every 5-10 minutes) to monitor BGP peer health.

---

### 3. **mikrotik-firewall-edge-router**
Comprehensive firewall configuration for BGP edge routers and ISP environments.

#### Features:
- 🛡️ RAW firewall rules for edge routers
- 📍 RFC6890 compliance (bad source/destination filtering)
- 🌐 BGP peer protection rules
- ⚙️ Interface lists (WAN/LAN management)
- 📋 Address lists for whitelist, blackhole routes
- 🔐 Management IP restrictions
- 🛑 Connection tracking optimization
- 🚫 Service port hardening

#### Configuration Sections:

**Service Hardening:**
- Disables unused services (FTP, TFTP, IRC, H.323, SIP, PPTP)
- Disables connection tracking for performance

**Interface Lists:**
- `WAN` - Interfaces connected to ISPs/IXPs
- `LAN` - Internal/local interfaces

**Address Lists:**
- `lan_subnets` - Internal network ranges
- `not_in_internet` - RFC6890 private ranges
- `bad_ipv4` - Strict source filtering
- `bad_src_ipv4` / `bad_dst_ipv4` - Directional filters
- `whitelist` - Trusted external services (DNS, NTP, RouterOS updates)
- `router_addresses` - IPs assigned to the router
- `bgp_networks_v4` - BGP-advertised networks
- `management` - Allowed IPs for remote management

**Firewall Rules:**
- BGP TCP port 179 protection
- ICMP ping allowance for diagnostics
- Whitelist-based external service access
- RFC6890 bad address filtering
- TCP flag validation
- Port 0 protocol filtering
- Local subnet source validation
- Interface-based traffic policies

**Blackhole Routes:**
- RFC6890 aggregate routes
- Local network blackhole routing
- CPU optimization through immediate route termination

#### Customization Points:
```
# Edit these sections for your environment:
- Interface names (PEER-1, PEER-2, LAN-1, LAN-2)
- IP addresses (subnets, BGP networks, management IPs)
- Whitelist entries (DNS, NTP servers)
- Router interface IPs
- BGP networks being advertised
```

**Usage:** Import this configuration into your edge router and customize IP addresses, interfaces, and whitelists for your specific network.

---

## 🚀 Getting Started

### Prerequisites
- MikroTik device with RouterOS 7.x installed
- SSH or WinBox access to the device
- Email configuration (for BGP monitoring script)
- Network interfaces properly configured

### Installation

1. **For Script Files:**
   - Copy script content
   - Navigate to **System → Scripts** in WinBox
   - Create new script and paste content
   - Configure required variables (cloudpwd, email addresses, etc.)
   - Save script

2. **For Firewall Configuration:**
   - Open Terminal or connect via SSH
   - Copy and paste the firewall rules
   - **IMPORTANT:** Customize IP addresses and interfaces before applying
   - Test connectivity before committing to production

3. **Scheduling Scripts:**
   - Navigate to **System → Scheduler**
   - Create new scheduled task
   - Select your script
   - Set interval (backup: daily; BGP monitor: every 5-10 minutes)

### Configuration Checklist

- [ ] RouterOS version is 7.x
- [ ] All interface names match your topology
- [ ] IP addresses updated for your network
- [ ] Email configuration tested (for BGP script)
- [ ] Cloud backup credentials set (for backup script)
- [ ] Whitelist IPs customized for your services
- [ ] Management IP whitelist updated
- [ ] BGP networks properly documented in firewall rules

---

## 📝 Examples

### Running Backup Script
```
:local cloudpwd "YourVeryStrongPassword"
:local cloudbkpname "production-router-backup"
# Script executes and logs progress
```

### Monitoring BGP Peers
```
:local arrEmails {"admin@example.com"; "network-team@example.com"}
:local debug true
# Script monitors all BGP peers and sends alerts
```

### Applying Firewall Rules
```
# SSH into router or use Terminal in WinBox
# Paste firewall configuration
# Verify with: /ip firewall raw print
# Test connectivity before committing
```

---

## ⚠️ Important Notes

1. **Version Compatibility**: These scripts are tested only for **RouterOS 7.x**. Use with caution on other versions.

2. **Before Applying**:
   - Always backup your current configuration
   - Test in a staging environment first
   - Document all custom IP addresses and interface names
   - Ensure you have remote access before applying firewall rules

3. **Firewall Rules**:
   - These rules are optimized for BGP edge routers with specific ISP characteristics
   - Adjust rules based on your actual network topology
   - The transparent mode rule (disabled by default) should only be enabled in transparent mode environments

4. **Email Alerts**:
   - Configure SMTP server in RouterOS System settings for the BGP monitoring script
   - Test email functionality before deploying to production

5. **Cloud Backup**:
   - Ensure internet connectivity and MikroTik account access
   - Strong passwords are essential for cloud backup security

---

## 👨‍💻 Author

**Eduardo Hartmann**  
GitHub: [@EdHart85](https://github.com/EdHart85)

### Acknowledgments
- Based on work by **Cha0s** (BGP monitoring script for RouterOS 6)
- Based on insights from **Daryll Swer** (Edge router BGP optimization guide)
- Official [MikroTik Documentation](https://wiki.mikrotik.com/)

---

## 📜 License

This project is licensed under the **GPL-3.0 License** - see the [LICENSE](LICENSE) file for details.

---

## 🤝 Contributing

Contributions are welcome! If you have improvements, bug fixes, or new scripts:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-script`)
3. Commit your changes (`git commit -m 'Add amazing script'`)
4. Push to the branch (`git push origin feature/amazing-script`)
5. Open a Pull Request

---

## 📞 Support & Community

- **Issues**: Report bugs or request features via [GitHub Issues](https://github.com/EdHart85/mikrotik-routeros-scripts/issues)
- **Wiki**: For detailed setup guides and troubleshooting
- **MikroTik Community**: Visit the [official MikroTik forums](https://forum.mikrotik.com/)

---

## 🔗 Resources

- [MikroTik RouterOS Official Wiki](https://wiki.mikrotik.com/)
- [BGP Configuration Guide](https://wiki.mikrotik.com/wiki/Manual:BGP)
- [Firewall Rules Documentation](https://wiki.mikrotik.com/wiki/Manual:IP/Firewall)
- [System Backup & Recovery](https://wiki.mikrotik.com/wiki/Manual:System)
- [Daryll Swer's Edge Router BGP Optimization](https://www.daryllswer.com/edge-router-bng-optimisation-guide-for-isps/)

---

**Last Updated:** 2026-03-26  
**Tested Version:** RouterOS 7.x
