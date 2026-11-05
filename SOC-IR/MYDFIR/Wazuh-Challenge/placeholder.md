## Findings - Alert 1: Guest Account Enabled (Authorized Security Validation)
- Event ID: 1786716316.965989
- Event Time: Aug. 14, 2026 14:05:14 UTC
- Rule: Guest Account Enabled
- Endpoint: 192.168.228.134 (DESKTOP-GPJMFAB)
- User: mg_win
### Summary
On **2026-08-14 at 14:05:14 UTC**, **alert 100002: Guest Account Enabled** was triggered on **DESKTOPGPJMFAB (192.168.228.134)** after the Windows Guest account was enabled. The activity matched the
custom Wazuh detection rule and was intentionally generated as part of a Wazuh validation exercise to verify that the detection operated as expected. No further incident response actions are required. As a
standard verification step, it is recommended to confirm with user mg_win that the activity was authorized, to rule out unauthorized account use or compromised credentials.

## Findings - Alert 2: Failed SSH Login (Authorized Security Validation)
- Event ID: 1786771762.886360
- Event Time: Aug. 15, 2026 05:29:21 UTC
- Rule: Multiple SSH login failures observed from the same source IP
- Source Address: 192.168.228.134 (username unknown)
- Destination Address: 192.168.228.133 (mg-ubuntu2004-vmw)
- Action: Active Response – firewall-drop
### Summary
On **2026-08-15 at 05:29:21 UTC**, **alert 100003: Multiple SSH login failures observed from the same source IP** was triggered on **mg-ubuntu2004-vmw (192.168.228.133)** after three consecutive failed
SSH authentication attempts originating from **192.168.228.134**. The activity matched the custom Wazuh detection rule, and upon reaching the configured threshold, Wazuh automatically executed the `firewalldrop` 
Active Response, blocking the source IP. The activity was intentionally generated as part of a Wazuh validation exercise to verify that the custom detection rule and Active Response functioned as
expected. No further action is required; however, it is recommended to review authentication logs associated with activity originating from 192.168.228.134 and examine related logs for any additional activity involving the 
source host.
