> 3 × (2 × {{ sku|USD|mdb.cluster.redis.v3.cpu.c100|string }} + 8 × {{ sku|USD|mdb.cluster.redis.v3.ram|string }}) = {% calc [currency=USD] 3 × (2 × {{ sku|USD|mdb.cluster.redis.v3.cpu.c100|number }} + 8 × {{ sku|USD|mdb.cluster.redis.v3.ram|number }}) %}
>
> Total: {% calc [currency=USD] 3 × (2 × {{ sku|USD|mdb.cluster.redis.v3.cpu.c100|number }} + 8 × {{ sku|USD|mdb.cluster.redis.v3.ram|number }}) %}, cost of operation of {{ VLK }} hosts per hour.

Where:
* 3: Number of {{ VLK }} hosts.
* 2: Number of vCPUs.
* {{ sku|USD|mdb.cluster.redis.v3.cpu.c100|string }}: Cost of using 100% vCPU per hour.
* 8: Amount of RAM per {{ VLK }} host (in GB).
* {{ sku|USD|mdb.cluster.redis.v3.ram|string }}: Cost of using 1 GB of RAM at 100% vCPU per hour.