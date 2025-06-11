# KQL Hit Doc - The Complete Kusto Query Language (KQL) Reference

Welcome to the **KQL Hit Doc** – your all-in-one, charming, exhaustive, and battle-ready knowledge base on **Kusto Query Language (KQL)**. This document is crafted as both a learning journal and an advanced cheat sheet to empower you with everything from beginner syntax to advanced analytics. Ideal for Azure Monitor, Application Insights, Log Analytics, and more.

---

## 📚 Table of Contents

1. [Introduction to KQL](#introduction-to-kql)
2. [Core Concepts](#core-concepts)
3. [Basic Syntax](#basic-syntax)
4. [Query Operators](#query-operators)
5. [Data Types](#data-types)
6. [Data Ingestion and Schema](#data-ingestion-and-schema)
7. [Commonly Used Tables (Azure)](#commonly-used-tables-azure)
8. [Filtering, Sorting, Projection](#filtering-sorting-projection)
9. [Aggregation & Summarization](#aggregation--summarization)
10. [Joins and Lookups](#joins-and-lookups)
11. [Subqueries and Let Statements](#subqueries-and-let-statements)
12. [Time Series Analysis](#time-series-analysis)
13. [Rendering & Visualization](#rendering--visualization)
14. [Advanced Functions & Plugins](#advanced-functions--plugins)
15. [Security and Access Control](#security-and-access-control)
16. [Performance Tuning](#performance-tuning)
17. [Tips, Tricks, and Patterns](#tips-tricks-and-patterns)
18. [Real-life Scenarios and Examples](#real-life-scenarios-and-examples)
19. [Further Reading & References](#further-reading--references)

---

## 📖 Introduction to KQL

**Kusto Query Language** (KQL) is a read-only query language designed for big data analytics. Used primarily in Azure Monitor, Log Analytics, and Application Insights, it is optimized for interactive ad hoc queries.

* Developed by Microsoft for Azure Data Explorer.
* Declarative and SQL-like but tailored for telemetry data.
* Use-cases: performance monitoring, diagnostics, security analysis.

## 🧠 Core Concepts

* Everything is **table-based**.
* Queries are **pipeline-based**, flowing through `|` operators.
* Supports **structured**, **semi-structured**, and **time-series** data.
* Read-only: No data updates via KQL.

## 🧾 Basic Syntax

```kql
TableName
| where Condition
| summarize Count = count() by Column
| order by Count desc
```

* Case-sensitive.
* Whitespace and indentation improve readability but are not required.

## ⚙️ Query Operators

* `project`, `extend`, `summarize`, `where`, `order by`, `take`, `join`
* Control flow: `if`, `case`
* Type casting: `tostring()`, `toint()`, etc.

## 🔠 Data Types

* **Primitive**: `string`, `int`, `long`, `datetime`, `bool`, `real`, `guid`
* **Complex**: `dynamic` (JSON-like), `timespan`

## 📥 Data Ingestion and Schema

* In Azure, data is pushed into tables like `Perf`, `Heartbeat`, `SecurityEvent`, etc.
* Schema = columns + data types + metadata

Use `.show` and `.ingest` commands (in ADX context).

## 📊 Commonly Used Tables (Azure)

* `AzureActivity`, `SigninLogs`, `SecurityEvent`
* `Perf`, `Syslog`, `Heartbeat`, `AppRequests`
* `Usage`, `InsightsMetrics`

## 🔍 Filtering, Sorting, Projection

```kql
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Computer, Account
| order by TimeGenerated desc
```

## 📈 Aggregation & Summarization

```kql
SigninLogs
| summarize Failures = count() by bin(TimeGenerated, 1h), ResultType
```

* `count()`, `avg()`, `max()`, `sum()`, `percentiles()`, `make_list()`

## 🔗 Joins and Lookups

```kql
Table1
| join kind=inner (Table2) on CommonColumn
```

* `inner`, `leftouter`, `rightouter`, `fullouter`, `anti`, `semi`

## 🔄 Subqueries and Let Statements

```kql
let FailedLogins = SigninLogs | where ResultType != 0;
FailedLogins | summarize count() by UserPrincipalName
```

* Use `let` for modular, reusable logic.

## ⏱️ Time Series Analysis

```kql
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| summarize avg(CounterValue) by bin(TimeGenerated, 5m)
```

* Use `make-series`, `range`, `bin()`, and `serialize`

## 📊 Rendering & Visualization

```kql
Perf
| summarize avg(CounterValue) by bin(TimeGenerated, 5m)
| render timechart
```

* Types: `timechart`, `barchart`, `piechart`, `table`

## 🧮 Advanced Functions & Plugins

* **Math/Stats**: `percentile()`, `variance()`, `stdev()`
* **String**: `split()`, `substring()`, `replace_string()`
* **Datetime**: `ago()`, `now()`, `startofhour()`
* **Dynamic**: `parse_json()`, `extractjson()`
* **Plugins**: `predict`, `anomaly_detection`, `cluster()`

## 🔐 Security and Access Control

* RBAC at Azure Resource and Log Analytics Workspace level.
* Table-level permissions.
* KQL itself doesn’t write data, ensuring read-only integrity.

## 🚀 Performance Tuning

* Use `project` early to limit columns.
* Filter early (`where`) to reduce row count.
* Avoid wildcard searches (`contains` → `has` / `startswith`)
* Index-aware queries improve performance.

## 🧩 Tips, Tricks, and Patterns

* Prefer `has` over `contains` for word-matching.
* Use `toscalar()` to assign values from a query.
* Use `distinct` for unique rows.
* Combine logs with `union`.
* Debug using `print`.

## 🎯 Real-life Scenarios and Examples

* Find top 5 IPs with failed login attempts:

```kql
SigninLogs
| where ResultType != 0
| summarize Count = count() by IPAddress
| top 5 by Count desc
```

* Analyze CPU over time:

```kql
Perf
| where CounterName == "% Processor Time"
| summarize avg(CounterValue) by bin(TimeGenerated, 10m), Computer
| render timechart
```

## 🔗 Further Reading & References

* [KQL Official Docs](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/)
* [Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/)
* [KQL Tutorials](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/tutorials/)
* [KQL Quick Reference](https://docs.microsoft.com/en-us/azure/data-explorer/kql-quick-reference)

---

## ✨ Charm Glimpse of KQL

> *"Querying logs should be poetic."*

KQL is not just a query language; it's an **analyst's wand**, a **developer's magnifier**, and a **security guardian's shield**. Use it to slice time, stitch patterns, sniff anomalies, and trace problems — all with readable, powerful syntax.

[Play Ground For Challenges](https://detective.kusto.io/awards?index=-1)

My Achievements:

![image](https://github.com/user-attachments/assets/ba51463a-9ab8-4359-a3cd-903f55bb5753)

Happy querying! ⚡

---

> 📁 **Pro Tip**: Use this `README.md` as your reference card, knowledge base, and personal guide while mastering KQL.
