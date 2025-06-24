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

The API can be used to shut down the edge in a controlled manner in case of an emergency (e.g. power outage) automatically.
The start-up process is manual and controlled by us. Please inform us after the shutdown and expected recovery time.

The endpoints are requiring a Token which can be obtained via helpdesk.

## Shutdown process

Once triggered, the shutdown API will:

* Send stop signal to all openstack VMs
* Stop the storage layer
* Shutdown the hosts

## Usage

Base URL: `https://shutdown.<EDGE>.wiit-edge.services/`

## Simulation

The simulation endpoint behaves the same way as the real shutdown endpoint. It connects to Openstack, Hosts and BMC to test as much as possible without stopping anything. This endpoint can be used for testing integrations to automate the process without stopping anything.

Note: A new simulation can only be started after the current shutdown has finished (`running: false` in status output).

```bash
curl -X POST -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/simulate
```

The endpoint will respond directly with:

```json
{"data":"We've got your request","error":null,"status":"success"}
```

The status of the shutdown process can be retrieved via status endpoint (see below).


### Status Endpoint

```bash
curl -X GET -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/simulate/status
```

The status endpoint can be used to retrieve the current state of the shutdown process. The reponse format is json. It's not needed to shut down the system.

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

The shutdown endpoint behaves the same way as the simulation endpoint and should only be used, if it is required to shut down the system.

```bash
curl -X POST -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/shutdown
curl -X GET -H 'auth-token: <TOKEN>' https://shutdown.<EDGE>.wiit-edge.services/shutdown/status
```
