Couchdb-dump to plain array
============

## Fork Modification Info

Backup will create a plain array of objects from the CouchDB, suitable for viewing with Apache Drill, in this format:

```
[
	{... document},
	{... document},
	{... document}
]
```

Do NOT attempt to restore using this file.

============

It works on LINUX/UNIX, Bash based systems (MacOSx) + Git Bash (Windows)

**Bash command line script to EASILY Backup & Restore a CouchDB database**

 * Needs bash (plus curl, tr, file, split, awk, sed)
 * Dumped database is output to a file (configurable).

## Quickstart (& quickend)
* Backup:

```bash couchdb-dump.sh -b -H 127.0.0.1 -d my-db -f dumpedDB.json -u admin -p password```

* Restore:

```bash couchdb-dump.sh -r -H 127.0.0.1 -d my-db -f dumpedDB.json -u admin -p password```

## Why do you need it?
Surprisingly, there is not a straightforward way to dump a CouchDB database. Often you are suggested to replicate it or to dump it with the couchdb `_all_docs` directive.

**But, using the `_all_docs` directive provides you with JSON which cannot be directly re-import back into CouchDB**.

Hence, the goal of this script(s) is to give you a simple way to Dump & Restore your CouchDB database.

## NOTE

Attachments in Database documents are only supported in CouchDB 1.6+

## Usage
```
Usage: ./couchdb-dump.sh [-b] -H <COUCHDB_HOST> -d <DB_NAME> -f <BACKUP_FILE> [-u <username>] [-p <password>] [-P <port>] [-l <lines>] [-t <threads>] [-a <import_attempts>]
	-b   Run script in BACKUP mode.
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

### Bonus 1! Full Database Compaction
In the past, we've used this script to greatly compress a bloated database.
In our use case, we had non-sequential IDs which cause CouchDB's B-Tree to balloon out of control, even with daily compactions.

**How does this fix work?**
When running the export, all of the documents are pulled out in "ID Order"- When re-importing these (now sorted) documents again, the B-Tree can be created in a much more efficient manner. We've seen 15GB database files, containing only 2.1GB of raw JSON, reduced to 2.5GB on disk after import!

### Bonus 2! Purge Historic and Deleted Data
CouchDB is an append-only database. When you delete records, the metadata is maintained for future reference, and is never fully deleted. All documents also retain a historic revision count.
With the above points in mind; the export and import does not include Deleted documents, or old revisions; therefore, using this script, you can export and re-import your data, cleansing it of any previously (logically) deleted data!

If you pair this with deletion and re-creation of replication rules (using the 'update_seq' parameter to avoid re-pulling the entire DB/deleted documents from a remote node) you can manually compress and clean out an entire cluster of waste, node-by-node.
Note though; after creating all the rules with a fixed update_seq, once completed to the entire cluster, you will need to destroy and recreate all replication rules without the fixed update_seq - else, when restarting a node etc, replication will restart from the old seq.

