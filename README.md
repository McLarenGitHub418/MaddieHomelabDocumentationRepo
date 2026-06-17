# MaddieHomelabDocumentationRepo
This holds a copy of my homelab documentation.
Maddie Homelab Network & Storage Architecture

Enterprise System Documentation & Disaster Recovery Plan

Author: Joshua Tharp

Current Revision: June 2026

Infrastructure Target: High-Availability Dual-Site 3-2-1 Disaster Recovery Architecture

1. Architectural Overview

The Maddie Homelab is a dual-site, highly integrated network, virtualization, and storage infrastructure designed to facilitate secure, high-throughput local services, local LLM/AI model execution, and media archiving.

The primary site utilizes a hyperconverged virtualization architecture alongside automated offsite replication over a secure Tailscale mesh VPN to achieve a fully realized 3-2-1 backup strategy with $100\%$ restoration capabilities.

       +-------------------------------------------------------+
       |                  ONSITE (Primary)                     |
       |                                                       |
       |  [ Virgin Media Hub 3 (Bridged) ]                     |
       |                 |                                     |
       |  [ TP-Link Archer AXE75 Router ]                      |
       |                 |                                     |
       |  [ HiSource 10GbE Switch Stack ]                      |
       |         |                 |                           |
       |    [ Uzi (PVE) ]     [ Adele (Primary NAS) ]          |
       |    - VMs & LXCs       - TrueNAS SCALE                 |
       +---------|-----------------|---------------------------+
                 |                 |
            [Tailscale VPN]   [Syncthing] (Secure Replication)
                 |                 |
       +---------v-----------------v---------------------------+
       |                  OFFSITE (DR Target)                  |
       |                                                       |
       |  [ TP-Link 2.4GHz Wi-Fi Bridge ]                      |
       |                 |                                     |
       |  [ TP-Link TL-LS1005G Switch ]                        |
       |                 |                                     |
       |  [ Aria (Backup NAS) ]                                |
       |  - TrueNAS SCALE (SAS Arrays)                         |
       +-------------------------------------------------------+


2. Device Catalog & Intended Purposes

Onsite Production Infrastructure

Uzi (Hypervisor Host): An HP Pro SFF 400 G9 running Proxmox VE. Serves as the primary compute node, running containerized Linux (LXC) microservices, VPN exit nodes, and automated pipeline scripts.

Adele (Primary NAS): A Fractal Define 7 XL tower running TrueNAS SCALE. Functions as the primary network-attached storage targets (SMB/NFS) and local backup repository.

Juno (Local AI / Workstation): A high-performance GMKtec Mini PC running Windows 11 Pro. Utilized for local large language model (LLM) execution ($31\text{B}$ parameter models at $16,384$ context) leveraging integrated NPU and GPU acceleration.

Coffee (Management Node): A Raspberry Pi 4b running Ubuntu Server. Acts as an out-of-band management node, hosting containerized LAN wakers (UpSnap) and heartbeat sanity monitoring.

Jack (Media Ingest Node): Custom client system housing multiple optical storage drives, functioning as an ingestion and automated ripping station for archival media.

Nori (Mobile Administrator Terminal): High-spec MSI Katana laptop. Acts as the primary terminal interface for administrative tasks, on-site diagnostics, and system orchestration.

Offsite Disaster Recovery Infrastructure

Aria (Disaster Recovery Target): A custom Node 804 server located offsite running TrueNAS SCALE. Equipped with enterprise SAS drive arrays configured in a redundant RAIDZ1 setup. Serves as the automated offsite replication partner for critical datasets over Tailscale.

3. Technical System Specifications

Storage & Compute Nodes

Host Name

Form Factor / Case

Processor

RAM

Storage / File System Configurations

OS / Hypervisor

Uzi

HP Pro SFF G9

Intel Core i5-13500

32GB DDR4

2TB NVMe (System/VMs) + 960GB NVMe + 8TB SATA HDD

Proxmox VE

Adele

Fractal Define 7 XL

Intel Core i5-13500

32GB DDR4

Mixed Enterprise SAS/SATA (ZFS Mirrors)

TrueNAS SCALE

Aria (Offsite)

Fractal Node 804

Intel Core i5-10505

24GB DDR4

256GB SSD (Boot) + 2x 8TB SAS (RAIDZ1)

TrueNAS SCALE

Juno

GMKtec Mini PC

Ryzen AI 9 HX 370

64GB LPDDR5

1TB NVMe SSD

Windows 11 Pro

Coffee

Raspberry Pi 4b

ARM Cortex-A72

4GB LPDDR4

32GB MicroSD

Ubuntu Server

4. Network Configuration & Topology

Onsite Network Architecture (10GbE Core)

The onsite backbone is architected using high-speed multi-gigabit switches utilizing SFP+ optical transceiver links to establish a 10GbE network core between the primary hypervisor node and storage node.

Device Source

Interface

IP Assignment

Local IP

Tailscale IP

Connection

Speed

Destination

VM Gateway

WAN

Static WAN

192.168.0.1

N/A

Coaxial

250 Mbps

ISP Gateway

AXE75 Router

WAN1

Interlink

192.168.1.1

N/A

CAT8

100 Mbps

