Get a list of buses:

```bash
yc serverless eventrouter bus list
```

Result:

```text
+----------------------+------------------+----------------------+--------+---------------------+
|          ID          |       NAME       |      FOLDER ID       | STATUS | DELETION PROTECTION |
+----------------------+------------------+----------------------+--------+---------------------+
| f6676a9ti657******** | my-new-bus-17    | b1g681qpemb4******** | ACTIVE | false               |
| f66aevm4ithv******** | my-favourite-bus | b1g681qpemb4******** | ACTIVE | true                |
| f66m2q222n92******** | my-bus-42        | b1g681qpemb4******** | ACTIVE | false               |
+----------------------+------------------+----------------------+--------+---------------------+
```