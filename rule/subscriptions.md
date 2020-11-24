## Get subscription relationship from Redis

Set up the Redis environment, taking MacOS X as an example:

```bash
 $ wget http://download.redis.io/releases/redis-4.0.14.tar.gz
$ tar xzf redis-4.0.14.tar.gz
$ cd redis-4.0.14
$ make && make install

# Start redis
$ redis-server
```

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules) and select the "Rules" tab on the left.

Then fill in the rule SQL:

```bash
SELECT * FROM "$events/client_connected"
```

![](./assets/rule-engine/redis_sub_1.png)

Related actions:

On the "Response Action" interface, select "Add Action", and then select "Get subscription relationship from Redis" in the "Action" drop-down box.

![](./assets/rule-engine/redis_sub_2.png)

Fill in the action parameters:

The "Get subscription list from Redis" action requires one parameter:

1). Associated resources. Now that the resource drop-down box is empty, and you can click "New Resource" in the upper right corner to create a Redis resource:

![](./assets/rule-engine/redis_sub_3.png)

Select "Redis single-node mode resources".

![](./assets/rule-engine/offline_msg_4.png)

Fill in the resource configuration:

Fill in the real Redis server address, keep other configurations as default, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "New" button.

![](./assets/rule-engine/redis_sub_5.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/redis_sub_6.png)

Return to the rule creation interface and click "New".

![](./assets/rule-engine/redis_sub_7.png)

The rule has been created, and a subscription relationship is inserted into Redis through the Redis CLI:

```bash
HSET mqtt:sub:test t1 1
```

![](./assets/rule-engine/redis_sub_8.png)

Log in to the device whose clientid is test via Dashboard:

![](./assets/rule-engine/redis_sub_9.png)

Check the subscription list, you can see that the **test** device has subscribed to the **t1** topic:

![](./assets/rule-engine/redis_sub_10.png)


## Get subscription relationship from MySQL

Set up a MySQL database and set the username and password to root/public. Take MacOS X as an example:
```bash
$ brew install mysql

$ brew services start mysql

$ mysql -u root -h localhost -p

ALTER USER 'root'@'localhost' IDENTIFIED BY 'public';
```

Initialize the MySQL database:
```bash
$ mysql -u root -h localhost -ppublic

create database mqtt;
```

Create the mqtt_sub table:
```sql
DROP TABLE IF EXISTS `mqtt_sub`;

CREATE TABLE `mqtt_sub` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `clientid` varchar(64) DEFAULT NULL,
  `topic` varchar(180) DEFAULT NULL,
  `qos` tinyint(1) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `mqtt_sub_idx` (`clientid`,`topic`,`qos`),
  UNIQUE KEY `mqtt_sub_key` (`clientid`,`topic`),
  INDEX topic_index(`id`, `topic`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8MB4;
```

{% hint style="danger" %}

The subscription relationship table structure cannot be modified, please use the above SQL statement to create

{% endhint %}

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules) and select the "Rules" tab on the left.

Then fill in the rule SQL:

```bash
SELECT * FROM "$events/client_connected"
```

![](./assets/rule-engine/mysql_sub_01.png)

Related actions:

Select "Add Action" in the "Response Action" interface, and then select "Get Subscription List from MySQL" in the "Add Action" drop-down box

![](./assets/rule-engine/mysql_sub_02.png)

Fill in the action parameters:

The "Get subscription list from MySQL" action requires one parameter:

1). Associated resources. Now that the resource drop-down box is empty, and you can click "New" in the upper right corner to create a MySQL resource:

![](./assets/rule-engine/mysql_sub_03.png)

The "Create Resource" dialog box pops up

![](./assets/rule-engine/mysql_sub_04.png)

Fill in the resource configuration:

Fill in the real MySQL server address and  corresponding values for other configuration, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/mysql_sub_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/mysql_sub_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/mysql_sub_07.png)

The rule has been created, and a subscription relationship is inserted into MySQL through "mysql":

