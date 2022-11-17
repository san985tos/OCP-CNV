# OCP-CNV

## Login to the lab

- The environment "OpenShift AIO with OCP Virtualization" is needed from RHPDS
- Login to bastion with the information from received email

```
❯ ssh lab-user@145.40.121.163
lab-user@145.40.121.163's password:
Activate the web console with: systemctl enable --now cockpit.socket

[lab-user@hypervisor ~]$
```

- SSH to bastion

```
[lab-user@hypervisor ~]$ ssh root@192.168.123.100
The authenticity of host '192.168.123.100 (192.168.123.100)' can't be established.
ECDSA key fingerprint is SHA256:nMS27GaKplE2GMSvmS6MAGiEJAFbW1LB32HW2HCfIYY.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.123.100' (ECDSA) to the list of known hosts.
root@192.168.123.100's password:
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Wed Nov 16 23:54:35 2022 from 192.168.123.104
[root@ocp4-bastion ~]#
```
