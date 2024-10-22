# Networking
I wanted to use USB C Thunderbolt networking for this since the plan was to run 3 Minisforum MS-01 nodes as a Proxmox cluster. The benefit of Thunderbolt networking is super fast speeds and low latency between nodes, and it doesn't use up any Ethernet or SFP+ ports. It also uses cabling that is inexpensive considering the use case. I'm currently testing with a $10 cable that is getting me ~18.5Gbps transfer speeds.

After getting my second MS-01 and a USB C cable, I followed the Scyto gists for setting up thunderbolt-net: https://gist.github.com/scyto/76e94832927a89d977ea989da157e9dc, specifically "Outcomes" 2 and 4:
  - Thunderbolt setup: https://gist.github.com/scyto/67fdc9a517faefa68f730f82d7fa3570
  - Dual stack OpenFabric (FRR) Routing: https://gist.github.com/scyto/4c664734535da122f4ab2951b22b2085

I ran into a few snags after enabling proxmox cluster and joining the two nodes together, so I wanted to document my process here to make it a litte more streamlined. In the end, I have 2 nodes in a cluster using the Thunderbolt link as the primary cluster network (IPv4), with migration going over the same network. There is a separate management IP using one of the 2.5GB ethernet ports, but I can disconnect those and still leave the cluster intact.

# Steps
- Follow the steps in the Thunderbolt setup on both nodes
- Reboot each node
- Install iperf3 on both nodes and test connectivity
- Add both(all) nodes to /etc/hosts on all nodes, using the IPv4 addresses
- Test ping using hostnames, e.g. `ping -c3 pve3` from all other nodes
  - I'm seeing ping times in sub-milliseconds, like 0.427ms

# Resetting Proxmox Cluster
The docs specifically recommend against this method. However, during testing I found this to be much better than reinstalling Proxmox every time which would require redoing all the other steps. Since there is no shared storage or anything else yet besides simply joining two nodes together in a cluster, I believe this method is pretty safe and reliable to repeat as many times as needed during testing.
- TODO