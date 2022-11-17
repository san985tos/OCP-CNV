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

## Deploying OpenShift Virtualization

- Open the OpenShift console and go to Operator hub menu

  ![This is an image](images/1.png)
  ![This is an image](images/2.png)
  ![This is an image](images/3.png)

- Next we need to deploy the HyperConverged resource, which, in addition to the OpenShift Virtualization operator, creates and maintains an OpenShift Virtualization Deployment for the cluster. Click on "Create HyperConverged", as a required operand, in the same screen to proceed.

  ![This is an image](images/4.png)

- This will open a new screen. We can again accept all the defaults for this lab. Continue the installation by clicking on "Create" at the bottom.

  ![This is an image](images/5.png)

- After a while, all these pods must be in RUNNING status

  ```
  watch -n2 'oc get pods -n openshift-cnv'
  ```

- The operator installation just be in succeded

  ```
  oc get csv -n openshift-cnv
  ```

- Description of the pods:

  ![This is an image](images/6.png)

- There's also a few custom resources that get defined too. For example the NodeNetworkState (nns) provides the current network configuration of our nodes - this is used to verify whether physical networking configurations have been successfully applied by the nmstate-handler pods. This is useful for ensuring that the NetworkManager state on each node is configured as required. We use this for defining interfaces/bridges on each of the machines for both physical machine connectivity and for providing network access for pods (and virtual machines) within OpenShift/Kubernetes. View the NodeNetworkState state with:

  ```
  oc get nns -A
  ```

  Example:

  ```
  [root@ocp4-bastion ~]# oc get nns -A
  NAME                           AGE
  ocp4-master1.aio.example.com   9m45s
  ocp4-master2.aio.example.com   9m47s
  ocp4-master3.aio.example.com   9m56s
  ocp4-worker1.aio.example.com   9m54s
  ocp4-worker2.aio.example.com   9m50s
  ocp4-worker3.aio.example.com   9m57s
  [root@ocp4-bastion ~]#
  ```

- You can then view the details of each managed node with:

  ```
  oc get nns/ocp4-worker1.aio.example.com -o yaml
  ```

## Storage

- Check Storage Classes of the lab

  ```
  oc get sc
  ```

- Then check which version of the OCS operator is installed by executing the following

  ```
  oc get csv -n openshift-storage
  ```

  Example

  ```
  [root@ocp4-bastion ~]# oc get csv -n openshift-storage
  NAME                   DISPLAY                       VERSION   REPLACES               PHASE
  mcg-operator.v4.9.12   NooBaa Operator               4.9.12    mcg-operator.v4.9.11   Succeeded
  ocs-operator.v4.9.12   OpenShift Container Storage   4.9.12    ocs-operator.v4.9.11   Succeeded
  odf-operator.v4.9.12   OpenShift Data Foundation     4.9.12    odf-operator.v4.9.11   Succeeded
  ```

- create the PVC with all this included:

  ```
  cat << EOF | oc apply -f -
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: "rhel8-ocs"
    labels:
      app: containerized-data-importer
    annotations:
      cdi.kubevirt.io/storage.import.endpoint: "http://192.168.123.100:81/rhel8-kvm.img"
  spec:
    volumeMode: Block
    storageClassName: ocs-storagecluster-ceph-rbd
    accessModes:
    - ReadWriteMany
    resources:
      requests:
        storage: 40Gi
  EOF
  ```

- Once created, CDI triggers the importer pod automatically to take care of the conversion for you:

  ```
  oc get pods
  ```

- You should see the importer pod as below:

  ```
  NAME                   READY   STATUS              RESTARTS   AGE
  importer-rhel8-ocs     0/1     ContainerCreating   0          1s
  ```

- Watch the logs and you can see the process, it may initially give an error about the pod waiting to start, you can retry after a few seconds:

  ```
  oc logs importer-rhel8-ocs -f
  ```

- You will see the log output as below:

  ```
  I1103 17:33:42.409423       1 importer.go:52] Starting importer
  I1103 17:33:42.442150       1 importer.go:135] begin import process
  I1103 17:33:42.447139       1 data-processor.go:329] Calculating available size
  I1103 17:33:42.448349       1 data-processor.go:337] Checking out block volume size.
  I1103 17:33:42.448380       1 data-processor.go:349] Request image size not empty.
  I1103 17:33:42.448395       1 data-processor.go:354] Target size 40Gi.
  I1103 17:33:42.448977       1 nbdkit.go:269] Waiting for nbdkit PID.
  I1103 17:33:42.949247       1 nbdkit.go:290] nbdkit ready.
  I1103 17:33:42.949288       1 data-processor.go:232] New phase: Convert
  I1103 17:33:42.949328       1 data-processor.go:238] Validating image
  I1103 17:33:42.969690       1 qemu.go:250] 0.00
  I1103 17:33:47.145392       1 qemu.go:250] 1.02
  I1103 17:33:53.728302       1 qemu.go:250] 2.03
  I1103 17:33:55.924329       1 qemu.go:250] 3.05
  I1103 17:33:58.014054       1 qemu.go:250] 4.06
  (...)
  I0317 11:46:56.253155       1 prometheus.go:69] 98.24
  I0317 11:46:57.253350       1 prometheus.go:69] 100.00
  I0317 11:47:00.195494       1 data-processor.go:205] New phase: Resize
  I0317 11:47:00.524989       1 data-processor.go:268] Expanding image size to: 40Gi
  I0317 11:47:00.878420       1 data-processor.go:205] New phase: Complete
  ```

- To view the structure of the importer pod to get some of the configuration it's using

  ```
  oc describe pod $(oc get pods | awk '/importer/ {print $1;}')
  ```

- Display the PVC

  ```
  oc get pvc
  ```

- Display the PVs

  ```
  oc get pv
  ```

-
