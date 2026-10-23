# SOC Investigation Report: SSH Brute-Force Activity

## 1. Incident Summary

**Incident Type:** Suspected SSH Brute-Force Activity Followed by Successful Authentication  
**Environment:** Controlled Ubuntu and Kali Linux lab  
**SIEM:** Splunk Enterprise  
**Investigation Date:** October 22, 2026  
**Severity:** High (Lab Classification)  
**Status:** Investigation Completed

## 2. Executive Summary

A controlled SSH brute-force simulation was conducted from a Kali Linux machine against an Ubuntu SSH server.

Splunk detected multiple failed authentication attempts originating from `172.27.166.1`, targeting the invalid username `fakeuser`.

Five failed SSH login attempts occurred between 22:20:04 and 22:20:25, followed by a successful authentication to the account `bless` at 22:20:55.

The successful authentication originated from the same source IP address, triggering a correlation detection.

## 3. Evidence and Timeline

| Time (October 22, 2026) | Event |
|---|---|
| 22:20:04 | Failed SSH authentication for `fakeuser` |
| 22:20:08 | Second failed authentication |
| 22:20:13 | Third failed authentication |
| 22:20:20 | Fourth failed authentication |
| 22:20:25 | Fifth failed authentication |
| 22:20:55 | Successful SSH authentication for `bless` |
| 22:21:16 | SSH session closed |

**Source IP:** `172.27.166.1`  
**Target System:** Ubuntu Linux VM  
**Authentication Method:** SSH password authentication

## 4. Investigation and Analysis

Splunk SPL searches were used to extract source IP addresses, usernames, authentication outcomes, and event timestamps.

The investigation established that:

- Five failed authentication attempts occurred within approximately 21 seconds.
- The attempts targeted the nonexistent account `fakeuser`.
- A successful login to `bless` occurred 30 seconds after the final failure.
- The successful login originated from the same IP address.
- Ubuntu `journalctl` records corroborated the authentication sequence.
- The SSH session ended at 22:21:16.

The investigation also included reviewing available system journal entries for indications of privilege escalation and user-account creation. No such activity was identified in the reviewed entries; this does not establish that all possible post-authentication activity was excluded.

## 5. Detection Logic

The Splunk correlation detection identifies successful SSH authentications preceded by at least five failed password attempts from the same source IP within a 10-minute window.

This behavior is suspicious because repeated failures followed by a successful login can indicate password guessing or credential compromise.

However, the detection does not prove that an account was compromised.

## 6. Response Recommendations

In a production SOC environment, recommended actions would include:

1. Validate whether the source IP and successful login were authorized.
2. Review authentication history and additional activity associated with the successful account.
3. Investigate commands executed, privilege changes, and persistence indicators where telemetry is available.
4. Review relevant endpoint and network telemetry.
5. Escalate confirmed unauthorized access according to incident response procedures.
6. Consider source-IP restrictions, stronger authentication controls, and SSH hardening.

## 7. Final Assessment

**Verdict:** Suspicious authentication pattern reproduced successfully in an authorized lab.

The investigation demonstrated SSH log analysis, SIEM detection development, event correlation, alert configuration, and evidence-based SOC documentation.

Because all authentication activity was intentionally generated, this was a simulated incident rather than a confirmed security breach.
