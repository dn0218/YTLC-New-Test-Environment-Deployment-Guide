# YTLC Project – New Test Environment Deployment Guide

This document describes the steps to deploy a new test environment for the YTLC project, including database setup (reusing an existing Oracle server), VM resource planning, network configuration, and data migration.

---

## Overview

The goal is to provision a **new test environment** on-site with:

- **Reused database server** (IP: `10.26.128.2`) – new schemas/tablespaces alongside existing ones.
- **Application VMs** replicating the existing testbed configuration, with possible resource adjustments.
- **Data migration** from the current test environment (schemas `POT`, `CRM`, `CC_REPORT`, etc.) to new schemas (suffixed with `2`) using Oracle Data Pump.

---

## Environment Architecture

All components reside in the `10.26.128.0/24` network. The database server is shared; application VMs are separate.

| Role                  | Hostname          | IP Address (ens3) | IP Address (ens4) | Gateway (ens3) | Gateway (ens4) |
|-----------------------|-------------------|-------------------|-------------------|----------------|----------------|
| OCS / OLC             | `ytldevocs01`     | 10.26.128.11      | 10.26.128.161     | 10.26.128.1    | 10.26.128.129  |
| MED / Settlement      | `ytldevmed01`     | 10.26.128.12      | 10.26.128.162     | 10.26.128.1    | 10.26.128.129  |
| CVBS / APIG           | `ytldevcvbs01`    | 10.26.128.13      | 10.26.128.163     | 10.26.128.1    | 10.26.128.129  |
| Provisioning          | `ytldevprov01`    | 10.26.128.14      | 10.26.128.164     | 10.26.128.1    | 10.26.128.129  |
| Stat Tool             | `ytldevstat01`    | 10.26.128.15      | 10.26.128.165     | 10.26.128.1    | 10.26.128.129  |
| Zcache                | `ytldevtools01`   | 10.26.128.16      | 10.26.128.166     | 10.26.128.1    | 10.26.128.129  |
| PCRF / PHUB           | `ytldevpcrf01`    | 10.26.128.17      | 10.26.128.167     | 10.26.128.1    | 10.26.128.129  |

> **Note**: The database server `10.26.128.2` is not listed above; it is shared among all application VMs.

---

## Database Server – Reuse Strategy

Only one database server (`10.26.128.2`) is available for the new test environment.  
We will **not create a separate instance**; instead we will create **new schemas (users)** and **new tablespaces** alongside the existing ones.

- **Separation**: Users and tablespaces use the `2` suffix (e.g., `POT2`, `TAB_CC2`).
- **Benefits**:
  - Minimal overhead – no additional Oracle instance.
  - Easy data migration using `remap_schema` and `remap_tablespace`.
  - Clear isolation from existing test data.

---

## Database Setup

All SQL commands below are executed as `sysdba` on `10.26.128.2`.