```
insert into mqtt_sub(clientid, topic, qos) values("test", "t1", 1);
```

![](./assets/rule-engine/mysql_sub_08.png)

Log in to the device whose clientid is test via Dashboard:

![](./assets/rule-engine/mysql_sub_09.png)

Check the "Subscription" list, you can see that the Broker obtains the subscription relationship from MySQL and subscribes to the agent device:

![](./assets/rule-engine/mysql_sub_10.png)


## Get subscription relationship from PostgreSQL

Build a PostgreSQL database, and take MacOS X as an example:
```bash
$ brew install postgresql
$ brew services start postgresql
```

Create the mqtt database:

```
# Create a database named 'mqtt' with the username postgres
$ createdb -U postgres mqtt

$ psql -U postgres mqtt

mqtt=> \dn;
List of schemas
Name  | Owner
--------+-------
public | postgres
(1 row)
```

Create the mqtt_sub table:

```sql
$ psql -U postgres mqtt
CREATE TABLE mqtt_sub(
  id SERIAL8 primary key,
  clientid character varying(64),
  topic character varying(255),
  qos integer,
  UNIQUE (clientid, topic)
);
```

{% hint style="danger" %}

The subscription relationship table structure cannot be modified. Please use the above SQL statement to create

{% endhint %}

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules) and select the "Rules" tab on the left.

Then fill in the rule SQL:

```bash
SELECT * FROM "$events/client_connected"
```

![](./assets/rule-engine/pg_sub_01.png)

Related actions:

Select "Add Action" on the "Response Action" interface, and then select "Get Subscription List from PostgreSQL" in the "Add Action" drop-down box

![](./assets/rule-engine/pg_sub_02.png)

Fill in the action parameters:

The "Get subscription list from PostgreSQL" action requires one parameter:

1). Associated resources. Now that the resource drop-down box is empty, and you can click "New" in the upper right corner to create a PostgreSQL resource:

![](./assets/rule-engine/pg_sub_03.png)

The "Create Resource" dialog box pops up

![](./assets/rule-engine/pg_sub_04.png)

Fill in the resource configuration:

 Fill in the real PostgreSQL server address and corresponding values for other configuration, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/pg_sub_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/pg_sub_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/pg_sub_07.png)

The rule has been created, and a subscription relationship is inserted into PostgreSQL through "psql"

```
insert into mqtt_sub(clientid, topic, qos) values('test', 't1', 1)'
```

![](./assets/rule-engine/pg_sub_08.png)

Log in to the device whose clientid is test via Dashboard:

![](./assets/rule-engine/pg_sub_09.png)

Check the "Subscriptions" list, you can see that the Broker obtains the subscription relationship from PostgreSQL and subscribes to the agent device:

![](./assets/rule-engine/pg_sub_10.png)

## Get subscription relationship from Cassandra

Set up the Cassandra database and set the user name and password to root/public. Take MacOS X as an example:
```bash
$ brew install cassandra
## Modify the configuration and turn off anonymous authentication
$  vim /usr/local/etc/cassandra/cassandra.yaml

    authenticator: PasswordAuthenticator
    authorizer: CassandraAuthorizer

$ brew services start cassandra

## Create root user
$ cqlsh -ucassandra -pcassandra

create user root with password 'public' superuser;
```

Create the "mqtt" tablespace:
```bash
$ cqlsh -uroot -ppublic

CREATE KEYSPACE mqtt WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '1'}  AND durable_writes = true;
```

Create the mqtt_sub table:

```sql

CREATE TABLE mqtt_sub (
    clientid text,
    topic text,
    qos int,
    PRIMARY KEY (clientid, topic)
) WITH CLUSTERING ORDER BY (topic ASC)
    AND bloom_filter_fp_chance = 0.01
    AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
    AND comment = ''
    AND compaction = {'class': 'org.apache.cassandra.db.compaction.SizeTieredCompactionStrategy', 'max_threshold': '32', 'min_threshold': '4'}
    AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND crc_check_chance = 1.0
    AND dclocal_read_repair_chance = 0.1
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair_chance = 0.0
    AND speculative_retry = '99PERCENTILE';

```

