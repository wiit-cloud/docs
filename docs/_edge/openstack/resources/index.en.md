---
title: Resource Metrics
lang: "en"
permalink: /edge/openstack/metrics/
has_children: false
nav_order: 3200
parent: Openstack
---

# Metrics from Libvirt and Operations Center

## Overview

The feature enables the provision of metrics from Libvirt and the Operations Center.

These metrics include information on the use of flavors, CPU, RAM and other resources as well as data on the project owners.

## Endpoint

URL: `https://resources._EDGE_.gecgo.net/federate`

Credentials are required for access. These can be obtained from the helpdesk.

### Example configuration Prometheus

Further details in the Prometheus documentation on [Federation](https://prometheus.io/docs/prometheus/latest/federation/) and [scrape configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).

```yaml
scrape_configs:
  - job_name: 'edge_metrics'
    scrape_interval: 15s
    honor_labels: true
    metrics_path: '/federate'
    scheme: https
    basic_auth:
      username: scrape
      password_file: ./scrape_user_edge

    params:
      'match[]':
        - '{__name__=~".+"}'

    static_configs:
      - targets:
        - 'resources._EDGE_.gecgo.net'
```

## Metrics from Libvirt

The metrics from Libvirt provide detailed information about the virtual machines (VMs), including:

- **Flavors used:** Information about the allocated resource profiles of the VMs.
- **CPU usage:** Current utilization of the CPU resources.
- **RAM usage:** Current memory usage.
- **Other metrics:** Additional information about the VMs available via Libvirt.

## Metrics from the Operations Center

In addition to the Libvirt metrics, metrics about the project owners from the Operations Center are also provided. An example of such a metric is:

```plaintext
sql_oc_membership{job="oc-exporter",instance="de-host-rack-01",owner="jemand@userdomain.de",project_name="projectname-dea9e633-9a03-4130-900b-dfde07734bff"} 1
```

This metric contains the following information:

- **Job:** The name of the export job (oc-exporter).
- **Instance:** The instance ID (de-host-rack-01).
- **Owner:** The email address of the project owner (<someone@userdomain.de>).
- **Project Name:** The name of the project (projectname-dea9e633-9a03-4130-900b-dfde07734bff).
- **Value:** The value of the metric (1).

# Metrics

|Name|Description|Type|
|----|------------|---|
|libvirt_domain_block_meta|Block device metadata info. Device name, source file, serial.|gauge|
|libvirt_domain_block_stats_allocation|Offset of the highest written sector on a block device.|gauge|
|libvirt_domain_block_stats_capacity_bytes|Logical size in bytes of the block device backing image.|gauge|
|libvirt_domain_block_stats_flush_requests_total|Total flush requests from a block device.|counter|
|libvirt_domain_block_stats_flush_time_seconds_total|Total time in seconds spent on cache flushing to a block device|counter|
|libvirt_domain_block_stats_limit_burst_length_read_requests_seconds|Read requests per second burst time in seconds|gauge|
|libvirt_domain_block_stats_limit_burst_length_total_requests_seconds|Total requests per second burst time in seconds|gauge|
|libvirt_domain_block_stats_limit_burst_length_write_requests_seconds|Write requests per second burst time in seconds|gauge|
|libvirt_domain_block_stats_limit_burst_read_bytes|Read throughput burst limit in bytes per second|gauge|
|libvirt_domain_block_stats_limit_burst_read_bytes_length_seconds|Read throughput burst time in seconds|gauge|
|libvirt_domain_block_stats_limit_burst_read_requests|Read requests per second burst limit|gauge|
|libvirt_domain_block_stats_limit_burst_total_bytes|Total throughput burst limit in bytes per second|gauge|
|libvirt_domain_block_stats_limit_burst_total_bytes_length_seconds|Total throughput burst time in seconds|gauge|
|libvirt_domain_block_stats_limit_burst_total_requests|Total requests per second burst limit|gauge|
|libvirt_domain_block_stats_limit_burst_write_bytes|Write throughput burst limit in bytes per second|gauge|
|libvirt_domain_block_stats_limit_burst_write_bytes_length_seconds|Write throughput burst time in seconds|gauge|
|libvirt_domain_block_stats_limit_burst_write_requests|Write requests per second burst limit|gauge|
|libvirt_domain_block_stats_limit_read_bytes|Read throughput limit in bytes per second|gauge|
|libvirt_domain_block_stats_limit_read_requests|Read requests per second limit|gauge|
|libvirt_domain_block_stats_limit_total_bytes|Total throughput limit in bytes per second|gauge|
|libvirt_domain_block_stats_limit_total_requests|Total requests per second limit|gauge|
|libvirt_domain_block_stats_limit_write_bytes|Write throughput limit in bytes per second|gauge|
|libvirt_domain_block_stats_limit_write_requests|Write requests per second limit|gauge|
|libvirt_domain_block_stats_physicalsize_bytes|Physical size in bytes of the container of the backing image.|gauge|
|libvirt_domain_block_stats_read_bytes_total|Number of bytes read from a block device, in bytes.|counter|
|libvirt_domain_block_stats_read_requests_total|Number of read requests from a block device.|counter|
|libvirt_domain_block_stats_read_time_seconds_total|Total time spent on reads from a block device, in seconds.|counter|
|libvirt_domain_block_stats_size_iops_bytes|The size of IO operations per second permitted through a block device|gauge|
|libvirt_domain_block_stats_write_bytes_total|Number of bytes written to a block device, in bytes.|counter|
|libvirt_domain_block_stats_write_requests_total|Number of write requests to a block device.|counter|
|libvirt_domain_block_stats_write_time_seconds_total|Total time spent on writes on a block device, in seconds|counter|
|libvirt_domain_info_cpu_time_seconds_total|Amount of CPU time used by the domain, in seconds.|counter|
|libvirt_domain_info_maximum_memory_bytes|Maximum allowed memory of the domain, in bytes.|gauge|
|libvirt_domain_info_memory_usage_bytes|Memory usage of the domain, in bytes.|gauge|
|libvirt_domain_info_meta|Domain metadata|gauge|
|libvirt_domain_info_virtual_cpus|Number of virtual CPUs for the domain.|gauge|
|libvirt_domain_info_vstate|Virtual domain state. 0: no state, 1: the domain is running, 2: the domain is blocked on resource, 3: the domain is paused by user, 4: the domain is down, 5: the domain is shut off,6: the domain is crashed, 7: the domain is suspended by guest power management|gauge|
|libvirt_domain_interface_meta|Interfaces metadata. Source bridge, target device, interface uuid|gauge|
|libvirt_domain_interface_stats_receive_bytes_total|Number of bytes received on a network interface, in bytes.|counter|
|libvirt_domain_interface_stats_receive_drops_total|Number of packet receive drops on a network interface.|counter|
|libvirt_domain_interface_stats_receive_errors_total|Number of packet receive errors on a network interface.|counter|
|libvirt_domain_interface_stats_receive_packets_total|Number of packets received on a network interface.|counter|
|libvirt_domain_interface_stats_transmit_bytes_total|Number of bytes transmitted on a network interface, in bytes.|counter|
|libvirt_domain_interface_stats_transmit_drops_total|Number of packet transmit drops on a network interface.|counter|
|libvirt_domain_interface_stats_transmit_errors_total|Number of packet transmit errors on a network interface.|counter|
|libvirt_domain_interface_stats_transmit_packets_total|Number of packets transmitted on a network interface.|counter|
|libvirt_domain_memory_stats_actual_balloon_bytes|Current balloon value (in bytes).|gauge|
|libvirt_domain_memory_stats_available_bytes|The total amount of usable memory as seen by the domain. This value may be less than the amount of memory assigned to the domain if driver is in use or if the guest OS does not initialize all assigned pages. This value is expressed in bytes.|gauge|
|libvirt_domain_memory_stats_disk_cache_bytes|The amount of memory, that can be quickly reclaimed without additional I/O (in bytes).Typically these pages are used for caching disk|gauge|
|libvirt_domain_memory_stats_major_fault_total|Page faults occur when a process makes a valid access to virtual memory that is not available. When servicing the page fault, if is required, it is considered a major fault.|counter|
|libvirt_domain_memory_stats_minor_fault_total|Page faults occur when a process makes a valid access to virtual memory that is not available. When servicing the page not fault, IO is required, it is considered a minor fault.|counter|
|libvirt_domain_memory_stats_rss_bytes|Resident Set Size of the process running the domain. This value is in bytes|gauge|
|libvirt_domain_memory_stats_unused_bytes|The amount of memory left completely unused by the system. Memory that is available but used for reclaimable caches should NOT be free. This value is expressed in bytes.|gauge|
|libvirt_domain_memory_stats_usable_bytes|How much the balloon can be inflated without pushing the guest system to swap, corresponds to 'Available' in /proc/meminfo|gauge|
|libvirt_domain_memory_stats_used_percent|The amount of memory in percent, that used by domain.|gauge|
|libvirt_domain_vcpu_cpu|Real CPU number, or one of the values from virVcpuHostCpuState|gauge|
|libvirt_domain_vcpu_state|VCPU state. 0: offline, 1: running, 2: blocked|gauge|
|libvirt_domain_vcpu_time_seconds_total|Amount of CPU time used by the domain's VCPU, in seconds.|counter|
|libvirt_domain_vcpu_wait_seconds_total|Vcpu's wait_sum metric. CONFIG_SCHEDSTATS has to be enabled|counter|
|libvirt_up|Whether scraping libvirt's metrics was successful.|gauge|
|libvirt_versions_info|Versions of virtualization components|gauge|
|sql_oc_membership|Project owner information of Operations Center|counter|
