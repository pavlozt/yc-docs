---
editable: false
sourcePath: en/_api-ref/datalens/function-ref/DEGREES.md
---

# DEGREES



#### Syntax {#syntax}


```
DEGREES( radians )
```

#### Description {#description}
Converts radians to degrees.

**Argument types:**
- `radians` — `Fractional number | Integer`


**Return type**: `Fractional number`

#### Example {#examples}



| **[n]**   | **[n] &ast; PI()**   | **DEGREES([n]&ast;PI())**   |
|:----------|:---------------------|:----------------------------|
| `-1.00`   | `-3.14`              | `-180.00`                   |
| `-0.50`   | `-1.57`              | `-90.00`                    |
| `-0.25`   | `-0.79`              | `-45.00`                    |
| `0.00`    | `0.00`               | `0.00`                      |
| `0.25`    | `0.79`               | `45.00`                     |
| `0.50`    | `1.57`               | `90.00`                     |
| `1.00`    | `3.14`               | `180.00`                    |




#### Data source support {#data-source-support}

`ClickHouse 21.8`, `Files`, `Google Sheets`, `Microsoft SQL Server 2017 (14.0)`, `MySQL 5.7`, `Oracle Database 12c (12.1)`, `PostgreSQL 9.3`, `Yandex Documents`, `YDB`.
