# MaddieHomelabDocumentationRepo
This holds a copy of my homelab documentation.

🌐 The Maddie Homelab: Enterprise-Grade Infrastructure in an IKEA LACK FrameWelcome to the documentation repository for the Maddie Homelab. This project is a dual-site, highly redundant, and fully documented personal network infrastructure. It is built to simulate enterprise storage topologies, containerized virtualization environments, and disaster-resilient systems on a budget.Key HighlightsDual-Site Architecture: Onsite production node coupled with a secure offsite backup target located on a secondary WAN.Active 3-2-1 Disaster Recovery: Automated incremental snapshots, containerized multi-site directory synchronization, and verified bare-metal recovery workflows.Software-Defined Networking: Seamless inter-site routing and remote administration orchestrated via a secure Tailscale SDN mesh network and Cloudflare tunnels.Sustainably Engineered: Built using recycled, custom-procured, and cascaded hardware mounted within a bespoke, customized dual-tier IKEA LACK rack frame.🏢 Physical Infrastructure & SpecificationsThe homelab spans two distinct physical sites connected via a secure, encrypted overlay network.   [ ONSITE PRODUCTION - Bilston ]                 [ OFFSITE REPLICATION - External WAN ]
+------------------------------------+             +------------------------------------+
|  Virgin Media Hub (Modem Mode)     |             |  Sky Fibre Hub (WAN Gateway)       |
|                 |                  |             |                 |                  |
|  TP-Link Archer AXE75 Router       |             |  TP-Link Wi-Fi Extender (Client)   |
|                 |                  |             |                 |                  |
|  HISOURCE Multi-Gig Switches       |             |  TP-Link Gigabit Switch            |
|       /         |          \       |             |                 |                  |
|   [Uzi]      [Adele]     [Juno]    |             |              [Aria]                |
|  Proxmox     TrueNAS     AI Node   |             |           TrueNAS Backup           |
|  VM/LXCs     Storage               |             |                                    |
+------------------------------------+             +------------------------------------+
                  \                                                 /
                   ================== Tailscale VPN Mesh ===========
