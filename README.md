# ElasticBook

Manage your Chrome bookmarks with Elasticsearch.

## CLI Options

### `help`

```
$ go run cmd/elasticbook/main.go -h
NAME:
   ElasticBook - Elasticsearch for your bookmarks

USAGE:
   /var/folders/mc/1wwp79ws30g1608y_hyzd7xr0000gn/T/go-build878109582/command-line-arguments/_obj/exe/main [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
   help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --command, -c  parse|index|indices|aliases|alias|count|delete|
   --verbose, -V  I wanna read useless stuff
   --help, -h   show help
   --version, -v  print the version

```

### `count`

```
$ go run cmd/elasticbook/main.go -c count
- Mobile Bookmarks (33)
- Bookmarks Bar (9)
- Other Bookmarks (10669)
```

### Delete index

Sample usage (from `go run` code):

```
$ go run cmd/elasticbook/main.go -c delete
00] - elasticbook-20151226224925
01] - elasticbook-20151227201242
02] - foobar
[0-02]:  2
Want to delete the foobar index? [y/N]: y
&{Acknowledged:true}
```

### Create index

Sample usage (from `go run` code):

```
$ go run cmd/elasticbook/main.go -c index
Node (10960/10960) 10m3s [====================================================================] 100%
Index created
- Mobile Bookmarks (33)
- Bookmarks Bar (9)
- Other Bookmarks (10918)
```

### List indices

Sample usage (from `go run` code):

```
$ go run cmd/elasticbook/main.go -c indices
00] - elasticbook-20151227224924
01] - elasticbook-20151228073443
```

### List aliases

Sample usage (from `go run` code):

```
$ go run cmd/elasticbook/main.go -c aliases
00] - elasticbook-20151227224924:     []
01] - elasticbook-20151228073443:     []
```

### Create alias

Sample usage (from `go run` code):

```
$ go run cmd/elasticbook/main.go -c alias
Index name: elasticbook-20151228073443
Alias name: default

00] - elasticbook-20151227224924:     []
01] - elasticbook-20151228073443:     [default]
```

## Elasticsearch

- https://www.elastic.co/guide/en/elasticsearch/guide

### A POQ (plain old query)

```
$ go run cmd/elasticbook/main.go -c index

Node (9001/10870) 8m32s [=======================================================>------------]  83%
```

Once you indexed all your data, try this:

```
POST elasticbook/bookmark/_search

{
  "sort": [
    "_score",
    {
      "date_added": {
        "order": "asc"
      }
    }
  ],
  "query": {
    "filtered": {
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "name": "elastic"
              }
            }
          ]
        }
      }
    }
  }
}
```

## Elastic

Elasticsearch client for Go.

- https://github.com/olivere/elastic/tree/release-branch.v3

## Martini

Classy web framework for Go.

- https://github.com/go-martini/martini

## BoltDB

An embedded key/value database for Go.

- https://github.com/boltdb/bolt

## Utils

### JQ

jq is a lightweight and flexible command-line JSON processor.

- https://stedolan.github.io/jq/
- https://stedolan.github.io/jq/manual/
- https://github.com/stedolan/jq

```
$ jq keys bookmarks_20151213.json
[
  "checksum",
  "roots",
  "version"
]
```

```
$ jq '.roots | keys' bookmarks_20151213.json
[
  "bookmark_bar",
  "other",
  "sync_transaction_version",
  "synced"
]
```

```
$ jq '.roots.bookmark_bar | keys' bookmarks_20151213.json
[
  "children",
  "date_added",
  "date_modified",
  "id",
  "name",
  "type"
]

$ jq '.roots.other | keys' bookmarks_20151213.json
[
  "children",
  "date_added",
  "date_modified",
  "id",
  "name",
  "type"
]
```

```
$ jq '.roots.other.children | length' bookmarks_20151213.json
10622
```

```
$ jq '.roots.other.children | .[42] | keys' bookmarks_20151213.json
[
  "date_added",
  "id",
  "meta_info",
  "name",
  "sync_transaction_version",
  "type",
  "url"
]
```

```
$ jq '.roots.other.children | .[] | .name ' bookmarks_20151213.json
...
...
...
```

```
$ jq '.roots.other.children | .[] | select(.url == "https://golang.org/")' bookmarks_20151215.json
{
  "date_added": "13094604045096757",
  "id": "37151",
  "name": "The Go Programming Language",
  "sync_transaction_version": "32917",
  "type": "url",
  "url": "https://golang.org/"
  }
```



