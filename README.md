# OCP-CNV

## Login to the lab

- The environment "OpenShift AIO with OCP Virtualization" is needed from RHPDS.
- Login to lab host with the information from received email

  ```
  ssh lab-user@145.40.121.163
  ```

- SSH to bastion then

  ```
  ssh root@192.168.123.100
  ```

- Get kubeadmin password of the environment

  ```
  echo $(cat /root/ocp-install/auth/kubeadmin-password)
  ```

- Get the console URL

  ```
  oc whoami --show-console
  ```

- Validate the cluster is healty

  ```
  oc get nodes
  ```
