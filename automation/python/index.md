# Python Automation
Automation / Python
Overview
This section showcases my practical work in network automation using Python.
My focus is on automating real-world network engineering tasks across Cisco DNAC, ISE, WLC, and traditional CLI‑based devices.

The goal of my automation work is simple:

Reduce repetitive manual tasks

Improve accuracy and consistency

Integrate APIs into network operations

Build reusable automation workflows

Skills & Tools
I use the following tools and libraries in my automation projects:

Python 3.x

REST APIs (GET / POST / PUT / DELETE)

Requests, JSON, YAML

Netmiko / Paramiko for CLI automation

Jinja2 for configuration templating

Ansible for orchestration

Cisco DNAC API

Cisco ISE ERS API

Cisco WLC REST API

Git / GitHub

Postman for API testing

Use Cases
1. Cisco DNAC – Retrieve Device Inventory
Automates the retrieval of all network devices from DNAC for documentation, auditing, and compliance.

python
import requests
import json

dnac = "https://dnac.example.com"
token = "YOUR_TOKEN"

headers = {"X-Auth-Token": token}
url = f"{dnac}/dna/intent/api/v1/network-device"

response = requests.get(url, headers=headers, verify=False)
print(json.dumps(response.json(), indent=2))
2. Cisco ISE – Bulk Import MAC Addresses
Used for onboarding large numbers of endpoints into ISE.

python
import requests
import json

ise = "https://ise.example.com"
headers = {
    "Content-Type": "application/json",
    "Accept": "application/json"
}

payload = {
    "ERSEndPoint": {
        "name": "TestDevice",
        "mac": "AA:BB:CC:DD:EE:FF",
        "groupId": "123456"
    }
}

response = requests.post(
    f"{ise}/ers/config/endpoint",
    headers=headers,
    auth=("admin", "password"),
    data=json.dumps(payload),
    verify=False
)

print(response.status_code)
3. Cisco WLC – Retrieve AP List
Exports AP inventory for reporting and troubleshooting.

python
import requests

wlc = "https://wlc.example.com"
response = requests.get(
    f"{wlc}/api/ap",
    auth=("admin", "password"),
    verify=False
)

print(response.json())
4. Generate Network Config Using Jinja2
Used for switch provisioning and bulk configuration generation.

python
from jinja2 import Template

template = Template("""
interface {{ interface }}
 description {{ desc }}
 switchport access vlan {{ vlan }}
""")

print(template.render(
    interface="Gig1/0/1",
    desc="Uplink",
    vlan=10
))
5. CLI Automation with Netmiko
Automates repetitive CLI tasks such as backups, config pushes, and audits.

python
from netmiko import ConnectHandler

device = {
    "device_type": "cisco_ios",
    "host": "10.1.1.1",
    "username": "admin",
    "password": "password"
}

conn = ConnectHandler(**device)
output = conn.send_command("show ip interface brief")
print(output)
conn.disconnect()
Automation Workflow
My typical automation workflow:

Identify a repetitive or error‑prone task

Test the API or CLI manually

Build a Python script

Add error handling and logging

Convert to reusable functions

Integrate with Ansible or GitHub

Document the workflow

Future Work
Full DNAC automation workflow (inventory → config → compliance)

ISE policy automation

Automated lab topology deployment

Ansible playbooks for switch provisioning

API‑driven network documentation generator

⭐ Summary
This section demonstrates my hands‑on experience with Python automation in real network environments.
All examples are based on real workflows I use in enterprise networks.
