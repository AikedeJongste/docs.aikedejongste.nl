---
layout: default
title: Postgres - pg_dump
#parent: Postgres
parent: Databases
---

# pg_dump


## Step by step




## WITH roles (good for backups)

## WITHOUT roles (good for migrations)

* `--no-privileges`
* `--no-owner`


## Options and examples

* `-Fp` = format plain, this is fastest but produces normal sql
* `-Fc` = format compressed, much smaller result, much slower


--jobs=<NUMBER OF CORES> - This allows the dump to run multiple jobs. My recommendation is to set this equal to the number of cores available on the machine the dump is running on. You must either use the directory or custom output formats. Speaking of which:

--format=d - This tells pg_dump that you want to use the directory output format. This is required for parallel jobs.

--data-only - This is the flag to dump only the data from the tables and not the schema information.

--schema=public - Instructs pg_dump to only dump the public schemas, for most cases this is all you need.

--file=<NAME OF DUMP> - The file/directory to output to.

--table=<NAME OF TABLE> - This flag specifies which table to dump. Multiple --table flag may be used together.




$ pg_dump -Z0 -j 10 -Fd database_name -f dumpdir
$ tar -cf - dumpdir | pigz > dumpdir.tar.gz
$ rm dumpdir


pg_dump -Fc -Z 9  --file=file.dump myDb
pg_restore -Fc -j 8  file.dump


## Performance and convenience

### Compression

The formats -Fd and -Fc have the same compression, but -Fd is 3 times faster to dump.


```pg_dump dbname --format=custom --no-privileges --disable-triggers -Fd -j4 -U username -f data.pgdump```

takes 7 minutes  

```pg_dump dbname --format=custom --no-privileges --disable-triggers -Fp -U username | pigz > data.pigz```

takes 11 minutes

The disk usage is the same. Directory format is hard to transfer.