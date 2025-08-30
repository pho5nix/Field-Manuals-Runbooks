# RANSOMWARE INCIDENT RESPONSE FIELD MANUAL
## Cybersecurity Response Protocol v2025

---

### OPERATIONAL SECURITY CLASSIFICATION: CONFIDENTIAL
### LAST UPDATED: AUGUST 2025
### ACTIVATION CODE: RANSOMWARE INCIDENT CONFIRMED

---

## IMMEDIATE ACTION CHECKLIST (0-15 MINUTES)

### Phase 1: Initial Containment
**Time Critical - Execute Simultaneously**

1. **ISOLATE AFFECTED SYSTEMS**
   - Disconnect ethernet cables physically
   - Disable WiFi at access point level
   - DO NOT power off systems (preserves volatile memory)
   - Document which systems show encryption indicators

2. **ACTIVATE INCIDENT RESPONSE TEAM**
   - Security Operations Center (SOC)
   - IT Infrastructure Team
   - Legal Counsel
   - Communications/PR Team
   - Executive Leadership
   - Cyber Insurance Carrier

3. **PRESERVE EVIDENCE**
   - Take photographs of ransom notes/screens
   - Document timestamps of first detection
   - Capture memory dumps if safely possible
   - Record all system behaviors and error messages

4. **IMPLEMENT NETWORK SEGMENTATION**
   - Isolate critical infrastructure (domain controllers, backup servers)
   - Disconnect shared drives and network storage
   - Disable VPN access temporarily
   - Block all RDP and administrative protocols

---

## ASSESSMENT PHASE (15-60 MINUTES)

### Threat Intelligence Gathering

**Ransomware Variant Identification**
- Examine file extensions (.locked, .encrypted, etc.)
- Review ransom note for group indicators
- Check IoCs against threat intelligence feeds
- Identify encryption methodology if possible

**Attack Vector Analysis**
- Review email gateway logs (last 72 hours)
- Check for exposed RDP ports (3389)
- Analyze VPN access logs
- Examine recent software installations
- Review PowerShell execution history

**Scope Determination Matrix**

| System Type | Priority | Check Method | Action if Compromised |
|------------|----------|--------------|----------------------|
| Domain Controllers | CRITICAL | Event logs, SYSVOL status | Immediate isolation, prepare rebuild |
| Backup Infrastructure | CRITICAL | Verify integrity, check timestamps | Disconnect, validate offline copies |
| Production Databases | HIGH | Query responsiveness, file locks | Export critical data if accessible |
| File Servers | HIGH | Sample file accessibility | Map affected shares, isolate |
| Workstations | MEDIUM | User reports, AV alerts | Reimage queue preparation |
| Development Systems | LOW | Developer verification | Snapshot current state |

---

## CONTAINMENT STRATEGY (1-4 HOURS)

### Network-Level Controls

**Firewall Emergency Rules**
```
BLOCK: All outbound connections to TOR nodes
BLOCK: All traffic to known C2 infrastructure
BLOCK: Ports 445, 139, 3389 between VLANs
BLOCK: All traffic from affected subnet to backup VLAN
MONITOR: DNS queries for DGA patterns
```

**Active Directory Hardening**
1. Reset krbtgt password twice (with 10-hour interval)
2. Disable all service accounts temporarily
3. Force password reset for all privileged accounts
4. Review and remove suspicious GPOs
5. Enable enhanced audit logging

### Endpoint Response Actions

**For Encrypted Systems:**
- Maintain power (encryption keys in memory)
- Run approved memory capture tools
- Document encryption timestamp
- Check for shadow copies: `vssadmin list shadows`
- DO NOT run decryption tools without validation

**For Clean Systems:**
- Deploy EDR in aggressive blocking mode
- Disable macro execution
- Implement application whitelisting
- Block PowerShell for standard users
- Enable controlled folder access

---

## ERADICATION PROCEDURES (4-24 HOURS)

### Threat Hunting Checklist

**Persistence Mechanisms to Check:**
- Scheduled tasks with encoded PowerShell
- WMI event subscriptions
- Registry run keys
- Service modifications
- DLL hijacking attempts
- Startup folder items

**Common Ransomware Artifacts:**
```powershell
# Check for suspicious processes
Get-Process | Where-Object {$_.Path -like "*temp*" -or $_.Path -like "*appdata*"}

# Review recent service installations
Get-WmiObject win32_service | Where-Object {$_.StartMode -eq 'Auto' -and $_.State -eq 'Running'} | Select Name, DisplayName, PathName, StartName

# Scan for encoded commands in scheduled tasks
schtasks /query /fo LIST /v | findstr /i "powershell base64"
```

### Malware Removal Protocol

1. **Deploy specialized ransomware removal tools**
   - Run from bootable media when possible
   - Use multiple vendors' tools for validation
   - Document all findings

2. **Registry cleaning targets**
   - HKLM\Software\Microsoft\Windows\CurrentVersion\Run
   - HKCU\Software\Microsoft\Windows\CurrentVersion\Run
   - HKLM\System\CurrentControlSet\Services

3. **File system cleanup**
   - Remove ransomware executables
   - Clear %TEMP% and %APPDATA% locations
   - Eliminate dropped scripts and tools

---

## RECOVERY OPERATIONS (24-72 HOURS)

### Backup Restoration Decision Tree

```
Is backup infrastructure compromised?
├─ YES → Rebuild backup servers from known-good media
│        Validate backup integrity offline
│        Implement air-gap for restoration
└─ NO → Verify backup timestamp predates infection
         Test restore on isolated system
         Implement staged restoration plan
```

### System Rebuild Priority Matrix

**Priority 1: Infrastructure (0-24 hours)**
- Domain controllers (from backup or rebuild)
- DNS/DHCP services
- Certificate authorities
- Core network services

