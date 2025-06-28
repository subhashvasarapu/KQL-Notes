

---

### ✅ File: `kusto-joins-explained.md`

````markdown
# 🔍 Kusto Join Types Explained (with Examples)

This document explains the different types of **Kusto Query Language (KQL)** joins using simple concepts, tables, and syntax examples.

---

## 📖 What is a Join?

In Kusto, a `join` is used to **combine rows** from two tables based on a **common column** (key). 

**General Syntax**:
```kusto
Table1
| join kind=<join_type> Table2 on <common_column>
````

---

## 👦 Example Tables

### Table: `KidsInfo`

| Name    | Age |
| ------- | --- |
| Alice   | 10  |
| Bob     | 9   |
| Charlie | 8   |

### Table: `FavoriteColor`

| Name  | Color |
| ----- | ----- |
| Alice | Red   |
| Bob   | Blue  |

---

## 🧩 1. `inner` Join

**Includes only matching rows** from both tables.

```kusto
KidsInfo
| join kind=inner FavoriteColor on Name
```

### Result:

| Name  | Age | Color |
| ----- | --- | ----- |
| Alice | 10  | Red   |
| Bob   | 9   | Blue  |

🧠 **Only matched rows shown. `Charlie` is excluded.**

---

## 🧩 2. `leftouter` Join

**All rows from left**, matched data from right, nulls if no match.

```kusto
KidsInfo
| join kind=leftouter FavoriteColor on Name
```

### Result:

| Name    | Age | Color |
| ------- | --- | ----- |
| Alice   | 10  | Red   |
| Bob     | 9   | Blue  |
| Charlie | 8   | null  |

🧠 `Charlie` is included with `null` for unmatched column.

---

## 🧩 3. `rightouter` Join

**All rows from right**, matched data from left, nulls if no match.

```kusto
KidsInfo
| join kind=rightouter FavoriteColor on Name
```

### Result:

| Name  | Age | Color |
| ----- | --- | ----- |
| Alice | 10  | Red   |
| Bob   | 9   | Blue  |

🧠 Since `FavoriteColor` doesn't have `Charlie`, he is excluded.

---

## 🧩 4. `fullouter` Join

**All rows from both tables**, matched where possible, nulls otherwise.

```kusto
KidsInfo
| join kind=fullouter FavoriteColor on Name
```

### Result:

| Name    | Age | Color |
| ------- | --- | ----- |
| Alice   | 10  | Red   |
| Bob     | 9   | Blue  |
| Charlie | 8   | null  |

🧠 `Charlie` is included, and all matches shown.

---

## 🧩 5. `leftanti` Join

**Rows only from left** that **do NOT match** in the right.

```kusto
KidsInfo
| join kind=leftanti FavoriteColor on Name
```

### Result:

| Name    | Age |
| ------- | --- |
| Charlie | 8   |

🧠 Filters left table for rows **without** matches in right.

---

## 🧩 6. `rightanti` Join

**Rows only from right** that **do NOT match** in the left.

```kusto
KidsInfo
| join kind=rightanti FavoriteColor on Name
```

### Result:

*(No unmatched rows in right table)*

🧠 Use when you want to see **what’s in right but not in left**.

---

## 🧩 7. `innerunique` Join

Performs like `inner` but ensures **only one match** per row in left (if duplicates exist in right).

```kusto
KidsInfo
| join kind=innerunique FavoriteColor on Name
```

### Result:

Same as `inner`, but guarantees **at most one** match per left row.

---

## 🗂️ Join Summary Table

| Join Type     | Description                                            | Included Rows       |
| ------------- | ------------------------------------------------------ | ------------------- |
| `inner`       | Matching rows only                                     | From both (matched) |
| `leftouter`   | All left + matching right                              | All from left       |
| `rightouter`  | All right + matching left                              | All from right      |
| `fullouter`   | All from both, matched where possible                  | All                 |
| `leftanti`    | Only left rows with **no match** in right              | Left unmatched      |
| `rightanti`   | Only right rows with **no match** in left              | Right unmatched     |
| `innerunique` | Inner join with **only one match** from right per left | Unique matches      |

---

## 🧠 Tips

* Always check the join key: it **must exist in both tables**.
* If keys have different column names, use:

```kusto
Table1
| join kind=inner (Table2 | rename NewKeyName = OldKeyName) on NewKeyName
```

* Use `project` to clean or reorder final output.

---

## 📌 Real-World Use Case Example

### Tables:

* `Users`: list of employees.
* `Logins`: login activity.

### Task:

> Find all users and their latest login time.

### Query:

```kusto
Users
| join kind=leftouter (
    Logins
    | summarize LastLogin=max(Timestamp) by UserId
) on UserId
```

