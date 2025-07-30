---
title: Designate DNSaaS 
lang: "en"
permalink: /openstack/DNS/
nav_order: 8999
---

# Create a DNS record in Designate

## Start

The Openstack OpenStack Platform includes a technology called DNS-as-a-Service (DNSaaS), also known as Designate.
DNSaaS includes a REST API for domain and records management, is multi-tenant and integrates the OpenStack Identity Service (Keystone) for authentication.

In this step, we will create a fictitious zone (domain) with MX and A records and store the appropriate IP/CNAME.

-----

The first step is to ensure that the `python-designateclient` is installed (pip install python-openstackclient python-designateclient)
The next step is to create a zone for our project:

```bash
$ openstack zone create --email webmaster@foobar.cloud foobar.cloud.
+----------------+--------------------------------------+
| Field          | Value                                |
+----------------+--------------------------------------+
| action         | CREATE                               |
| attributes     |                                      |
| created_at     | 2018-08-15T06:45:24.000000           |
| description    | None                                 |
| email          | webmaster@foobar.cloud               |
| id             | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| masters        |                                      |
| name           | foobar.cloud.                        |
| pool_id        | bb031d0d-b8ca-455a-8963-50ec70fe57cf |
| project_id     | 2b62bc8ff48445f394d0318dbd058967     |
| serial         | 1534315524                           |
| status         | PENDING                              |
| transferred_at | None                                 |
| ttl            | 3600                                 |
| type           | PRIMARY                              |
| updated_at     | None                                 |
| version        | 1                                    |
+----------------+--------------------------------------+
```

Note the trailing "." is required for the zone/domain to be created.
The result so far:

```bash
$ openstack zone list
+--------------------------------------+-----------------------+---------+------------+--------+--------+
| id                                   | name                  | type    |     serial | status | action |
+--------------------------------------+-----------------------+---------+------------+--------+--------+
| 036ae6e6-6318-47e1-920f-be518d845fb5 | foobar.cloud.         | PRIMARY | 1534315524 | ACTIVE | NONE   |
+--------------------------------------+-----------------------+---------+------------+--------+--------+

$ openstack zone show foobar.cloud.
+----------------+--------------------------------------+
| Field          | Value                                |
+----------------+--------------------------------------+
| action         | NONE                                 |
| attributes     |                                      |
| created_at     | 2018-08-15T06:45:24.000000           |
| description    | None                                 |
| email          | webmaster@foobar.cloud               |
| id             | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| masters        |                                      |
| name           | foobar.cloud.                        |
| pool_id        | bb031d0d-b8ca-455a-8963-50ec70fe57cf |
| project_id     | 2b62bc8ff48445f394d0318dbd058967     |
| serial         | 1534315524                           |
| status         | ACTIVE                               |
| transferred_at | None                                 |
| ttl            | 3600                                 |
| type           | PRIMARY                              |
| updated_at     | 2018-08-15T06:45:30.000000           |
| version        | 2                                    |
+----------------+--------------------------------------+
```

The domain "foobar.cloud" is now registered and ready to use (status: ACTIVE) for our project.
In the next step, we want to create MX records (records for mail servers in this zone) for this domain.

But first let's see the content (recordsets) that already exist in your new zone.

```bash
$ openstack recordset list foobar.cloud.
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+--------+--------+
| id                                   | name          | type | records                                                                        | status | action |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+--------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud. | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE | NONE   |
|                                      |               |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |        |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud. | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534315524 3507 600 86400 3600 | ACTIVE | NONE   |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+--------+--------+
```

Here you see an "empty shell" of a domain with automatically generated NS and SOA records which are ready to be queried.

```bash
$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud NS
ns1.openstack.de-west-01.wiit-cloud.io.
ns2.openstack.de-west-01.wiit-cloud.io.

$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud SOA
ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534315524 3507 600 86400 3600
```

Creating an MX record:
You can now add, modify, or delete records within this zone (openstack recordset --help).
For MX records we also set up the typical mail server priorities (10,20), where the lower value is always selected first
and the second entry serves as a "backup".

```bash
$ openstack recordset create --record '10 mx1.foobar.cloud.' --record '20 mx2.foobar.cloud.' --type MX foobar.cloud. foobar.cloud.
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:15:32.000000           |
| description | None                                 |
| id          | 4197e380-dc61-4e1d-bf92-0dcf5344eafa |
| name        | foobar.cloud.                        |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 10 mx1.foobar.cloud.                 |
|             | 20 mx2.foobar.cloud.                 |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | MX                                   |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+

$ openstack recordset list foobar.cloud.
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+---------+--------+
| id                                   | name          | type | records                                                                        | status  | action |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+---------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud. | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE  | NONE   |
|                                      |               |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |         |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud. | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534317332 3507 600 86400 3600 | PENDING | UPDATE |
| 4197e380-dc61-4e1d-bf92-0dcf5344eafa | foobar.cloud. | MX   | 20 mx2.foobar.cloud.                                                           | PENDING | CREATE |
|                                      |               |      | 10 mx1.foobar.cloud.                                                           |         |        |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+---------+--------+

```