Onsite Production Environment (Bilston, UK)Rack Frame: 2x Stacked & Reinforced IKEA LACK tables acting as a standard 19-inch width alternative rack cabinet.Power Delivery & Conditioning: CyberPower CP1500EPFCLCD-UK Uninterruptible Power Supply (UPS) rated at 1500VA/900W providing pure sine-wave backup power to all production hosts.Core Routing: TP-Link Archer AXE75 AXE5400 Tri-Band Wi-Fi 6E Router.Switching Fabric:Rack Switch: HISOURCE 8-Port 2.5GbE Multi-Gigabit Switch with dual 10GbE SFP+ uplinks (optical interconnect to desk).Desk Switch: HISOURCE 4-Port 2.5GbE Multi-Gigabit Switch with dual 10GbE SFP+ uplinks.ISP Gateway: Virgin Media Hub 3 (configured in Modem Mode to eliminate double-NAT and hand off all routing logic to the core TP-Link router).Offsite Replication & Disaster Recovery TargetPhysical Housing: Dedicated bottom-shelf enclosure inside a modified IKEA BILLY bookcase.Gateway: Sky Fibre WAN.Internal Networking: TP-Link TL-LS1005G 5-Port Gigabit Network Switch feeding off an isolated wireless-to-ethernet bridge.💻 Current Hardware InventoryOnsite Compute & Storage Nodes📦 Uzi - Always-On Application Virtualization HostAn ultra-quiet, high-density compute node running a bare-metal hypervisor, modified with ziptied custom intake fans (Arctic P12 MAX/P8 MAX series) for high continuous workloads.Platform: HP Pro SFF 400 G9CPU: Intel Core i5-13500 (14 Cores / 20 Threads, featuring Intel UHD 770 QuickSync for accelerated transcoding)RAM: 32GB DDR4 3200MHzStorage:System/VM Root: Seagate FireCuda 530 2TB PCIe 4.0 NVMe SSDLocal Cold Pool: WD Ultrastar DC HC320 8TB Enterprise SATA HDDScratch/Fast Cache: Samsung 960GB M.2 PCIe NVMe SSDOS: Proxmox VE (PVE)🗄️ Adele - Primary Network-Attached StorageA high-capacity, custom-built storage array housed in a high-airflow Fractal Design Define 7 XL chassis, engineered for quiet, continuous data integrity.CPU: Intel Core i5-13500 (14 Cores)Cooling: Thermalright Peerless Assassin 120 SEMotherboard: ASUS TUF Gaming B660-Plus WiFi D4RAM: 32GB DDR4 3200MHzStorage Pool:1x Seagate Exos X10 10TB Enterprise SATA HDD1x Western Digital Ultrastar DC HC330 10TB Enterprise SATA HDDBoot Pool: Samsung 850 PRO 128GB SATA SSDOS: TrueNAS SCALE🧠 Juno - Local Edge AI & Machine Learning NodeA specialized, ultra-compact compute node optimized for running localized, quantized Large Language Models (LLMs) asynchronously.Platform: GMKtec EVO-X1 Mini PCCPU: AMD Ryzen AI 9 HX 370 (featuring integrated Radeon 890M GPU)RAM: 64GB DDR5 8000MHz (shared unified memory for large model allocation)Storage: KIOXIA Exceria 1TB PCIe NVMe SSDNPU: AMD XDNA 2 Dedicated NPU (up to 50 TOPS)OS: Windows 11 Pro (headless, configured with LM Studio for model serving)Offsite & Secondary Nodes💾 Aria - Offsite Backup Target NodeA resilient, high-capacity SAS array deployed offsite to receive replicated snapshots over a secure mesh tunnel.CPU: Intel Core i5-10505 (6 Cores)Motherboard: Gigabyte H410M S2RAM: 24GB DDR4 (mismatched configurations tested and verified for stability)Host Bus Adapter: LSI SAS HBA (flashed to IT Mode for direct ZFS drive access)Storage Pool:3x Seagate/HP 8TB 3.5" SAS Enterprise Hard Drives configured in RAIDZ1Boot Pool: Samsung SM843TN 256GB SSDCase: Fractal Design Node 804 (modified for custom high-density drive configuration)OS: TrueNAS SCALE📟 Coffee - Offsite Management & Edge ControllerA low-power, single-board computer running edge services to coordinate wake-on-LAN commands and verify node reachability.Platform: Raspberry Pi 4b (4GB RAM) in a cooling chassisOS: Ubuntu ServerPrimary Containers: UpSnap (Wake-on-LAN Dashboard), Machine Sanity Checkers🚀 Nori - Mobile Tactical TerminalA rugged, high-performance laptop used as the primary remote terminal for hypervisor and storage administration.Platform: MSI Katana GF66Specs: Intel Core i7-11800H | 24GB DDR4 3200MHz | RTX 3060 Laptop GPU | 2TB FireCuda NVMe SSD | Intel AX210 Wi-Fi 6E UpgradeOS: Dual-Boot Windows 11 Home / Linux administration tools💿 Jack - Optical Archiving NodeA custom system engineered for ripping, transcribing, and digitizing physical optical media collections.Platform: Intel Core i3-10100 | 8GB RAM | Corsair 4000D AirflowOptical Assembly: 3x Simultaneous DVD-RW / BD-ROM Drives (PLDS, HP, TSSTcorp)OS: Automated ripping suite🛜 Network Architecture & AddressingCore Onsite IPv4 Subnet AllocationsHost DevicePhysical PortAddressing TypeLocal IP AddressTailscale VPN IPInterconnect SpeedConnected TargetVirgin Hub 3COAXWANPublic IPN/A250 MbpsISP EndpointVirgin Hub 3LAN1Static (Modem)192.168.0.1N/A100 Mbps (Powerline)HISOURCE RACKTP-Link AXE75WAN1WAN192.168.1.1N/A100 MbpsVirgin Hub 3Google PixelWi-Fi 6EDynamic (DHCP)192.168.1.54100.118.0.996 GHz WirelessTP-Link AXE75NoriWi-Fi 6EDynamic (DHCP)192.168.1.18100.102.104.1066 GHz WirelessTP-Link AXE75AdeleEthernet 1Dynamic (DHCP)192.168.1.125100.97.105.1072.5 GbEHISOURCE RACKJunoEthernet 2Dynamic (DHCP)192.168.1.68100.125.178.762.5 GbEHISOURCE RACKUziEthernet 3Dynamic (DHCP)192.168.1.197100.96.124.622.5 GbEHISOURCE RACKCoffeeEthernet 7Dynamic (DHCP)192.168.1.74100.76.50.631 GbEHISOURCE RACKHypervisor Virtualization Architecture (Uzi Proxmox Virtual Networking)[ PCIe i226-V (2.5GbE Physical NIC) ]
                 |
             [ vmbr0 ] (Virtual Bridge)
                 |
  +--------------+--------------+--------------+---------------+--------------+
  | (vnic)       | (vnic)       | (vnic)       | (vnic)        | (vnic)       |
