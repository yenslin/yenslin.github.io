# BGP Route Reflector Design Notes

This page captures my design approach for BGP Route Reflectors (RRs) in data center and WAN environments.

---

## 1. Why use route reflectors

- **Scalability:** Avoid full iBGP mesh (`n(n-1)/2` sessions).
- **Control:** Centralize policy (inbound/outbound filtering, communities).
- **Simplicity:** Easier to add new nodes (only peer with RRs).

---

## 2. Basic RR concepts

- **RR:** Reflects routes between its clients.
- **Client:** iBGP neighbor configured as a client of the RR.
- **Non-client:** Normal iBGP neighbor; RR does not reflect between non-clients.
- **Cluster ID:** Identifies the RR cluster; used to prevent loops.

Key rule:

- Routes learned from a **client** are reflected to:
  - All **other clients**
  - All **non-clients**
- Routes learned from a **non-client** are reflected to:
  - **Clients only**

---

## 3. Single vs dual RR design

### Single RR

- **Pros:** Simple, minimal config.
- **Cons:** Single point of failure; not recommended for production.

### Dual RR (recommended)

- Two RRs per “cluster” or per PoP.
- All clients peer with both RRs.
- RRs usually **do not** need to be clients of each other; they can be full-mesh or use another hierarchy.

---

## 4. RR placement

Common patterns:

- **Data center spine as RR:**
  - Spines act as RRs for leafs.
  - Works well in Clos fabrics (EVPN/VXLAN).
- **Dedicated RR nodes:**
  - Virtual or physical routers.
  - Offload control-plane from forwarding devices.

Design considerations:

- **CPU / memory:** RRs handle many BGP updates.
- **Redundancy:** At least 2 RRs per domain.
- **Latency:** Keep RRs close (topologically) to clients.

---

## 5. Basic IOS-XE/IOS-XR style config example

### On RR

```text
router bgp 65000
 bgp cluster-id 1
 neighbor 10.0.0.1 remote-as 65000
 neighbor 10.0.0.1 update-source Loopback0
 neighbor 10.0.0.1 route-reflector-client

 neighbor 10.0.0.2 remote-as 65000
 neighbor 10.0.0.2 update-source Loopback0
 neighbor 10.0.0.2 route-reflector-client

On client
```text
router bgp 65000
 neighbor 10.0.255.1 remote-as 65000
 neighbor 10.0.255.1 update-source Loopback0

 neighbor 10.0.255.2 remote-as 65000
 neighbor 10.0.255.2 update-source Loopback0
