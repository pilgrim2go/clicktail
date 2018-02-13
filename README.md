# clicktail

`clicktail` is [Altinity](https://altinity.com)'s agent for ingesting log file data into ClickHouse and making it available for exploration. Its favorite format is **JSON**, but understands how to parse a range of other well-known log formats.

`clicktail` is based on `honeytail` tool see [here](https://github.com/honeycombio/honeytail) and [its documentation](https://honeycomb.io/docs/send-data/agent/)

## Supported Parsers

`clicktail` supports reading files from `STDIN` as well as from a file on disk.

Our complete list of parsers can be found in the [`parsers/` directory](parsers/), but as of this writing, `clicktail` will support parsing logs generated by:

- [ArangoDB](parsers/arangodb/)
- [MongoDB](parsers/mongodb/)
- [MySQL](parsers/mysql/)
- [PostgreSQL](parsers/postgresql/)
- [nginx](parsers/nginx/)
- [regex](parsers/regex/)

## Installation

Install from `deb` package:

```
curl -s https://packagecloud.io/install/repositories/Altinity/clickhouse/script.deb.sh | bash
```
if you want to force `os` and `dist` of your system, use the following command:

```
curl -s https://packagecloud.io/install/repositories/Altinity/clickhouse/script.deb.sh | os=ubuntu dist=xenial bash
```

Install from source:

```
go get github.com/Altinity/clicktail
```

to install to a specific path:

```
GOPATH=/usr/local go get github.com/Altinity/clicktail
```

the binary will install to `/usr/local/bin/clicktail`

## Configuration

Use `clicktail.conf` file to manage options. There are section of the file called `Required Options` which should be set in the first place.

#### Config Example

`/etc/clicktail/clicktail.conf`
```
[Required Options]
ParserName = mysql
LogFiles = /var/log/mysql/mariadb-slow.log
Dataset = clicktail.mysql_slow_log
```

## Usage

Make sure ClickHouse server has proper schema created. See `schema` folder for `CREATE DATABASE` and `CREATE TABLE` statements.

Or do the following:

Create DB:
```
cat schema/db.sql | clickhouse-client --multiline
```

Create Table for MySQL slow logs:
```
cat schema/mysql.sql | clickhouse-client --multiline
```

Once schema is prepared you can use for MySQL:
```
clicktail --dataset='clicktail.mysql_slow_log' --parser=mysql --file=/var/log/mysql/mysql-slow.log
```

Or for Nginx:

```
clicktail -p nginx -f /var/log/nginx/access.log -d clicktail.nginx_log --nginx.conf=/etc/nginx/nginx.conf --nginx.format=combined
```

For more advanced usage, options, and the ability to scrub or drop specific fields, see [documentation](https://honeycomb.io/docs/send-data/agent).

