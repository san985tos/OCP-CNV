# Deploy Workloads

- The virtual machine we're going to create will have the following properties
- We are going to create a machine called rhel8-server-ocs.
- We'll utilise the Persistent Volume Claim (PVC) called rhel8-ocs that was created using the CDI utility with a CentOS 8 base image.
- We will utilise the NetworkAttachmentDefinition we created for the underlying host's third NIC (enp3s0 via br1). This is the tuning-bridge-fixed interface which refers to that bridge created previously.
- As we're using Multus as its default networking CNI we also ensure Multus attaches this NetworkAttachmentDefinition.
- Lastly we have set the evictionStrategy to LiveMigrate so that any request to move the instance will use this method (we will explore this in more depth in a later lab).

![This is an image](images/1.png)
