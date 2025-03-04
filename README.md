# CSE-307-Project
**README: AZ Network Solutions – 8-Floor Office Building Network Design**

---

### 1. Overview
This project showcases a network design for AZ Network Solutions, a mid-sized enterprise occupying an 8-floor office building. The primary goals are to ensure:
- **Efficient communication** between all floors
- **Scalability** for future expansion
- **High performance** and **fault tolerance**  
- **Automatic route updates** through dynamic routing

Each floor implements a specific topology based on unique requirements and is assigned a distinct IP address range. This design uses routers, switches, and hubs as needed to meet redundancy, cost-effectiveness, and manageability requirements.

---

### 2. Topology Selection by Floor

1. **Floor 1, Floor 2 & Floor 3**  
   - **Topology**: Mesh  
   - **Reason**: Provides high reliability and multiple communication paths. In a mesh, each device can have multiple direct connections, ensuring minimal downtime if a link fails.

2. **Floor 4 & Floor 5**  
   - **Topology**: Ring  
   - **Reason**: Ensures redundancy and fault tolerance. A ring topology allows traffic to flow in both directions, helping maintain connectivity if one link breaks.

3. **Floor 6, Floor 7 & Floor 8**  
   - **Topology**: Star  
   - **Reason**: Simple, cost-effective design centered on a switch (or router). Easy to maintain and expand as needed.

---

### 3. IP Addressing Scheme

Each floor is assigned a specific IP network range. Subnetting within these ranges is used to segment and organize devices (e.g., hosts, routers, switches). The general allocations are:

- **Floor 1**: `172.16.0.0/16` (Class B Private)  
- **Floor 2**: `172.17.0.0/16` (Class B Private)  
- **Floor 3**: `10.0.0.0/8` (Class A Private)  
- **Floor 4**: `10.0.0.0/8` (Class A Private)  
  > *Note:* Floors 3 and 4 share the same major Class A range but will be allocated different subnets within `10.0.0.0/8` to avoid overlap.

- **Floor 5**: `172.15.0.0/16` (Class B Public)  
- **Floor 6**: `172.14.0.0/16` (Class B Public)  
- **Floor 7**: `172.13.0.0/16` (Class B Public)  
- **Floor 8**: `172.12.0.0/16` (Class B Public)  

**Subnetting**  
- Each floor is subdivided further based on the number of devices and the topology.  
- Ensure each router interface and device segment has its own subnet.

---

### 4. Dynamic Routing Strategy

To allow for automatic updates and simpler administration, **dynamic routing** is implemented across all routers. This replaces the previously mentioned static routing. A recommended protocol is **OSPF** (Open Shortest Path First) or **EIGRP** (Enhanced Interior Gateway Routing Protocol, if using Cisco-exclusive devices). Below is an example using OSPF:

1. **Assign Router IDs**:  
   Each router should have a unique Router ID (e.g., using the highest IP address on a loopback interface).

2. **Enable OSPF** on Each Router:  
   ```bash
   Router(config)# router ospf 1
   Router(config-router)# router-id <unique-id>
   ```

3. **Advertise Networks**:  
   For each subnet directly connected to the router, add the corresponding network statements. For example:  
   ```bash
   Router(config-router)# network 172.16.0.0 0.0.255.255 area 0
   Router(config-router)# network 172.17.0.0 0.0.255.255 area 0
   Router(config-router)# network 10.0.0.0 0.255.255.255 area 0
   ...
   ```
   - Adjust the network statements and wildcard masks according to each floor’s subnets.

4. **Area Design** (Optional, for larger deployments):  
   - If desired, divide floors into multiple OSPF areas (e.g., one area per floor). This can help segment routing updates and reduce overhead.

5. **Verify OSPF Adjacencies**:  
   - Use commands like `show ip ospf neighbor` or `show ip route` to ensure proper neighbor relationships and routes are established.

---

### 5. Implementation Details

1. **Devices**  
   - **Routers**: Interconnect each floor and run OSPF (or another chosen dynamic protocol).  
   - **Switches**: Central device for star topology floors and distribution for mesh/ring floors.  
   - **Hubs** (if needed): Typically used in smaller segments or for legacy reasons. Switches are preferred for better performance and segmentation.

2. **Floor Layout**  
   - **Floor 1, 2, 3 (Mesh)**: Each computer or switch may connect to multiple other switches/devices, creating multiple redundant paths.  
   - **Floor 4, 5 (Ring)**: Each switch is connected in a ring. Traffic can travel in either direction.  
   - **Floor 6, 7, 8 (Star)**: A single switch or router acts as the central point; each computer connects to this central device.

3. **Hosts per Floor**  
   - Each floor has **6 computers** connected per the assigned topology.  
   - Additional servers, printers, or other networked devices can be placed in their respective subnets.

4. **Network Segmentation & Subnetting**  
   - Separate subnets are created for each floor (and each segment within a floor if needed).  
   - This prevents broadcast domain overlap and eases management/troubleshooting.

---

### 6. Testing & Verification

1. **Ping/Traceroute**  
   - Verify connectivity between floors by pinging from a host on one floor to a host on another.  
   - Use traceroute to ensure the path follows expected routes and dynamic protocol is functioning.

2. **Routing Table Checks**  
   - On each router, verify the routing table (`show ip route`) to confirm that all floor subnets are learned.

3. **Failover Testing**  
   - Disconnect one link in a mesh or ring topology and confirm that traffic automatically reroutes via the dynamic routing protocol.

---

### 7. Maintenance & Future Expansion

1. **Scalability**  
   - Additional floors or devices can be integrated by assigning new subnets and adding OSPF network statements.  
   - Star floors can easily expand by adding more ports or switches.

2. **Monitoring**  
   - Implement SNMP or NetFlow for traffic and performance monitoring.  
   - Log OSPF neighbor changes to quickly detect network issues.

3. **Security**  
   - Consider using access control lists (ACLs) on routers for segment protection.  
   - Implement VLANs and 802.1X authentication on switches for enhanced security.

---

### 8. Conclusion

This network design balances **reliability**, **performance**, and **manageability** for an 8-floor office building. By leveraging **mesh**, **ring**, and **star** topologies where appropriate, and employing **dynamic routing** (e.g., OSPF) across floors, the network remains robust, scalable, and easier to maintain compared to static-only solutions. Proper subnetting, monitoring, and security practices will ensure the long-term success and stability of AZ Network Solutions’ infrastructure.

---

**End of README**
