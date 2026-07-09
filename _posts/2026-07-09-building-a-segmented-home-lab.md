---
layout: post
title: "Building a Segmented Home Lab"
tags: [Home Lab, Networking, Proxmox]
excerpt: "A Proxmox hypervisor, a properly segmented network behind a Sophos XGS firewall, self-hosted DNS filtering, hardened Linux servers, and a Windows AD domain to practice both sides of the fence."
---

I built this lab to keep learning hands-on outside of work, my day job is almost entirely
Microsoft 365 and MSP tooling, so this is where I get to work with firewalls, virtualisation,
and identity infrastructure at a level I don't normally touch day to day.

## The hardware

Everything runs on a single Intel NUC hosting **Proxmox VE** as the hypervisor. Proxmox sits
underneath everything else in this write-up, it's what lets me run a firewall, DNS filtering,
multiple Linux servers, and a Windows domain controller off one small box without them
interfering with each other.

## Network design

The network sits behind a **Sophos XGS** firewall, with a UniFi switch and access point handling
wired and wireless. I split the network into five VLANs rather than running everything flat,
partly because that's how I'd expect a real environment to be segmented, and partly because it
forced me to actually think through firewall rules between zones instead of just having one
network where everything can talk to everything.

<figure>
  <svg viewBox="0 0 800 420" xmlns="http://www.w3.org/2000/svg">
    <defs>
      <style>
        .box { fill: #161d2e; stroke: #253150; stroke-width: 1.5; }
        .accent-box { fill: #161d2e; stroke: #4fd1c5; stroke-width: 1.5; }
        .lbl { fill: #e6e9f0; font-family: Arial, sans-serif; font-size: 13px; text-anchor: middle; }
        .lbl-sm { fill: #9aa4bb; font-family: Arial, sans-serif; font-size: 11px; text-anchor: middle; }
        .line { stroke: #4fd1c5; stroke-width: 1.5; fill: none; }
      </style>
    </defs>
    <rect x="330" y="10" width="140" height="46" class="accent-box" rx="8"/>
    <text x="400" y="30" class="lbl">Internet</text>
    <text x="400" y="45" class="lbl-sm">WAN</text>
    <line x1="400" y1="56" x2="400" y2="90" class="line"/>
    <rect x="310" y="90" width="180" height="46" class="accent-box" rx="8"/>
    <text x="400" y="110" class="lbl">Sophos XGS Firewall</text>
    <text x="400" y="125" class="lbl-sm">Perimeter + inter-VLAN rules</text>
    <line x1="400" y1="136" x2="400" y2="170" class="line"/>
    <rect x="300" y="170" width="200" height="46" class="box" rx="8"/>
    <text x="400" y="190" class="lbl">UniFi Switch + AP</text>
    <text x="400" y="205" class="lbl-sm">Wired + wireless</text>
    <line x1="400" y1="216" x2="400" y2="240" class="line"/>
    <line x1="80" y1="240" x2="720" y2="240" class="line"/>
    <line x1="80" y1="240" x2="80" y2="260" class="line"/>
    <rect x="10" y="260" width="140" height="50" class="box" rx="8"/>
    <text x="80" y="280" class="lbl">Guest</text>
    <text x="80" y="296" class="lbl-sm">Isolated, internet only</text>
    <line x1="240" y1="240" x2="240" y2="260" class="line"/>
    <rect x="170" y="260" width="140" height="50" class="box" rx="8"/>
    <text x="240" y="280" class="lbl">IoT</text>
    <text x="240" y="296" class="lbl-sm">No LAN access</text>
    <line x1="400" y1="240" x2="400" y2="260" class="line"/>
    <rect x="330" y="260" width="140" height="50" class="box" rx="8"/>
    <text x="400" y="280" class="lbl">Management</text>
    <text x="400" y="296" class="lbl-sm">Infra device admin</text>
    <line x1="560" y1="240" x2="560" y2="260" class="line"/>
    <rect x="490" y="260" width="140" height="50" class="box" rx="8"/>
    <text x="560" y="280" class="lbl">Admin</text>
    <text x="560" y="296" class="lbl-sm">Trusted admin devices</text>
    <line x1="720" y1="240" x2="720" y2="260" class="line"/>
    <rect x="650" y="260" width="140" height="50" class="box" rx="8"/>
    <text x="720" y="280" class="lbl">LAN</text>
    <text x="720" y="296" class="lbl-sm">General internal</text>
    <line x1="400" y1="310" x2="400" y2="340" class="line"/>
    <rect x="260" y="340" width="280" height="60" class="accent-box" rx="8"/>
    <text x="400" y="362" class="lbl">Proxmox Hypervisor</text>
    <text x="400" y="378" class="lbl-sm">Pi-hole + Unbound · Linux servers · Windows AD + Kali</text>
  </svg>
  <figcaption>Firewall-first segmentation: five VLANs, inter-VLAN traffic controlled at the Sophos XGS, everything virtualised on Proxmox.</figcaption>
</figure>

## DNS filtering

I run **Pi-hole** in front of **Unbound** as a recursive resolver, so DNS queries get filtered for
ads and known-malicious domains network-wide, and Unbound resolves recursively rather than
forwarding to a third-party resolver. It's a small thing, but it's the same DNS-layer filtering
concept I manage for clients at work through DNSFilter, just self-hosted and fully in my control
here.

## Linux hardening

The Linux boxes (Ubuntu and Debian) are where I practice the basics properly rather than assuming
defaults are fine: SSH key-based auth with password login disabled, unattended upgrades for
security patches, and host-level firewalling on top of the network segmentation. I also run a few
services in **Docker**, which has been a good excuse to get comfortable writing and troubleshooting
docker-compose YAML rather than clicking through a GUI.

## Windows AD + Kali

I built a small **Windows Server Active Directory** domain alongside a **Kali Linux** box so I can
look at identity infrastructure from both sides, setting up group policy, users and OUs on the
defensive side, and testing against my own domain from Kali on the offensive side. Most of my day
job is defending Microsoft 365 tenants I don't control the underlying AD for, so this is where I
get to see the parts of the identity stack that sit underneath what I normally manage.

> **What's next:** planning to add a proper SIEM (looking at Wazuh) to start centralising logs
> from across the lab, partly to get hands-on with a SIEM I don't currently use at work.