[LXC Immich]  [LXC Jellyfin] [LXC Matrix] [VM Arrstack] [LXC Share]  [LXC Tailscale]
 (Photo Sync)  (Transcoding)  (Matrix Chat) (Media Grabber) (Local SMB) (Exit Node/VPN)
Guest Container/VMVirtual InterfaceIP AssignmentLocal Port MappingTailscale IP MappingLocal Service FunctionSillyTavern (LXC)vmbr0Dynamic (DHCP)192.168.1.162:8000100.96.124.62:8000AI LLM FrontendImmich (LXC)vmbr0Dynamic (DHCP)192.168.1.57:2283100.96.124.62:2283Media Backup ArrayNordscale (LXC)vmbr0Dynamic (DHCP)192.168.1.30100.96.124.63Network Tunnel RoutingJellyfin (LXC)vmbr0Dynamic (DHCP)192.168.1.148:8096100.96.124.62:8096GPU Accelerated StreamerCloudflared (LXC)vmbr0Dynamic (DHCP)192.168.1.188N/ACloudflare Tunnel daemonMatrixChat (LXC)vmbr0Dynamic (DHCP)192.168.1.77OfflineLocal Chat serverNetworkShare (LXC)vmbr0Static192.168.1.56N/ALocal File share mountArrstack (VM)vmbr0Dynamic (DHCP)192.168.1.133100.73.236.74Automated media downloader🚨 Disaster Recovery & Incident Post-MortemCase Study: Incident Report 11-04-2026Incident Classification: Critical Metadata Storage Layer Failure / Hardware MalfunctionDate of Event: 09/04/2026 at approximately 20:54 BSTSystem Affected: Adele Primary Storage ZFS Pool (AdeleHDD)Total Recovery Rate: 100% Data Restored, 0% Data LossRoot Cause Analysis (RCA)During an automated Docker process involving a heavy video transcode on local media, Adele suffered a total PCIe enumeration failure on the motherboard level. The storage array utilized two enterprise Samsung PM983 NVMe SSDs in a mirrored configuration as a dedicated ZFS metadata allocation class.The motherboard's PCIe bus lost physical connection with the SSD controller, dropping both drives from the bus simultaneously. This resulted in the immediate corruption and un-mountable state of the primary AdeleHDD ZFS pool.ZFS Recovery Logs & AnomaliesDuring initial automated system restoration attempts, the ZFS storage daemon registered anomalous resilvering statistics caused by the corrupted metadata pointers:Alert: 138.05% Resilvered, 1 Second Remaining (during AdeleHDD initial auto-resilver)Alert: 505.66% Resilvered, 2 Minutes 50 Seconds Remaining (secondary cycle)Total Registered ZFS Read/Write Errors: 6,551nvme0n1 controller reporting 1,125,457 hardware interface faults.Incident Timeline & Resolution WorkflowHardware Isolation: Removed both suspect Samsung PM983 metadata drives from the motherboard slots. The motherboard was isolated, tested, confirmed faulty, and processed for RMA with the vendor (Case Number: 3563536).Pool Reconstruction: Replaced the underlying host infrastructure. Re-created the ZFS array on Adele with a strict architectural decision to discontinue the use of hardware-attached flash storage for ZFS metadata pools on consumer platforms.Data Recovery Strategy: Formulated a remote disaster recovery replication pipe. Initiated a standard rsync restoration script pulling incremental archives from the offsite disaster recovery node (Aria) over the secure Tailscale VPN mesh.Verification: Validated directory structures, cross-referenced file checksums, and executed parity scrubs to ensure database restoration for all local apps (including Immich metadata and local file assets).🎨 Architectural Naming Conventions & PhilosophiesThe naming scheme of the homelab follows an internal naming standard derived from character traits, performance tiers, and creative design archetypes.Frank Series (Legacy Compute): Named in tribute to classic technical-diy subcultures. The series spanned generations from a Packard Bell Atom processor up to a Ryzen 9 5950X workstation. It represents structural development and rapid prototyping.Adele Series (Storage Centric): Named for classic structural aesthetics. Built as the cornerstone file server, prioritizing stability, raw disk count, and robust throughput over compute power.Aria (Offsite Replication): The offsite "silent guardian" node, designed to sleep until queried, operating remotely with high-density SAS disk arrays.Uzi (Production Compute): Named after the highly active, fast-firing, compact protagonist of Murder Drones. True to its namesake, it is a small form factor (SFF) machine that drives almost all core container workloads and real-time processing pipelines.Coffee (System Heartbeat/Management): Built on a micro-chassis (Raspberry Pi), acting as the reliable, nimble node that triggers Wake-on-LAN commands and keeps higher-draw nodes powered down until requested.Juno (Unified AI Environment): Focused entirely on local machine learning operations. Capable of handling high-context modern model serving in a small form factor.📜 Core Infrastructure PoliciesStrict Anti-CCA Policy: To maintain signal integrity and ensure power safety, the Maddie Homelab enforces a absolute ban on Copper Clad Aluminium (CCA) networking cables. All deployed paths are verified 100% Solid Bare Copper (Cat6/Cat6a/Cat8).Asynchronous Configuration Management: Any changes to production hypervisor networks must first be documented and matched in the offline logical topography diagram before execution to minimize configuration drift.Active Redundancy Rule: No production databases may run exclusively on a single host. All files must replicate to the local TrueNAS pool (Adele) and subsequently sync with the offsite storage array (Aria).📅 Roadmap & Future Actions[ ] Complete full verification check of Uzi's Proxmox Container file systems.[ ] Finalize ASUS Motherboard RMA status and rebuild primary Adele SAS control board.[ ] Integrate physical VESA Monitor Arm console directly into the central LACK rack system.[ ] Migrate local markdown architecture notes into a public GitBook site hosted at themaddiehomelab.co.uk.


