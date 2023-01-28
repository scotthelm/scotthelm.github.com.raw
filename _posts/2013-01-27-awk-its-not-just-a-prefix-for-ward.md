---
layout: base
title: Awk - It's not just a prefix for "ward"
show_title: true
tags: tech tools awk
---
It's kind of a joke around where I work. I saw a data import script my boss had
written that used `awk` to preprocess massive data files, separating them into
record-type-specific files for use with BCP. Since then, I have (jokingly)
declared that the combination of awk and elasticsearch is my hammer, and all
problems are nails.

I was looking for speed improvements for our index import process. Having
made said improvements (by parallelizing the tasks - invoking the import process
in multiple forks and datasets separated by modulus math), I needed an easy way to
keep track of the progress. I already had the processes writing to a log file
with this format:

    20755 processed 1000 objects ending with: 18000 in 53 seconds

The first number is the process id or pid.
Since I had multiple processes running, I didn't want to have to find the last
entry for each pid and do math to find out how quickly things were going. So I
whipped up this little import_stats.awk script:

    #! /bin/awk -f
    # this section happens before any lines are read.
    # it allows you to set up variables that you can
    # use as the program executes
    BEGIN {
      total_seconds = 0;
      count = 0;
    }

    # the unnamed section here is what is executed 
    # for each line. In this case, it's checking to
    # see if a line begins with a number, and 
    # incrementing counts and totals on if it does.
    # the $9 variable is the ninth column in the 
    # data line
    {
      if ($1 ~ /^[0-9]*$/) {
        total_seconds += $9;
        ++count;
      }
    }

    # The end section allows you to sum up, peforming
    # actions to clean up or print results. In this
    # case I'm printing my results
    END {
      print "average seconds per thousand: " total_seconds / count;
      print "total processed_thus far: " count * 1000;
    }


now I can check on progress by typing this from the app's root:

    cat log/import.log | awk -f import_stats.awk

and I get

    average seconds per thousand: 50.4306
    total processed_thus far: 432000

Linux and Unix have so many great tools that have been developed over the years
that are rock solid foundations for new things. I found this site to be helpful
as I was writing my little script:
[Awk - A Tutorial and Introduction](http://www.grymoire.com/Unix/Awk.html)

It took no time at all to develop this utility. It's simple, saves me time and
makes me happy.
