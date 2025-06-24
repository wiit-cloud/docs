---
title: Shutdown API
lang: "en"
permalink: /edge/addons/shutdown/
has_children: false
nav_order: 4200
parent: Addons
---

# Shutdown API

## Overview

The Shutdown API allows for a controlled shutdown of the edge environment in emergency situations (e.g., power outages). This process is automated, while the startup must be performed manually by our team.

**Important**: Please notify us after initiating a shutdown and provide the estimated recovery time.

All API endpoints require an authentication token, which can be obtained via our helpdesk.

## Shutdown process

When triggered, the Shutdown API performs the following steps:

* Sends a stop signal to all OpenStack virtual machines.
* Stops the storage layer.
* Powers down the hosts.

## Usage

Base URL: `https://shutdown.<EDGE>.wiit-edge.services/`

## Simulation

Use the simulation endpoint to test your integration without performing an actual shutdown. It connects to OpenStack, hosts, and BMC to simulate the process as closely as possible—without stopping any services.

**Note**: A new simulation can only be started once the current shutdown process is complete (`running: false` in status output).

### Trigger Simulation

```bash
curl -X POST -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/simulate
```

Response:

```json
{"data":"We've got your request","error":null,"status":"success"}
```

The status of the shutdown process can be retrieved via status endpoint (see below).

### Check Status

```bash
curl -X GET -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/simulate/status
```

Response Example:

```json
{
    "data": {
        "running": false,
        "start_time": "2025-06-24T07:47:04.065478",
        "current_step": "Description of the current step (only included while the shutdown process is running)",
        "error": "Last Error. Only included if an error happened during the last shutdown."
    },
    "error": null,
    "status": "success"
}
```

## Shutdown

Use this endpoint only when a real shutdown is required.

### Trigger Shutdown

```bash
curl -X POST -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/shutdown
```

### Check Shutdown Status

```bash
curl -X GET -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/shutdown/status
```
