Couchdb-dump to plain array
============

## Modification info for this fork

The script has been modified, where Backup will create a plain array of objects from the CouchDB, suitable for viewing with Apache Drill, in this format:

```
[
	{... document},
	{... document},
	{... document}
]
```

Do NOT attempt to restore using this file, it doesn't contain the metadata expected to restore.

## See original fork https://github.com/danielebailo/couchdb-dump for full backup/ restore functionality and full docs.

## Quickstart (& quickend)
* Dump:

```./couchdb-dump.sh -b -H 127.0.0.1 -d my-db -f dumpedDB.json -u admin -p password```

## Usage
```
Usage: ./couchdb-dump.sh [-b] -H <COUCHDB_HOST> -d <DB_NAME> -f <BACKUP_FILE> [-u <username>] [-p <password>] [-P <port>] [-l <lines>] [-t <threads>] [-a <import_attempts>]
	-b   Run script in BACKUP (dump) mode.
	-H   CouchDB Hostname or IP. Can be provided with or without 'http(s)://'
	-d   CouchDB Database name to dump.
	-f   File to Dump-to.
	-P   Provide a port number for CouchDB [Default: 5984]
	-u   Provide a username for auth against CouchDB [Default: blank]
	       -- can also set with 'COUCHDB_USER' environment var
	-p   Provide a password for auth against CouchDB [Default: blank]
	       -- can also set with 'COUCHDB_PASS' environment var
	-t   Number of CPU threads to use when parsing data [Default: nProcs-1]
	-c   Create DB on demand, if they are not listed.
	-q   Run in quiet mode. Suppress output, except for errors and warnings.
	-z   Compress output file (Backup Only)
	-T   Add datetime stamp to output file name (Backup Only)
	-V   Display version information.
	-h   Display usage information.

Example: ./couchdb-dump.sh -b -H 127.0.0.1 -d mydb -f dumpedDB.json -u admin -p password
```

## Why do you need it?
We use this fork to dump couchdb for reading by Apache Drill.
