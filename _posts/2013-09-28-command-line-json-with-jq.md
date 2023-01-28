---
layout: base
title: Command Line JSON with JQ
show_title: true
tags: tech tools jq
---

I started my first foray in to bash scripting. I have client information stored
in a Mongo database. I have canonical data in Postgresql. I have indexed data
in Elasticsearch. I want a quick way to check on the health of the indexes for
clients both interactively and through script calls from something like Monit.
I also want it to work really quickly.

It would be relatively simple for me to fire up a ruby script and do what I want to do.
However, the parts of our applications that deal with Mongo and Elasticsearch are
found in Rails engines. For what I want, that was too heavy. I really wanted to
keep this utility as light as possible, and put my fledgeling Linux skills to the test.

So I dug into the [Mongo CLI](http://docs.mongodb.org/manual/administration/scripting/)
and discovered that I could output the results of a query back to the shell by
invoking mongo with a script thusly:

    mongo hostname/databasename path/to/script

my script returned the name and elastic search url for each client:

    cursor = db["clients"].find({},{name: 1, elastic_search_url: 1});
    while ( cursor.hasNext() ) {
       printjsononeline( cursor.next() );
    }

And this was where I ran into my first roadblock. JSON. It just so happens that
JSON is not that easy to parse with `awk`, `sed`, and `grep`. So I lurked on stackoverflow
and stumbled on the [JQ](http://stedolan.github.io/jq/) library. It has been a
godsend for dealing with JSON on the command line.

Installation of the binary was really quick and painless:

    wget http://stedolan.github.io/jq/download/linux64/jq
    chmod +x jq
    sudo mv jq /usr/bin/

And I had JSON parsing available on my command line. (There are source install
options as well to be found on the [dowloads page](http://stedolan.github.io/jq/download/)).

I found that I needed to modify the output of my mongo script so that it was
close to valid json:

    cursor = db["clients"].find({},{key: 1, elastic_search_url: 1});
    print("{ \"clients\" : [");
    while ( cursor.hasNext() ) {
       printjsononeline( cursor.next() );
       print(",");
    }
    print("{}")
    print("]}");

then I turned to my trusty pal, `awk` and wrote this little script to clean up
the client output:

    #! /usr/bin/awk -f
    /[{,\]]/ {
      gsub("ObjectId\(","",$0);
      gsub("\)","",$0);
      print $0
    }

That allowed me to pipe the valid json output to `jq`:

      mongo $MONGODB ./clients.js | ./client_cleanup.awk | jq '.["clients"]'

and I get really pretty output like this:

    [
      {
        "key": "development_client",
        "elastic_search_url": "http://search.dev:9200",
        "_id": "50a6a3d86224051db2000015"
      },
      {
        "key": "bptest_client",
        "elastic_search_url": "http://search.dev:9200",
        "_id": "50d0cd506224052eb7000002"
      },
      {
        "key": "global_test_client",
        "elastic_search_url": "http://search.dev:9200",
        "_id": "50ed86586224053e54000001"
      },
      {}
    ]

Then I started dealing with elasticsearch. I have many indexes for various clients
so when I make a curl to the cluster status endpoint, the output is easily over 
300 rows of console output. By contrast, the same endpoint, picking out only the
name of the indexes (which is all I really want for this particular function) is
amazingly simple:

    curl -s -XGET 'http://search.dev:9200/_status' | jq '.["indices"]' | jq 'keys'

and produces what I really want to see:

    [
      "client_509aada36224051fec000001",
      "client_50a6a3d86224051db2000016",
      "client_50d093c96224051f91000002",
      "client_50d0bbb06224052add000002",
      "client_50d0cd506224052eb7000002",
      "client_50d1d2fa6224052c64000002",
      "client_50d485b6622405261a000002",
      "client_512b8556622405614f000002",
      "client_51924d266224055568000004",
      "client_51bf5685622405297a000002",
      "client_51e441a8622405799900000e",
    ]

and another call tells me how many indexes I have:

    $ curl -s -XGET 'http://search.dev:9200/_status' | jq '.["indices"]' | jq 'keys' | jq 'length'
    $ 11

This makes it incredibly easy to work with data retrieved from JSON services
using `curl`. Which makes it incredibly easy to script service interruption
detection and health monitoring.

I am now in the process of wrapping a good many elasticsearch and mongo calls in
my little bash script. I'll be hosting the reult on github. as soon as I get it
in any sort of presentable shape. However, I can't say enough good things about
the JQ library. If you need to do anything with JSON on the command line, I
have found this to be the best option. Cheers, Stedolan, for the great contribution!

