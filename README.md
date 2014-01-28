# Tangerine Deployment Debugging

These are my notes for dealing with Tangerine deployment issues.

## Problems replicating to master database

### Testing

Spin up a VM using a Vagrantfile from my [vagrant-couchdb](https://github.com/chrisekelley/vagrant-couchdb) repository.
The README has code for adding a squid instance in case there are proxy server issues.

#### Useful vagrant commands

    vagrant up
    vagrant ssh
    ssh vagrant@localhost -p 2222 - same as vagrant ssh
    vagrant halt

#### Useful couchdb commands

Creating a user for testing

       COUCH=http://localhost:5984
       curl -HContent-Type:application/json -vXPUT $COUCH/_users/org.couchdb.user:testuser \
       --data-binary '{"_id": "org.couchdb.user:testuser","name": "testuser","roles": [],"type": "user","password": "password"}'

### Implimenting an offline version

If replication just cannot happen, send the user a copy of the database.

Replication command:

       curl -v -H 'Content-Type: application/json' -X POST http://localhost:5984/_replicate -d ' \
       {"source": "http://username:password@server/couchdb", "target": "couchdb"} '

Copy the db to your /vagrant share:

    cd /usr/local/var/lib
    sudo tar -czvf couchdb.tar.gz couchdb
    sudo mv couchdb.tar.gz /vagrant/

Message for user:

There is a link at the top of http://localhost:5984/_utils/database.html?yourcouch to "Compact & Cleanup..."
that you must click to compact the db. (must be logged in for it to work - see lower right bottom corner to see if you are still logged in)
Once it is done, shutdown the db.
Zip the db. Check if the file size is sane enough for email
If sane, email it to me.
I will replicate it to the master db.

