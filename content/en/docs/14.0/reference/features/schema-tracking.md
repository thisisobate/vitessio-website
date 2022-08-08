---
title: Schema Tracking
weight: 16
aliases: ['/docs/reference/schema-tracking/']
---

VTGate does not natively track table schema. Users are allowed to provide an authoritative list of columns through a VSchema which is then used to enhance query planning. If no such list is provided, a set of queries will not be supported by VTGate due to a lack of information on the underlying tables/columns.

The schema tracking functionality alleviates this issue and enable VTGate to plan more queries. When using schema tracking, VTGate keeps an authoritative list of columns on all tables. The following query set can be planned:

1. `SELECT *` cross-shard queries that need evaluation at the VTGate level.
2. Queries which reference columns without a table qualifier as long as the column names are unique across the tables involved in the query.
3. Evaluation improvement in Aggregations, Group By, Having, Limit, etc. clauses that require processing of records at VTGate level. VTGate will not require `weight_string()` value for the evaluation and can compare the values directly. 

## VTGate

Schema tracking is enabled in VTGate with the flag `--schema_change_signal`. When enabled, VTGate listens for schema changes from VTTablet.
A change triggers a fetch query on vttablet on internal _vt schema.
If the Table ACL is enabled, then an exempted/allowed username needs to be passed to VTGate with flag `--schema_change_signal_user`.

## VTTablet

Schema tracking is enabled in VTTablet with the flag `--queryserver-config-schema-change-signal`. When enabled, VTTablet sends schema changes to VTGate based on an interval that can be modified with the flag `--queryserver-config-schema-change-signal-interval` (defaults to 5 seconds).
