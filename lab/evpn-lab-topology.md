
---

### 4. `lab/evpn-lab-topology.md`

```markdown
# EVPN/VXLAN Lab Topology

This page documents my EVPN/VXLAN lab topology and key configuration ideas.

---

## 1. Lab goals

- Understand **EVPN control-plane** (BGP EVPN).
- Practice **VXLAN data-plane** encapsulation.
- Test **L2 and L3 VNI**.
- Try **multi-homing** and **anycast gateway**.

---

## 2. Topology overview

Basic Clos-style fabric:

- **Spines:**
  - Spine1, Spine2
- **Leafs:**
  - Leaf1, Leaf2, Leaf3, Leaf4
- **Hosts:**
  - Host-A (behind Leaf1)
  - Host-B (behind Leaf2)
  - Host-C (behind Leaf3/Leaf4 for multihoming tests)

Underlay:

- IP routed fabric using **ISIS** or **OSPF** (or static in small lab).
- Loopbacks used for:
  - BGP EVPN peering
  - VTEP source addresses

Overlay:

- VXLAN tunnels between leafs.
- BGP EVPN as control-plane.

---

## 3. Addressing plan (example)

- **Spine loopbacks:** `10.0.0.1/32`, `10.0.0.2/32`
- **Leaf loopbacks:** `10.0.0.11/32`, `10.0.0.12/32`, `10.0.0.13/32`, `10.0.0.14/32`
- **VTEP loopbacks:** same as router loopback or separate (e.g. `10.0.1.x/32`)
- **Underlay links:** `/31` or `/30` between spine–leaf

VLAN/VNI mapping example:

- **VLAN 10 → VNI 1010** (Tenant-A L2)
- **VLAN 20 → VNI 1020** (Tenant-B L2)
- **L3 VNI:** `10000` (Tenant-A VRF)

---

## 4. Control-plane: BGP EVPN

- Spines act as **route reflectors** for EVPN.
- Leafs are **RR clients**.

On spines (conceptual):

- `router bgp 65000`
- `address-family l2vpn evpn`
- `neighbor LEAFx route-reflector-client`

On leafs:

- `router bgp 65000`
- `address-family l2vpn evpn`
- `neighbor SPINEx activate`

---

## 5. Data-plane: VXLAN

On leafs:

- Define **VTEP source interface** (loopback).
- Map VLANs to VNIs.
- Enable **ingress-replication** or **multicast** for BUM traffic.

Conceptual example:

```text
interface nve1
  source-interface Loopback0
  member vni 1010
    ingress-replication protocol bgp
  member vni 1020
    ingress-replication protocol bgp