```bash
$ openstack recordset create --type A --record 1.2.3.4 foobar.cloud. www
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:28:15.000000           |
| description | None                                 |
| id          | d932688f-21d5-44b1-aa27-030c342788e7 |
| name        | www.foobar.cloud.                    |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 1.2.3.4                              |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | A                                    |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+
```

Result:

```bash
$ openstack recordset list foobar.cloud.
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| id                                   | name              | type | records                                                                        | status | action |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud.     | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE | NONE   |
|                                      |                   |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |        |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud.     | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534318095 3507 600 86400 3600 | ACTIVE | NONE   |
| 4197e380-dc61-4e1d-bf92-0dcf5344eafa | foobar.cloud.     | MX   | 20 mx2.foobar.cloud.                                                           | ACTIVE | NONE   |
|                                      |                   |      | 10 mx1.foobar.cloud.                                                           |        |        |
| d932688f-21d5-44b1-aa27-030c342788e7 | www.foobar.cloud. | A    | 1.2.3.4                                                                        | ACTIVE | NONE   |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
```

Once the recordsets are active you can use the designated DNS servers:

* ns1.openstack.de-west-01.wiit-cloud.io
* ns2.openstack.de-west-01.wiit-cloud.io

Query for these records:

```bash
$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud NS
ns1.openstack.de-west-01.wiit-cloud.io.
ns2.openstack.de-west-01.wiit-cloud.io.

$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud MX
10 mx1.foobar.cloud.
20 mx2.foobar.cloud.

$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud www.foobar.cloud
1.2.3.4
```

> ATTENTION! At this time, this domain (foobar.cloud) is not yet resolvable worldwide.

For this construct to be used worldwide, each domain managed by Designate must have delegation to the name servers `ns1.openstack.de-west-01.wiit-cloud.io` and `ns2.openstack.de-west-01.wiit-cloud.io` established by the respective registrar.

> Details about our authoritative DNS servers:
>
> * ns1.openstack.de-west-01.wiit-cloud.io: '85.14.241.161' / '2001:4ba0:133d::53'
> * ns2.openstack.de-west-01.wiit-cloud.io: '85.14.241.162' / '2001:4ba0:133d::5353'

In order to complete the mail records, it is still advisable to store corresponding A-records for the mail servers

```bash
$ openstack recordset create --type A --record 2.3.4.5 foobar.cloud. mx1
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:42:31.000000           |
| description | None                                 |
| id          | 630d5103-7c02-4a58-83a5-97f802cf141c |
| name        | mx1.foobar.cloud.                    |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 2.3.4.5                              |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | A                                    |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+
and
$ openstack recordset create --type A --record 3.4.5.6 foobar.cloud. mx2
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:42:56.000000           |
| description | None                                 |
| id          | 2b47dbe4-70b9-4edb-ac2f-25cb8398bacb |
| name        | mx2.foobar.cloud.                    |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 3.4.5.6                              |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | A                                    |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+
```

Result after a few seconds:

```bash
$ openstack recordset list foobar.cloud.
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| id                                   | name              | type | records                                                                        | status | action |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud.     | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE | NONE   |
|                                      |                   |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |        |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud.     | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534318976 3507 600 86400 3600 | ACTIVE | NONE   |
| 4197e380-dc61-4e1d-bf92-0dcf5344eafa | foobar.cloud.     | MX   | 20 mx2.foobar.cloud.                                                           | ACTIVE | NONE   |
|                                      |                   |      | 10 mx1.foobar.cloud.                                                           |        |        |
| d932688f-21d5-44b1-aa27-030c342788e7 | www.foobar.cloud. | A    | 1.2.3.4                                                                        | ACTIVE | NONE   |
| 630d5103-7c02-4a58-83a5-97f802cf141c | mx1.foobar.cloud. | A    | 2.3.4.5                                                                        | ACTIVE | NONE   |
| 2b47dbe4-70b9-4edb-ac2f-25cb8398bacb | mx2.foobar.cloud. | A    | 3.4.5.6                                                                        | ACTIVE | NONE   |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
```

## Reverse DNS 

Reverse DNS for ipv4 Floating IPs can be configured once a Floating IP is assigned to your project.
To set reverse DNS records for ipv6 Addresses the reverse zone for your ipv6 subnet needs to be created from WIIT AG and shared into your project.
Please contact our support and provide your ipv6 prefix and your project id.
After we created the zone you will recieve instructions to accept a zone transfer, afterwards you can manage ipv6 reverse records within that zone.
