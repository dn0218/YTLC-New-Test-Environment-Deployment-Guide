# Application VM Configuration

The VMs should be provisioned with resources similar to the existing testbed (see table below). If total host resources are limited, adjust CPU/RAM proportionally.

## VM Resource Summary (Based on Testbed)

| Role         | vCPU | RAM (GB) | App Storage (GB) | Key Users / Groups | Filesystem Mounts                |
|--------------|------|----------|------------------|--------------------|----------------------------------|
| OCS / OLC    | 16   | 128      | 600              | `ocs`, `olc`, `qmdb`, `chfa` | `/ocs`(50G), `/ocsdata`(200G), `/olc`(20G), `/qmdb`(50G), `/chfa`(20G) |
| MED          | 8    | 64       | 1000             | `med`, `medweb`, `sett`, `settweb` | `/med`(50G), `/meddata`(200G), `/setweb`(20G), `/setttdata`(200G) |
| CVBS / APIG  | 12   | 64       | 400              | `web` (multiple), `vc`          | `/web/*`, `/vc/*`, `/job/*` (see testbed table) |
| Provisioning | 8    | 32       | 400              | `spn`, `bcare`                   | `/spn`(50G), `/bcare`(50G)      |
| Stat Tool    | 8    | 48       | 400              | `apig`, `stc`, `jnc`, `tools`   | `/apig/*`, `/stc`(50G), `/jnc/*`, `/tools`(50G) |
| Zcache       | 8    | 32       | 300              | `tools`, `dmz`                   | `/tools`(50G), `/dmz`(20G)      |
| PCRF / PHUB  | 8    | 48       | 500              | `pcfweb`, `pcf`, `phub`, `tools`| `/pcfweb/*`, `/pcf`(50G), `/phub`(20G), `/tools`(50G) |

## Network & Hostname Mapping

| Hostname         | ens3 IP        | ens4 IP        | Purpose                     |
|------------------|----------------|----------------|-----------------------------|
| `ytldevocs01`    | 10.26.128.11   | 10.26.128.161  | OCS / OLC                   |
| `ytldevmed01`    | 10.26.128.12   | 10.26.128.162  | MED / Settlement            |
| `ytldevcvbs01`   | 10.26.128.13   | 10.26.128.163  | CVBS / APIG                 |
| `ytldevprov01`   | 10.26.128.14   | 10.26.128.164  | Provisioning                |
| `ytldevstat01`   | 10.26.128.15   | 10.26.128.165  | Stat Tool                   |
| `ytldevtools01`  | 10.26.128.16   | 10.26.128.166  | Zcache                      |
| `ytldevpcrf01`   | 10.26.128.17   | 10.26.128.167  | PCRF / PHUB                 |