{% hint style="danger" %}

The subscription relationship table structure cannot be modified. Please use the above SQL statement to create

{% endhint %}

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules) and select the "Rules" tab on the left.

Then fill in the rule SQL:

```bash
SELECT * FROM "$events/client_connected"
```

![](./assets/rule-engine/cass_sub_01.png)

Related actions:

Select "Add Action" on the "Response Action" interface, and then select "Get Subscription List from Cassandra" in the "Add Action" drop-down box

![](./assets/rule-engine/cass_sub_02.png)

Fill in the action parameters:

The "Get subscription list from Cassandra" action requires one parameter:

1). Associated resources. Now that the resource drop-down box is empty, and you can click "New" in the upper right corner to create a Cassandra resource:

![](./assets/rule-engine/cass_sub_03.png)

The "Create Resource" dialog box pops up

![](./assets/rule-engine/cass_sub_04.png)

Fill in the resource configuration:

Fill in the real Cassandra server address and  corresponding values for other configuration, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/cass_sub_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/cass_sub_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/cass_sub_07.png)

The rule has been created, and a subscription relationship is inserted into Cassandra through "cqlsh":

```
insert into mqtt_sub(clientid, topic, qos) values('test', 't1', 1);
```

![](./assets/rule-engine/cass_sub_08.png)

Log in to the device whose clientid is test via Dashboard:

![](./assets/rule-engine/cass_sub_09.png)

Check the "Subscription" list, and you can see that the Broker obtains the subscription relationship from Cassandra and subscribes to the agent device:

![](./assets/rule-engine/cass_sub_10.png)

## Get subscription relationship from MongoDB

Set up a MongoDB database and set the user name and password to root/public. Take MacOS X as an example:
```bash
$ brew install mongodb
$ brew services start mongodb

## Add root/public user
$ use mqtt;
$ db.createUser({user: "root", pwd: "public", roles: [{role: "readWrite", db: "mqtt"}]});

## Modify the configuration and turn off anonymous authentication
$ vi /usr/local/etc/mongod.conf

    security:
    authorization: enabled

$ brew services restart mongodb
```

Create the mqtt_sub table:
```sql
$ mongo 127.0.0.1/mqtt -uroot -ppublic
db.createCollection("mqtt_sub");
```

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules) and select the "Rules" tab on the left.

Then fill in the rule SQL:

```bash
SELECT * FROM "$events/client_connected"
```

![](./assets/rule-engine/mongo_sub_01.png)

Related actions:

Select "Add Action" on the "Response Action" interface, and then select "Get Subscription List from MongoDB" in the "Add Action" drop-down box

![](./assets/rule-engine/mongo_sub_02.png)

Fill in the action parameters:

The "Get subscription list from MongoDB" action requires one parameter:

1). Associated resources. Now that the resource drop-down box is empty, and you can click "New" in the upper right corner to create a MongoDB resource:

![](./assets/rule-engine/mongo_sub_03.png)

The "Create Resource" dialog box pops up

![](./assets/rule-engine/mongo_sub_04.png)

Fill in the resource configuration:

Fill in the real MongoDB server address and corresponding values for other configuration, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/mongo_sub_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/mongo_sub_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/mongo_sub_07.png)

The rule has been created, and a subscription relationship is inserted into MongoDB through "mongo"

```
db.mqtt_sub.insert({clientid: "test", topic: "t1", qos: 1})
```

![](./assets/rule-engine/mongo_sub_08.png)

Log in to the device whose clientid is test via Dashboard:

![](./assets/rule-engine/mongo_sub_09.png)

Check the "Subscription" list, and you can see that the Broker obtains the subscription relationship from MongoDB and subscribes to the agent device:

![](./assets/rule-engine/mongo_sub_10.png)
