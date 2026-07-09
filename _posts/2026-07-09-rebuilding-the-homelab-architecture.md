---
layout: post
title: "Rebuilding the Home Lab: Architecture, Automation, and What's Next"
tags:
  - homelab
  - Networking
  - Automation
  - Proxmox
excerpt: The current state of my home lab rebuild — hardware, network segmentation, the services running on it, and the automation layer I'm building on top.
---
I recently stripped the security side of my home lab back to a clean baseline and started rebuilding it properly, It was quite messy, unorganised and made it really overwhelming to learn and work in this environment. Rather than continuing to bolt things onto a setup that had accumulated a lot of one off decisions over time. This post is a snapshot of where that rebuild currently stands: the hardware, the network design, what's running, and what I'm automating.

## Hardware and virtualization

Everything runs on [Proxmox VE](https://www.proxmox.com/), hosted on an Intel NUC of mine I bought 3 years ago, which acts as the hypervisor for every VM and LXC container below.

- **QNAP TS-433 NAS** — Storage and backups.
- **Sophos XG 135** — firewall and router, and the gateway for every VLAN on the network.
- **Ubiquiti switches and access points** — managed switching and wireless.

## Network segmentation

The network is split into five VLANs, each with a distinct purpose and no default trust between them:

|VLAN|Purpose|Subnet|
|---|---|---|
|10|Management|192.168.50.0/24|
|20|Homelab|192.168.60.0/24|
|30|Personal|192.168.70.0/24|
|40|IoT|192.168.80.0/24|
|50|Guest|192.168.90.0/24|

The idea is straightforward: management traffic, personal devices, IoT gadgets, guest devices, and the lab itself shouldn't be able to reach each other by default. If something on the IoT VLAN is ever compromised, it shouldn't have a clear path to anything that matters. This is best practice ensuring a bad actor can't manoeuvre between my internal networks.

## Core services

- **Pi-hole and Unbound**, on the management VLAN, handle DNS filtering and recursive resolution for the network.
- **A Docker host**, on the homelab VLAN, runs a media automation stack (the "*arr" tools).
- **A separate LXC** runs Jellyfin, with Intel QuickSync passthrough for hardware transcoding. This is currently the only service exposed to the internet, for remote access.
- **An Ansible control node**, also on the homelab VLAN, runs the automation layer described below.

## Automation

I've started layering [Ansible](https://www.ansible.com/) on top of the manual setup, beginning with patch management:

- A dedicated control node with an inventory covering the Docker host and Jellyfin. The Proxmox host and NAS are deliberately excluded from automated management for now, both stay hands on and require maintenance each month.
- A dedicated, passphrase less SSH key used only for automation, kept separate from my personal key, so a compromise of one doesn't hand over the other.
- A tested playbook that patches each host and conditionally reboots only if the patch actually requires it.
- The playbook runs on a systemd timer, weekly, so patching doesn't rely on me remembering to do it.

Next up for automation is Terraform, to handle VM and LXC provisioning itself rather than just what runs on top of them once they exist, this is going to be a great project to work on!

## Access and hardening

SSH is key only, root login and password authentication are disabled, and every host uses a standardised non root sudo user rather than ad hoc accounts. The full IP addressing scheme (static ranges, DHCP pools, reserved lab range) is documented in my [homelab repo](https://github.com/JoshCapoz/homelab).

## What's still open

Not everything is fully sorted yet, and I'd rather document that honestly than pretend otherwise:

- There's no firewall rule currently permitting VLAN 20 to reach the Pi-hole resolver on VLAN 10, so hosts on VLAN 20 are resolving DNS directly against 1.1.1.1, bypassing Pi-hole entirely. Sitting on the list to fix.
- I suspect the physical switch port feeding the Proxmox NUC is set to access mode rather than trunked, since two LXCs are currently running untagged as a workaround. Still investigating.

## Conclusion
This has been month's of hard work and dedication to get all these services up and running. I've learnt alot about networking, virtualisation and most importantly how to secure all this infrastructure. I'm excited to keep building and working on my skills!