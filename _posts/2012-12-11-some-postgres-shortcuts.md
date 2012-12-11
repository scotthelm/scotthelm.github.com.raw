---
layout: base
title: "Some Postgres Shortcuts"
---

##Postgres
***

###Command Line
```
pg_dump <database_name> -s -O -h <host> -U <username> -W -f <path_to_target_file> -n <schema_name> -n <schema_name>
```

* -s Schema only, please.  No data
* -O (that's an O as in "Oh"): don't script ownership of objects
* -h the host.  If you're on the box with the database, then localhost usually works
* -U the username that you want to connect to the database with
* -W prompt me for a password.  If you don't want this, then you will need to set the PG_PASS environment variable
* -n schema name.  Postgres has the concept of schemas.  Think of them as namespaces.  You can pass a list with multiple -n or leave it blank and get all of them

The -h, -U and -W work with psql as well

###psql

within psql, \e will open your editor of choice.  I usually use vim.  It creates a file in
```
/tmp/psql.edit.<pid>
```
where pid is the process ID of psql.  You edit your sql in that file.  When you :wq to write and quit vim, psql will use that file as it's command.  So if you want it to execute, then make sure it ends with a semicolon; or it won't run.

More to come. These are ones I've used today.