**Priority 2: Business Critical (24-48 hours)**
- ERP/Financial systems
- Customer-facing applications
- Email infrastructure
- Critical databases

**Priority 3: Productivity (48-72 hours)**
- File shares
- Collaboration platforms
- User workstations
- Development environments

### Decryption Considerations

**Before Paying Ransom:**
- Consult law enforcement (FBI IC3, Secret Service)
- Verify decryptor functionality with test files
- Negotiate through experienced intermediary
- Document all communications
- Prepare for potential non-delivery

**Free Decryption Resources:**
- Check No More Ransom project
- Consult vendor decryption databases
- Engage incident response firms
- Review security researcher releases

---

## POST-INCIDENT ACTIVITIES

### Lessons Learned Documentation

**Technical Analysis Required:**
- Complete attack timeline
- Initial compromise vector
- Lateral movement techniques
- Data exfiltration evidence
- Security control failures

**Process Improvements:**
- Incident detection gaps
- Response time analysis
- Communication effectiveness
- Decision-making bottlenecks
- Resource availability issues

### Enhanced Security Posture

**Immediate Implementations (Week 1)**
- MFA on all external access points
- Privileged access management (PAM)
- Network segmentation validation
- EDR deployment completion
- Backup infrastructure hardening

**Short-term Improvements (Month 1)**
- Zero-trust architecture planning
- Security awareness training
- Phishing simulation program
- Vulnerability management maturity
- Incident response retainer

**Long-term Strategy (Quarter 1)**
- Security operations center (SOC) enhancement
- Threat intelligence program
- Purple team exercises
- Business continuity planning
- Cyber resilience framework

---

## COMMUNICATION TEMPLATES

### Internal Stakeholder Update
```
SITUATION: Ransomware incident detected at [TIME]
IMPACT: [NUMBER] systems affected, [SERVICES] disrupted
ACTIONS: Containment complete, recovery in progress
TIMELINE: Estimated restoration by [DATE/TIME]
NEEDS: [Specific resource requirements]
```

### Law Enforcement Notification
```
Agency: FBI Cyber Division / IC3
Incident Type: Ransomware
Variant: [If known]
Impact: $[Estimated] in downtime/recovery
Data Theft: [Yes/No/Unknown]
Ransom Demand: [Amount in cryptocurrency]
```

### Cyber Insurance Claim
```
Policy Number: [XXX]
Incident Date/Time: [Timestamp]
Discovery Method: [How detected]
Affected Systems: [Count and types]
Business Impact: [Operational/Financial]
Response Actions: [Per this runbook]
```

---

## REFERENCE MATERIALS

### Critical Contacts

| Role | Primary | Backup | After-Hours |
|------|---------|--------|-------------|
| Incident Commander | [Name/Phone] | [Name/Phone] | [Phone] |
| FBI Cyber Division | 1-855-292-3937 | ic3.gov | 24/7 |
| Cyber Insurance | [Carrier/Phone] | [Broker/Phone] | [Hotline] |
| Legal Counsel | [Firm/Phone] | [Attorney/Phone] | [Emergency] |
| PR/Communications | [Contact/Phone] | [Backup/Phone] | [Phone] |

### Command Center Tools

**Essential Software:**
- FTK Imager (Memory capture)
- Wireshark (Network analysis)
- Sysinternals Suite (System analysis)
- YARA (Malware scanning)
- Volatility (Memory analysis)

**Cloud Resources:**
- VirusTotal (Hash checking)
- Have I Been Pwned (Credential verification)
- Shodan (External exposure assessment)
- AlienVault OTX (Threat intelligence)

### Regulatory Requirements

**Notification Timelines:**
- GDPR: 72 hours to supervisory authority
- CCPA: Without unreasonable delay
- HIPAA: 60 days to affected individuals
- State Breach Laws: Varies (typically 30-60 days)
- SEC (public companies): 4 business days

---

## APPENDICES

### A. Ransomware Families Quick Reference (2025)

| Family | Extension | Initial Vector | Encryption | Notes |
|--------|-----------|----------------|------------|-------|
| LockBit 4.0 | .lockbit4 | RDP, Phishing | AES-256 + RSA-4096 | StealBit data theft |
| BlackCat/ALPHV | .alphv | Compromised credentials | AES-128 | Rust-based, cross-platform |
| Cl0p | .cl0p | Zero-day exploits | AES + RSA | MOVEit, GoAnywhere targeting |
| Akira | .akira | VPN vulnerabilities | ChaCha20 | Targets VMware ESXi |
| Black Basta | .basta | Qakbot, phishing | ChaCha20 + RSA-4096 | Double extortion |

### B. Recovery Time Objectives

| System Category | RTO Target | RPO Target | Justification |
|----------------|------------|------------|---------------|
| Payment Processing | 4 hours | 1 hour | Revenue critical |
| Email | 8 hours | 4 hours | Communication essential |
| File Shares | 24 hours | 24 hours | Productivity impact |
| Development | 48 hours | 48 hours | Project timelines |
| Archives | 72 hours | 1 week | Historical data |

### C. Validation Checklist

**Before Declaring "All Clear":**
- [ ] All systems restored or rebuilt
- [ ] Vulnerability that allowed access patched
- [ ] Enhanced monitoring deployed
- [ ] Backups validated and tested
- [ ] Incident report completed
- [ ] Legal notifications sent
- [ ] Insurance claim filed
- [ ] Lessons learned documented
- [ ] Security improvements implemented
- [ ] Staff training completed

---

**END OF FIELD MANUAL**

*This document contains sensitive security information. Distribute only to authorized personnel involved in incident response. Review and update quarterly or after significant incidents.*
