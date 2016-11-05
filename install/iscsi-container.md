## Installing and Running NexentaEdge Scale-Out Shared NameSpace iSCSI protocol compatible service container

### Step 1: Setting up Replicast network
NexentaEdge design for high performance and massive scalability beyound 1000 servers per cluster. It doesn't have central metadata server or coordination server. Its design is shared nothing with metadata and data fully distributed across the cluster. To work optimally NexentaEdge requires dedicated backend high-performance network, isolated with VLAN segment and set for Jumbo Frames.

Follow guide lines from from Data Container with regards of setting up Replicast network.

### Step 2: Prepare local host configuration for Data Container
There are example configuration files (see conf directory) to modify. Adjust networking interface. Typicaly first port assigned will be eth0.

### Step 3: Create service configuration
Use NEADM management tool to setup service parameters
```
neadm service create iscsi iscsi-mongodb
neadm service serve company-branch1/finance/databases/mongodb
```

### Step 4: Run NexentaEdge GW NFS service across cluster
There is no limits on how many GW containers can existing within Replicast network. Start the NexentaEdge service container with the following run command:
```
docker run --network host --name nedge-iscsi-mongodb \
	-e CCOW_SVCNAME=iscsi-mongodb \
	-e HOST_HOSTNAME=$(hostname) -d -t -i --privileged=true \
	-v /root/gw0/ccow.json:/opt/nedge/etc/ccow/ccow.json \
	-v /root/gw0/ccowd.json:/opt/nedge/etc/ccow/ccowd.json \
	-v /root/gw0/auditd.ini:/opt/nedge/etc/ccow/auditd.ini \
	-v /root/gw0/corosync.conf:/opt/nedge/etc/corosync/corosync.conf \
	-v /etc/localtime:/etc/localtime:ro \
	-v /etc/timezone:/etc/timezone:ro \
        nexenta/nedge /opt/nedge/nmf/nefcmd.sh start -j iscsiserv
```

At this point you will have iSCSI block service running and exporting LUN to serve MongoDB database

### Step 6: Verify that service is running

TODO