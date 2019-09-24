## Networking
These steps are required to set up networking on a bare-metal cluster.

First install the metallb LoadBalancer. We also need a mechanism to advertise
the IP addresses assigned to pods to the greater network. For this, we peer
BGP with the home router (Ubiquiti EdgeRouter Lite) from all nodes.

All the relevant details for BGP (ASN, peer IPs, networks) are located in
the `metallb-bgp.yaml` file.

Install metallb
`kubectl apply -f metallb.yaml`

Install metallb with BGP
`kubectl apply -f metallb-bgp.yaml`

Configure EdgeRouter Lite
```
set protocols bgp 64512 parameters router-id 10.0.0.1 
set protocols bgp 64512 maximum-paths ibgp 32
set protocols bgp 64512 neighbor 10.0.0.20 remote-as 64512
set protocols bgp 64512 neighbor 10.0.0.21 remote-as 64512
set protocols bgp 64512 neighbor 10.0.0.22 remote-as 64512
commit
save
```

Verify BGP status on the router
```
matt@ubnt:~$ show ip bgp summary 
BGP router identifier 10.0.0.1, local AS number 64512
BGP table version is 50
1 BGP AS-PATH entries
0 BGP community entries
1  Configured ebgp ECMP multipath: Currently set at 1
32  Configured ibgp ECMP multipath: Currently set at 32

Neighbor                 V   AS   MsgRcv    MsgSen TblVer   InQ   OutQ    Up/Down   State/PfxRcd
10.0.0.20                4 64512 3294       3277      50      0      0  1d02h54m               1
10.0.0.21                4 64512 3279       3266      50      0      0  1d02h42m               1
10.0.0.22                4 64512 3284       3269      50      0      0  1d02h43m               1

Total number of neighbors 3

Total number of Established sessions 3
```

**Note**: The nodes will all establish BGP as soon as the network is configured. They will **not**
advertise any routes until a pod is configured and exposed with a `LoadBalancer`
