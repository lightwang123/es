# Deprecation notice

This tool is not supported any more. You may achieve the same result with
simple curl requests to your Elasticsearch cluster.
See [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/_exploring_your_cluster.html) for details.


# Elasticsearch command line tool

`es` is a small command line tool to interact with the
Elasticsearch search engine.

**Warning** This tool currently only supports Elasticsearch 1.x.

Notice that you could do all of this with `curl` commands, as
seen on the [Elasticsearch API](http://www.elasticsearch.org/guide/reference/api/).
However, you probably save a few keystrokes with `es`.

## Setup

You need to compile yourself currently:

	  $ go get github.com/olivere/es
	  $ cd $GOPATH/src/github.com/olivere/es
	  $ go build
	  $ ./es help

## Commands

Before we start, you can always lookup the Elasticsearch API via
the `api` command, like so:

	  $ es api indices

The `api` command will open up a browser window with the API page
that matches the specified command. You can find the complete
[Elasticsearch API here](http://www.elasticsearch.org/guide/reference/api/).

Let's get started. First we list existing indices, either all of them
or via a regular expression.

	  $ es indices
	  master
	  marvel
	  dummy
	  $ es indices 'm.*'
	  master
	  marvel

Let's create a new index. Use the -f flag to force creation, i.e. it will
not print an error if the index already exists (and won't touch the
existing index).

	  $ es create twitter
	  $ es indices
	  master
	  marvel
	  dummy
	  twitter
	  $ es create twitter
	  Error: IndexAlreadyExistsException[[twitter] Already exists] (400)
	  $ es create -f twitter

Print some useful information with the status API.

	  $ es status twitter
	  { ... }

You can also get the status of all indices. Just leave out the index name.

	  $ es status
	  { ... }

If you need information about the number of documents in an index and such,
use the stats API call. You can use it with or without an index name.

	  $ es stats
	  { ... }
	  $ es stats twitter
	  { ... }

Let's remove some indices.

	  $ es delete twitter
	  $ es indices
	  master
	  marvel
	  dummy
	  $ es delete twitter
	  Error: IndexMissingException[[twitter] missing] (404)
	  $ es delete -f twitter
	  $ es indices
	  master
	  marvel
	  dummy

Let's review mappings, and even create mappings from the command line.

	  $ es mapping dummy
	  {
		  "dummy" : {
		  }
	  }
	  $ es mapping nonexistent
	  Error: IndexMissingException[[nonexistent] missing] (404)
	  $ es create twitter
	  $ es put-mapping twitter tweet < tweet-mapping.json
	  $ es mapping twitter
	  {
	    "twitter" : {
	      "tweet" : {
	        "properties" : {
	          "message" : {
	            "type" : "string",
	            "store" : "yes"
	          }
	        }
	      }
	    }
	  }

Templates, oh how I love thee... here's a sample session.

	  $ es templates
	  dummy-template
	  $ es template dummy-template
	  {
	  }
	  $ es create-template another-template < template.json
	  $ es templates
	  dummy-template
	  another-template
	  $ es template another-template
	  {
	  }
	  $ es delete-template another-template
	  $ es delete-templete -f another-template

And, to get a list of all aliases, use:

    $ es aliases
    alias-1
    alias-2
    another-alias
    $ es aliases 'al*'
    alias-1
    alias-2
    $ es aliases -i
    alias-1 -> index1
    alias-2 -> index1
    another-alias -> index2

Finally, there's some helper command, e.g. reindexing one index into
another:

    $ es reindex -v twitter twitter-snapshot


## Credits

Thanks a lot for the great folks working hard on
[Elasticsearch](http://www.elasticsearch.org/) and
[Go](http://golang.org/).

Also a big thanks to [Blake Mizerany](https://github.com/bmizerany)
and the [fast heroku client](https://github.com/bmizerany/hk)
for inspiration on how to create a Go-based command line tool.
