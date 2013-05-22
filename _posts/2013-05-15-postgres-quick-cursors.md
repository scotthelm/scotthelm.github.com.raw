---
layout: base
title: Postgres quick cursors
show_title: true
---

I needed to act on individual members of a set. I also didn't want to write a 
function. In psql, I discovered the DO block. It allowed me to gain access to
pgplsql without being in the context of a UDF (User Defined Function).

In this particular case, I created a table called view_refugees.  These were
views that were causing my rails migration to fail due to a change in an
underlying table column's datatype. The plan is that I will create the view
refugees from pg_views, drop the views, run the migration, then recreate the
views.

     do $$declare r record;
      begin
        for r in select viewname, schemaname from view_refugees
        loop
          execute 'drop view '
          || r.schemaname
          || '.'
          || r.viewname
          || ' cascade';
        end loop;
      end$$;

DO block makes that easier.
