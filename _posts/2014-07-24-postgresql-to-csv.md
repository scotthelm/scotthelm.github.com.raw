---
layout: base
title: Postgres to CSV
show_title: true
---
Here lately, I have had to produce several reports from psql. If you're anything
like me, Then you're all about automating automatable things. And you're also
probably all about simplicity. So I turned to my old friends, `psql` and `cron`.

## Overview
If you want to automate something in Linux, figure out how to run it from the
command line. Then run it from `cron`. You can automate `cron` from something like
[puppet](http://puppetlabs.com) or
[chef](http://www.getchef.com/chef/) or
[docker](http://www.docker.com)

## The Command
First of all - you will probably want to wrap a thing like this in a shell
script. I use bash, so my `~/psql_report_script.sh` would be like this: 

    #!/bin/bash

     PGPASSWORD=$YOUR_ENVIRONMENT_VARIABLE psql -d <database> -h <host> -U <username> -c 'copy (select something from your_tables) TO STDOUT WITH CSV HEADER' -o /path/to/your/file.csv

Now chmod +x that file, and let's break that command down just a little.

<dl>
<dt>PGPASSWORD=$YOUR_ENVIRONMENT_VARIABLE</dt>
<dd>Now that's sensitive information. But it allows us to run the command without
having to type the password in, and that's a good thing. That's why we use an
environment variable. Pro tip - if you don't want a command to show up in <code>history</code> then put a space before it.</dd>

<dt>psql</dt>
<dd>That's our actual command</dd>

<dt>-d</dt>
<dd>that's the name of your database</dd>

<dt>-h</dt>
<dd>that's the hostname of your database server</dd>

<dt>-U</dt>
<dd>that's the database username</dd>

<dt>-c</dt>
<dd>that's the sql command to run. Since we want to output the contents of the
query, we use the <code>COPY</code> command.[more on that here](http://www.postgresql.org/docs/current/static/sql-copy.html). This is pretty much the guts of the 
command. If your SQL is complicated, consider using the -f argument to pass in
a file containing your sql command. In our COPY command we're telling psql to
copy the result of the query to STDOUT and in a CSV format with headers</dd>

<dt>-o</dt>
<dd>that's where we want the file to be saved</dd>
</dl>

## The Automation

I like cron. It's the easiest, fastest way to make things happen on a schedule
on a linux machine. Here's [how to set it up](https://help.ubuntu.com/community/CronHowto). TLDR: type in `crontab -e` to edit your crontab file, and read the top
line. From the how to set it up link:

> minute (0-59), hour (0-23, 0 = midnight), day (1-31), month (1-12), weekday (0-6, 0 = Sunday)

The last part of the file is your command. So if I wanted this to run every day
at 2am, my crontab line would look like this:

    01 02 * * * /home/my_user/reports/psql_report_script.sh
    30 02 * * * scp /path/to/your/file.csv some_user@mysftserver:/path/to/outfile.csv

Here, I'm also exporting the file out to another server at 2:30am every morning.

As you can see it's not hard at all to set up a repeatable report extract using
simple command-line tools. 