VM Gateway

HiSource Rack

SFP+2

Interlink

Switch Link

N/A

SFP+ AOC

10 Gbps

HiSource Desk

HiSource Rack

Eth1

DHCP

192.168.1.125

100.97.105.107

CAT6a

2.5 Gbps

Adele (NAS)

HiSource Rack

Eth2

DHCP

192.168.1.68

100.125.178.76

CAT6a

2.5 Gbps

Juno (AI)

HiSource Rack

Eth3

DHCP

192.168.1.197

100.96.124.62

CAT6a

2.5 Gbps

Uzi (PVE)

HiSource Desk

SFP+1

Interlink

Switch Link

N/A

SFP+ AOC

10 Gbps

HiSource Rack

HiSource Desk

Eth1

DHCP

192.168.1.232

100.102.104.106

CAT8

2.5 Gbps

Nori (Admin)

Virtual Networking & Container Mapping (Proxmox VE)

A single physical NIC (i226-V) is bound to the Linux Virtual Bridge vmbr0 inside Proxmox, hosting various virtual machines and isolated Linux Containers (LXCs):
Container Name
Virtual Interface
Local IP Address
Port Bindings
Tailscale IP
Service Description
LXC Immich
vnic
192.168.1.57
2283
100.96.124.62:2283
Photo / Asset Archival platform
LXC Jellyfin
vnic
192.168.1.148
8096
100.96.124.62:8096
Local Media Streaming Server
LXC Nordscale
vnic
192.168.1.30
N/A
100.96.124.63
Secure Outbound Exit Node
LXC Matrix
vnic
192.168.1.77
N/A
Offline
Self-hosted Secure Chat Server
LXC Handbrake
vnic
192.168.1.72
5800
N/A

Automated Transcoding Engine
VM Arrstack
vnic
192.168.1.133
Multiple
100.73.236.74
Automation & Content Retrieval

5. Disaster Recovery Strategy (3-2-1)

The backup policy ensures redundant, crash-consistent datasets through a multi-tiered layout:

3 Copies of Data: Primary production storage, a secondary local backup target, and an offsite target.
2 Different Mediums: Solid-State Disk arrays (NVMe/SATA) for hypervisor workloads, combined with Mechanical Enterprise SAS/SATA hard drives for cold storage pools.
1 Offsite Location: Daily replication tasks synchronized to Aria (the offsite disaster recovery NAS) running at an alternate geographical location over a Tailscale VPN mesh network utilizing secure Syncthing tunnels.

6. Incident Post-Mortem (INC-20260409-01)
Metadata Pool Isolation & Full Site Recovery
Incident ID: INC-20260409-01
Date of Event: 09/04/2026 @ 20:54 BST
Priority: P1 (Critical Outage)
Impacted Node: Adele (Primary NAS)

+------------------+     Motherboard PCIe Failure     +-----------------------+
|  Samsung PM983   | ===============================> |  Adele ZFS HDD Pool   |
|  Enterprise SSDs |     (Loss of Metadata VDEVs)     |    (Faulted State)    |
+------------------+                                  +-----------------------+
                                                                  |
                                                                  v
+------------------+          Tailscale VPN           +-----------------------+
|  Offsite Aria    | <=============================== |  Full Site Restore    |
|  ZFS HDD Pool    |     (rsync @ 100% Data Recov)    |  (New System Board)   |
+------------------+                                  +-----------------------+


Executive Summary

A severe PCIe bus enumeration failure on the primary system motherboard (ASUS TUF B660) caused the sudden unresponsiveness and hardware drop-out of two mirrored Samsung PM983 enterprise NVMe SSDs operating as the dedicated Special Metadata VDEV for the primary storage array (AdeleHDD). This unexpected loss of the metadata pool resulted in a critical cascading fault, rendering the primary filesystem unreadable and corrupted.

Root Cause Analysis (RCA)

Subsequent investigative testing verified a permanent hardware failure regarding PCIe channel negotiation on the motherboard's primary M.2 slots. Because a ZFS pool containing a dedicated Special Metadata VDEV becomes completely unrecoverable if the metadata VDEV fails or goes offline, the primary storage pool entered an unrecoverable faulted state.

Corrective Action & DR Execution
System De-isolation: Failed hardware was cleanly stripped down, isolated, andpackaged for RMA processing with SCAN Computers/ASUS under Case Number 3563536.
Disaster Recovery Protocol: Sourced temporary alternative hardware to re-establish system backplanes.
Offsite Recovery Execution: Initiated rsync restoration tasks directly from the offsite disaster recovery node (Aria) through a secure Tailscale VPN mesh network.
Verification: Validated directory structures, checksummed all restored media archives, and brought host services back online. Data recovery rate: $100\%$ ($0\text{ bytes}$ lost).

Remediation & Preventative Steps
Elimination of Special VDEVs: ZFS "Special" metadata classes have been permanently decommissioned on production pools to mitigate the risk of a single-point-of-failure crash-loop. High-frequency write-caching is now managed solely through standard SSD partitioning.

Enhanced Offsite Staggering: Upgraded cold-storage drives at the offsite DR site (Seagate ST8000 arrays) to ensure continuous backup retention limits.
