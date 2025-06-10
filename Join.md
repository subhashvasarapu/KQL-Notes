
# KQL Join Guide - Complete Reference

This guide explains everything you need to know about joins in **Kusto Query Language (KQL)**. Useful for beginners and intermediate users working with Azure Data Explorer, Log Analytics, or Application Insights.

---

## 🔍 What is a Join in KQL?

A **join** operation merges rows from two tables based on a related column between them.

```kql
TableA
| join kind=inner TableB on CommonColumn
```

---

## 🔗 Types of Joins

| Join Type      | Description |
|----------------|-------------|
| `inner`        | Returns rows that have matching values in both tables |
| `leftouter`    | Returns all rows from the left table, and matching rows from the right table |
| `rightouter`   | All rows from the right table, and matching from the left |
| `fullouter`    | All rows when there is a match in one of the tables |
| `anti`         | Rows from the left table that **don't** match the right |
| `leftanti`     | Same as `anti` |
| `rightanti`    | From the right side that don’t match left |
| `cross`        | Cartesian product — every row from A joins with every row from B |

---

## ✅ Valid Join Syntax

```kql
TableA
| join kind=inner TableB on ColumnName
```

### With Column Aliases

```kql
TableA
| join kind=inner TableB on $left.ColumnA == $right.ColumnB
```

---

## ❌ Invalid Join Syntax and Errors

### Common Error

```text
Semantic error: join: both sides of equality should be column entities only.
```

### Example of What **Not** to Do

```kql
TableA
| join kind=inner TableB on 1 == 1   // ❌ INVALID
```

You must **only use column names** in the `on` clause.

Corrected version:

```kql
TableA
| extend join_key = 1
| join kind=inner (TableB | extend join_key = 1) on join_key
```

---

## 🧠 Use Case Example: IP Geolocation

### Step 1: Filter FileReceived events

```kql
let FileEvents = ChatServerLogs
| where Timestamp between (datetime(2025-06-04 19:03:00) .. datetime(2025-06-07 22:04:00))
| where EventType == "FileReceived"
| extend Minute = bin(Timestamp, 1m)
| project Minute, Timestamp, ClientIP;
```

### Step 2: Find windows with only one active IP

```kql
let LoneActivityWindows = FileEvents
| summarize TotalEvents = count(), UniqueIPs = dcount(ClientIP) by Minute
| where UniqueIPs == 1;
```

### Step 3: Extract lone IPs

```kql
let LoneIPs = FileEvents
| join kind=inner (LoneActivityWindows) on Minute
| summarize Events = count() by Minute, ClientIP
| project Minute, ClientIP;
```

### Step 4: Join with IP Geolocation

```kql
let IPGeoMapped = LoneIPs
| extend dummy=1
| join kind=inner (IpToLocation | extend dummy=1) on dummy
| where ipv4_is_in_range(ClientIP, IpCidr)
| project Minute, ClientIP, Lat, Lon;
```

---

## 🛑 Troubleshooting Tips

- Always ensure you're joining on columns using `==` or `$left.col == $right.col`
- For many-to-many join simulation, use `extend dummy=1` and join on `dummy`
- Validate your column names — column mismatches cause silent query failures

---

## 🧾 Summary

- Use `join` only with column names or valid aliasing
- Avoid `1 == 1` directly in `on` clause
- Use `project`, `extend`, and `bin` for better control
- Test joins with small filters first, then scale up

Happy querying! 🚀
