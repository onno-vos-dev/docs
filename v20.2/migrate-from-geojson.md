---
title: Migrate from GeoJSON
summary: Learn how to migrate data from GeoJSON into a CockroachDB cluster.
toc: true
build_for: [cockroachdb]
---

<span class="version-tag">New in v20.2</span>: CockroachDB supports efficiently storing and querying spatial data.

This page has instructions for migrating data from GeoJSON into CockroachDB using [`shp2pgsql`](https://manpages.debian.org/stretch/postgis/shp2pgsql.1.en.html) and [`IMPORT`][import].

In this example we will import a specific data set that is available as a [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON): a [tornadoes data set](https://www.spc.noaa.gov/gis/svrgis/zipped/1950-2018-torn-initpoint.zip) that is [available from the US Nationial Weather Service](https://www.spc.noaa.gov/gis/svrgis/) (NWS).

Please refer to the documentation of your GIS software for instructions on exporting GIS data to a GeoJSON.

## Step 1. Convert the GeoJSON data to SQL

To load the tornado GeoJSON into CockroachDB, we must first convert it to SQL using the `shp2pgsql` tool.

First, download and unzip the tornado data:

{% include copy-clipboard.html %}
~~~ shell
wget https://www.spc.noaa.gov/gis/svrgis/zipped/1950-2018-torn-initpoint.zip
~~~

{% include copy-clipboard.html %}
~~~ shell
unzip 1950-2018-torn-initpoint.zip
~~~

{% include copy-clipboard.html %}
~~~ shell
cd 1950-2018-torn-initpoint/
~~~

Next, convert the GeoJSON data to SQL using the following command:

{% include copy-clipboard.html %}
~~~ shell
shp2pgsql 1950-2018-torn-initpoint.shp > tornado-points.sql &
~~~

## Step 2. Host the files where the cluster can access them

Each node in the CockroachDB cluster needs to have access to the files being imported.  There are several ways for the cluster to access the data; for a complete list of the types of storage [`IMPORT`][import] can pull from, see [Import File URLs](import.html#import-file-urls).

For local testing, you can [start a local file server](create-a-file-server.html).  The following command will start a local file server listening on port 3000:

{% include copy-clipboard.html %}
~~~ shell
python3 -m http.server 3000
~~~

{{site.data.alerts.callout_success}}
We strongly recommend using cloud storage such as Amazon S3 or Google Cloud to host the data files you want to import.
{{site.data.alerts.end}}

## Step 3. Import the SQL

Since the file is  being served from a local server, and is formatted as Postgres-compatible SQL we can import the data using the standard [`IMPORT PGDUMP`](import.html#import-a-postgres-database-dump) method:

{% include copy-clipboard.html %}
~~~ sql
IMPORT PGDUMP ('http://localhost:3000/tornado-points.sql');
~~~

~~~
        job_id       |  status   | fraction_completed | rows  | index_entries |  bytes
---------------------+-----------+--------------------+-------+---------------+-----------
  584874782851497985 | succeeded |                  1 | 63645 |             0 | 18287213
(1 row)
~~~

{% include {{ page.version.version }}/sql/use-import-into.md %}

## See also

- [`IMPORT`][import]
- [Migrate from OpenStreetMap](migrate-from-openstreetmap.html)
- [Migrate from Shapefiles](migrate-from-shapefiles.html)
- [Migration Overview](migration-overview.html)
- [Migrate from MySQL][mysql]
- [Migrate from Postgres][postgres]
- [SQL Dump (Export)](cockroach-dump.html)
- [Back Up and Restore Data](backup-and-restore.html)
- [Use the Built-in SQL Client](cockroach-sql.html)
- [Other Cockroach Commands](cockroach-commands.html)

<!-- Reference Links -->

[postgres]: migrate-from-postgres.html
[mysql]: migrate-from-mysql.html
[import]: import.html
