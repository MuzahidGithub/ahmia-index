# Ahmia index
Ahmia search engine uses Elasticsearch 5.4.3 to index content.

## Installation
Please install elastic search from the official repository. Elasticsearch 2.4 and 5.x both work.

## Configuration
Default configuration is enough to run index in dev mode. Here is suggestion for a more secure configuration

### /etc/security/limits.conf

```sh
elasticsearch - nofile unlimited
elasticsearch - memlock unlimited
```

### /etc/default/elasticsearch
on CentOS/RH: /etc/sysconfig/elasticsearch

```sh
MAX_OPEN_FILES=1065535
MAX_LOCKED_MEMORY=unlimited
```

### For ES 5.4.3 /etc/elasticsearch/jvm.options

```sh
# For ES 5.4.3! Half of your memory, other half is for Lucene
-Xms3g
-Xmx3g
```

## Start the service

```sh
# systemctl start elasticsearch
```

## Init mappings
Please do this when running for the first time

```sh
$ curl -XPUT -i "localhost:9200/crawl/" -d "@./mappings.json"
```

Index rotation is possible
--------------------------

- This is just an example
- Crawl to the crawl-YEAR-MONTH index
- Point latest-crawl to the latests indexes

```sh
curl -XPOST 'http://localhost:9200/_aliases' -d '
{
    "actions" : [
        { "remove" : { "index" : "crawl-2017-10", "alias" : "latest-crawl" } },
        { "add" : { "index" : "crawl-2017-11", "alias" : "latest-crawl" } },
        { "add" : { "index" : "crawl-2017-12", "alias" : "latest-crawl" } }
    ]
}'
```

Setup blacklisting
------------------

```sh
$ virtualenv -p python3 venv3
$ source venv3/bin/activate
$ pip install -r requirements.txt
```

## Crontab for Auto Blacklisting of Child Abuse Websites (torsocks required)

```sh
# Every day
0 22 * * * cd /usr/local/home/juha/ahmia-index/ && torsocks ./venv/bin/python child_abuse_onions.py > filter_these_domains.txt && bash call_filtering.sh
```