🛜 Network Architecture & Addressing

Core Onsite IPv4 Subnet Allocations

Host Device

Physical Port

Addressing Type

Local IP Address

Tailscale VPN IP

Interconnect Speed

Connected Target

Virgin Hub 3

COAX

WAN

Public IP

N/A

250 Mbps

ISP Endpoint

Virgin Hub 3

LAN1

Static (Modem)

192.168.0.1

N/A

100 Mbps (Powerline)

HISOURCE RACK

TP-Link AXE75

WAN1

WAN

192.168.1.1

N/A

100 Mbps

Virgin Hub 3

Google Pixel

Wi-Fi 6E

Dynamic (DHCP)

192.168.1.54

100.118.0.99

6 GHz Wireless

TP-Link AXE75

Nori

Wi-Fi 6E

Dynamic (DHCP)

192.168.1.18

100.102.104.106

6 GHz Wireless

TP-Link AXE75

Adele

Ethernet 1

Dynamic (DHCP)

192.168.1.125

100.97.105.107

2.5 GbE

HISOURCE RACK

Juno

Ethernet 2

Dynamic (DHCP)

192.168.1.68

100.125.178.76

2.5 GbE

HISOURCE RACK

Uzi

Ethernet 3

Dynamic (DHCP)

192.168.1.197

100.96.124.62

2.5 GbE

HISOURCE RACK

Coffee

Ethernet 7

Dynamic (DHCP)

192.168.1.74

100.76.50.63

1 GbE

HISOURCE RACK
