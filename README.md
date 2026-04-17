# splunk-bruteforce-detection-lab
Brute force attack detection using Splunk SIEM

#  SSH Brute Force Investigation using Splunk

##  Project Overview

This project demonstrates how to investigate and detect SSH brute force attacks using log data (`auth.log`) in Splunk.
The analysis follows a structured SOC (Security Operations Center) investigation process.

---

## Objectives

* Identify attacking IP addresses
* Analyze login attempts and patterns
* Detect brute force behavior
* Determine if compromise occurred
* Recommend security actions

---

##  Tools Used

* Splunk (SIEM)
* Linux authentication logs (auth.log)

---

##  Investigation Process

### Step 1: Identify Attacking IP

---
index=main sourcetype=bruteforceattack.log "Failed password"
| stats count by src_ip
| sort - count


**Goal:** Find IPs with highest failed login attempts.

---

###  Step 2: Count Login Attempts

index=main sourcetype=linux_secure "Failed password"
| stats count by src_ip

**Goal:** Determine attack intensity.

---

###  Step 3: Analyze Targeted Usernames

index=main sourcetype=bruteforceattack.log "Failed password"
| stats count by user
| sort - count


**Goal:** Identify commonly targeted accounts (e.g., damian, root, admin).

---

### Step 4: Confirm Brute Force Behavior

index=main sourcetype=bruteforceattack.log "Failed password"
| stats dc(user) as unique_users by src_ip

**Goal:** Detect one IP attacking multiple usernames.

---

###  Step 5: Check for Successful Login

index=main sourcetype=bruteforceattack.log ("Failed password" OR "Accepted password")
| stats count by src_ip, action


**Goal:** Determine if attacker gained access.

---

###  Step 6: Determine Attack Time

index=main sourcetype=bruteforceattack.log Failed password"
| stats min(_time) as start max(_time) as end by src_ip
``
**Goal:** Identify attack duration and timing.

---

###  Step 7: Visualize Attack Pattern

add your findings to a dashboard

---

## Findings

* **Attacker IP:** 185.220.101.47
* **Attack Type:** SSH brute force (dictionary attack)
* **Targeted Users:** root, admin, postgres, test
* **Time Window:** Short burst (automated activity)
* **Successful Login:  No

---

##  Analysis

The attacker attempted multiple logins within a short time frame using different usernames.
This pattern indicates an automated brute force attack using a username/password dictionary.

---

## Indicators of Compromise (IOCs)

* Malicious IP: 185.220.101.47
* Multiple failed login attempts
* Repeated invalid username patterns

---

##  Recommendations

* Block malicious IP at firewall
* Disable SSH root login
* Enforce strong password policies
* Enable Multi-Factor Authentication (MFA)
* Deploy tools like fail2ban

--

## Conclusion

This project demonstrates a real-world SOC investigation workflow for detecting SSH brute force attacks using Splunk.
The structured approach ensures accurate detection, analysis, and response to potential threats.

---

