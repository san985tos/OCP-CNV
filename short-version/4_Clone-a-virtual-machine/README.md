# Clone a Virtual Machine

In this lab we're going to clone a workload and see that it's identical to the source of the clone. To do this we'll complete the following steps:

- Download and customise a Fedora 34 image (Preparations step)
- Launch it as a virtual machine via OpenShift Virtualization
- Install a basic application inside the VM.
- Clone the VM
- Test the clone to make sure it's identical to the source

- Create a Fedora VM

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: "fc34-original"
  labels:
    app: containerized-data-importer
  annotations:
    cdi.kubevirt.io/storage.import.endpoint: "http://192.168.123.100:81/Fedora-Cloud-Base-34-1.2.x86_64.raw"
spec:
  volumeMode: Block
  storageClassName: ocs-storagecluster-ceph-rbd
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 40Gi
---
apiVersion: kubevirt.io/v1alpha3
kind: VirtualMachine
metadata:
  name: fc34-original
  labels:
    app: fc34-original
    os.template.kubevirt.io/fedora34: 'true'
    vm.kubevirt.io/template-namespace: openshift
    workload.template.kubevirt.io/server: 'true'
spec:
  running: true
  template:
    metadata:
      labels:
        vm.kubevirt.io/name: fc34-original
    spec:
      domain:
        cpu:
          cores: 1
          sockets: 1
          threads: 1
        devices:
          disks:
            - bootOrder: 1
              disk:
                bus: virtio
              name: disk0
          interfaces:
            - bridge: {}
              model: virtio
              name: nic0
          networkInterfaceMultiqueue: true
          rng: {}
        machine:
          type: pc-q35-rhel8.1.0
        resources:
          requests:
            memory: 1024M
      evictionStrategy: LiveMigrate
      hostname: fc34-original
      networks:
        - multus:
            networkName: tuning-bridge-fixed
          name: nic0
      terminationGracePeriodSeconds: 0
      volumes:
        - name: disk0
          persistentVolumeClaim:
            claimName: fc34-original
```
