# Connectivity Tests

This file contains the ping tests I used to check the network before and after applying the ACL.

## Before Applying the ACL

### Unauthorized-PC → Web-Server

Source: `192.168.40.10`  
Destination: `192.168.20.10`

Result: **Successful**

The Unauthorized-PC was able to reach the Web-Server. This showed that, before adding the ACL, devices in the untrusted VLAN could access the server network.

## After Applying the ACL

### Unauthorized-PC → Web-Server

Result: **Blocked**

Packet loss: **100%**

The ACL blocked traffic from the untrusted VLAN to the server VLAN.

### Unauthorized-PC → Admin-PC

Result: **Blocked**

Packet loss: **100%**

The ACL also prevented the unauthorized device from reaching the admin network.

### Employee-1 → Web-Server

Result: **Successful**

Packet loss: **0%**

Employee traffic to the server was still working after the ACL was applied.

## Conclusion

The tests showed that the ACL was working as intended. It blocked the unauthorized device from reaching the server and admin networks, while normal employee access to the Web-Server was still allowed.