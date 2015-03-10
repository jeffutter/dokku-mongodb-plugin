[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/jeffutter/dokku-mongodb-plugin?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

MongoDB plugin for Dokku
---------------------------
Plugin to setup Mongodb accounts for containers deployed to Dokku


Installation
------------
```
git clone https://github.com/jeffutter/dokku-mongodb-plugin.git /var/lib/dokku/plugins/mongodb
dokku plugins-install
```


Commands
--------
```
$ dokku help
    mongodb:console                 		        Launch an admin mongodb console
    mongodb:create <app> <database> 		        Create a Mongo database and optional params for app
    mongodb:delete <app> <database> 		        Delete specified Mongo database
    mongodb:dump <database> [-tar]                  Creates a binary export of the contents of database (-tar tarball dump)
    mongodb:link <app> <database>	       	        Set ENV variables for app if database exists
    mongodb:list                    		        List all databases
    mongodb:logs                    		        Show logs from MongoDB program
    mongodb:restore <database> <file-or dirname>    Restores the state of a database of a database exported with mongodb:dump
    mongodb:start                   		        Start the MongoDB docker container if it isn't running
    mongodb:status                  		        Shows status of MongoDB
    mongodb:stop                    		        Stop the MongoDB docker container
```

Simple usage
------------
You need to have app running with the same name!

Create a new DB:
```
$ dokku mongodb:create foo            # Server side
$ ssh dokku@server mongodb:create foo # Client side

    {
        "_id" : ObjectId("524c90dc45addf0edad783a2"),
        "user" : "foo",
        "readOnly" : false,
        "pwd" : "825ec0deacccb3c6bb621d84153e5877"
    }

```

Now if you push your app again, you will have the following ENV variables:
```
MONGODB_DATABASE
MONGODB_HOST
MONGODB_PORT
MONGODB_USERNAME
MONGODB_PASSWORD
MONGO_URL
MONGO_URI
```

These can be found using:
```
dokku config appname
```

Persistence
-----------

The Mongo DB data is stored outside the container on the host at `$DOKKU_ROOT/.mongodb/data`. Inside the container, this location is bound to `/tmp/mongo` and will be there. 
Since the data is stored outside the container, it will persistent through container restarts, and also be available to future revisions of your container. 

Backing up a database
---------------------

`mongodb:dump` creates a backup of a whole database. The result can be optionally compressed in a gzipped tarball (tar.gz) by adding the `-tar` parameter after the database name. The dump is placed in `$DOKKU_ROOT/.mongodb/dumps` and named `<databasename>-<date and time>`.  
*Example: `dokku mongodb:dump api-production -tar`*

Restoring a database
--------------------
`mongodb:restore` can be used to restore dump created with `mongodb:dump` (or `mongodump` which it uses internally). It can be used with a gzipped dump.  
*Example: `dokku mongodb:restore api-production /path/to/dump/api-production-2015-03-09-16h54-43s.tar.gz`*  
  
It can also be used with a database dumped to a folder (`mongodb:dump` without the `-tar`argument)  
*Example: `dokku mongodb:restore api-production /path/to/dump/api-production-2015-03-09-16h54-43s/api-production/`*  
  
This will drop the database and re-create it completely from the dump.
