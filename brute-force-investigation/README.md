# Brute Force Attack Investigation

## Objective

Investigate repeated failed login attempts and identify
whether they indicate a possible brute force attack.

## Environment

- Operating System: Windows
- Tool: Windows Event Viewer
- Event ID: 4625

## Investigation

Multiple failed authentication attempts were analyzed
using Windows Security Event Logs.

The investigation focused on:

- Username
- Timestamp
- Source information
- Number of failed attempts
- Authentication details

## Finding

Repeated failed login attempts against a test account
can indicate a possible brute force attack.

## MITRE ATT&CK

T1110 - Brute Force

## Recommendations

- Use strong passwords
- Enable MFA
- Configure account lockout policies
- Monitor authentication failures
- Investigate unusual login activity

## Conclusion

The investigation demonstrates how Windows Security
Event Logs can be used to identify suspicious
authentication activity.
