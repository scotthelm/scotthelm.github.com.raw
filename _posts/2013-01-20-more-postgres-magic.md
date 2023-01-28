---
layout: base
title: More Postgres Magic
show_title: true
tags: tech database postgres
---
[REASSIGN OWNED BY old_role new_role] (http://www.postgresql.org/docs/9.0/static/sql-reassign-owned.html)

This command saved my bacon today. I had to apply some scripts that dropped a
couple of database objects. The role I was using did not have ownership of the
objects in question, so the script failed. I needed to change ownership so the
scripts would run correctly.

REASSIGN OWNED BY does just what it says - log into the database you want to
effect, and run the command. It transfers ownership from the old role to the
new role for all objects in the database.


    postgres=# create database ownage;
    CREATE DATABASE
    postgres=# grant all on database ownage to old_owner;
    GRANT
    postgres=# grant all on database ownage to owner_wannabe;
    GRANT
    postgres=# \q

    psql ownage -U old_owner -h localhost

    ownage=# create table users (id serial, username text, password text);
    NOTICE:  CREATE TABLE will create implicit sequence "users_id_seq" for serial column "users.id"
    CREATE TABLE
    ownage=# create table things (id serial, user_id integer, name text);
    NOTICE:  CREATE TABLE will create implicit sequence "things_id_seq" for serial column "things.id"
    CREATE TABLE
    ownage=# \d
                  List of relations
     Schema |     Name      |   Type   |  Owner
    --------+---------------+----------+----------
     public | things        | table    | old_owner
     public | things_id_seq | sequence | old_owner
     public | users         | table    | old_owner
     public | users_id_seq  | sequence | old_owner
    (4 rows)

    ownage=# reassign owned by old_owner to owner_wannabe;
    REASSIGN OWNED
    ownage=# \d
                     List of relations
     Schema |     Name      |   Type   |     Owner     
    --------+---------------+----------+---------------
     public | things        | table    | owner_wannabe
     public | things_id_seq | sequence | owner_wannabe
     public | users         | table    | owner_wannabe
     public | users_id_seq  | sequence | owner_wannabe
    (4 rows)


With that, we no longer have to iterate over objects one by one and alter owner.

And that, my friends is more Postgres magic.

