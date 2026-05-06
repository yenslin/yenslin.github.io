# Cisco SD-WAN vEdge Template Design

This note captures how I structure vEdge templates in Cisco SD-WAN (vManage) and the reasoning behind each section.

---

## 1. Template design principles

- **Consistency:** Reuse feature templates across sites/regions.
- **Separation of concerns:** Keep transport, VPN, and policy logic modular.
- **Scalability:** Minimize device-specific templates; push variables via CLI/additional templates.
- **Troubleshootability:** Make it easy to map template → running config.

---

## 2. Template types

**Device templates:**

- **Device template (vEdge):** Binds all feature templates to a specific vEdge.
- **Feature templates:** Reusable building blocks.

Common feature templates:

- **System template**
- **VPN 0 (Transport) template**
- **VPN 512 (Management) template**
- **Service VPN templates (VPN 1, 10, etc.)**
- **OMP template**
- **BFD template**
- **Security / ACL / QoS templates**

---

## 3. System template

Key fields I usually define:

- **Hostname**
- **System IP**
- **Site ID**
- **Timezone**
- **Organization name**
- **Controller group-list**

Example (conceptual):

- **system-ip:** `10.255.255.x`
- **site-id:** `1001`
- **organization-name:** `My-Org`
- **controller-group-list:** `1`

---

## 4. Transport VPN (VPN 0)

Purpose: Underlay connectivity to WAN transports (MPLS, Internet, 5G, etc.).

Typical elements:

- **Interfaces:** `ge0/0`, `ge0/1` (e.g. MPLS / INET)
- **IP addressing:** Static or DHCP
- **TLOC extension:** If used
- **NAT:** For internet-facing links
- **Tunnel QoS / color:** `mpls`, `biz-internet`, `public-internet`, etc.

Example structure:

- **Interface ge0/0:**
  - IP: `x.x.x.x/30`
  - Color: `mpls`
  - No NAT
- **Interface ge0/1:**
  - IP: `y.y.y.y/30`
  - Color: `biz-internet`
  - NAT enabled

---

## 5. Management VPN (VPN 512)

Used for out-of-band management (if applicable).

- **Interface:** `eth0` or mgmt interface
- **IP:** Static or DHCP
- **Default route:** To management gateway
- **Services:** SSH/NETCONF if needed

---

## 6. Service VPNs (e.g. VPN 1, 10)

These carry user traffic.

Typical design:

- **VPN 1:** Branch user LAN
- **VPN 10:** Voice or special service
- **VPN 20:** Guest

Each VPN template usually includes:

- **Interfaces:** `ge0/2`, `ge0/3` (LAN)
- **IP addressing:** /24 or /26
- **DHCP server:** Optional
- **Static routes:** To local networks if needed

---

## 7. OMP template

Defines control-plane behavior.

Key parameters:

- **Advertise:** Connected, static, OSPF, BGP
- **Graceful restart**
- **ECMP / load-balancing**
- **EID/Service routes (if used)**

Example:

- **advertise connected**
- **advertise static**
- **ecmp limit 4**

---

## 8. BFD template

Controls tunnel liveliness.

- **Multiplier:** e.g. 7
- **Interval:** e.g. 1000 ms
- **Detection time:** Multiplier × interval

---

## 9. Attaching templates to devices

Workflow:

1. Create all feature templates.
2. Create a device template and attach feature templates.
3. Attach the device template to a vEdge.
4. Fill in device-specific variables (system IP, interface IPs, etc.).
5. Push configuration and verify.

---

## 10. Basic verification commands

On vEdge:

- `show sdwan control connections`
- `show sdwan bfd sessions`
- `show sdwan omp peers`
- `show sdwan omp routes`
- `show interface`
- `show running-config`

I will extend this page later with real configs from my lab and production experience.
