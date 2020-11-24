## Save offline messages to Redis

Set up the Redis environment, and take MacOS X as an example:

```bash
 $ wget http://download.redis.io/releases/redis-4.0.14.tar.gz
$ tar xzf redis-4.0.14.tar.gz
$ cd redis-4.0.14
$ make && make install

# Start redis
$ redis-server
```

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules), Select the "Rules" tab on the left.

Then fill in the rule SQL:

FROM Description

 	**t/#**: Publishers publish a message to trigger saving offline messages to Redis

 	**$events/session_subscribed**: Subscribers subscribe to topics to trigger offline messages

​	**$events/message_acked**: After the subscriber replies to the message ACK, the offline message that has been received is triggered to delete

```bash
SELECT * FROM "t/#", "$events/session_subscribed", "$events/message_acked" WHERE topic =~ 't/#'
```

![](./assets/rule-engine/offline_msg_1.png)

Related actions:

In the "Response Action" interface, select "Add Action", and then select "Save Offline Messages to Redis" in the "Action" drop-down box.

![](./assets/rule-engine/offline_msg_2.png)

Fill in the action parameters:

The action of "Saving offline message to Redis"  requires two parameters:

1). Redis Key expired TTL

2). Associated resources. Now the resource drop-down box is empty, and you can click "New Resource" in the upper right corner to create a Redis resource:

![](./assets/rule-engine/offline_msg_3.png)

Select "Redis single-node mode resources".

![](./assets/rule-engine/offline_msg_4.png)

Fill in the resource configuration:

Fill in the real Redis server address, keep other configurations as default, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "New" button.

![](./assets/rule-engine/offline_msg_5.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/offline_msg_7.png)

Return to the rule creation interface and click "New".

![](./assets/rule-engine/offline_msg_6.png)

The rule has been created, and a piece of data is sent through the WebSocket client of Dashboard **(The QoS of the published message must be greater than 0):**

![](./assets/rule-engine/offline_msg_8.png)


After the message is sent, you can see that the message is saved in Redis through the Redis CLI:

```bash
$ redis-cli

KEYS mqtt:msg\*

hgetall Key
```

![](./assets/rule-engine/offline_msg_10.png)

Use another client to subscribe to the topic "t/1" **(The QoS of the subscribed topic must be greater than 0, otherwise the message will be received repeatedly, and the topic wildcard subscription method is not supported to obtain offline messages)**:

![](./assets/rule-engine/offline_msg_11.png)

After subscribing, the offline messages saved in Redis are received  immediately:

![](./assets/rule-engine/offline_msg_12.png)

After offline messages are received, they will be deleted in Redis:

![](./assets/rule-engine/offline_msg_13.png)

## Save offline messages to MySQL

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

Create the mqtt_msg table:
```sql
DROP TABLE IF EXISTS `mqtt_msg`;
CREATE TABLE `mqtt_msg` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `msgid` varchar(64) DEFAULT NULL,
  `topic` varchar(180) NOT NULL,
  `sender` varchar(64) DEFAULT NULL,
  `qos` tinyint(1) NOT NULL DEFAULT '0',
  `retain` tinyint(1) DEFAULT NULL,
  `payload` blob,
  `arrived` datetime NOT NULL,
  PRIMARY KEY (`id`),
  INDEX topic_index(`id`, `topic`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8MB4;
```

{% hint style="danger" %}

The message table structure cannot be modified. Please use the above SQL statement to create

{% endhint %}

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules) and select the "Rules" tab on the left.

Then fill in the rule SQL:

FROM Description

 	**t/#**: Publishers publish a message to trigger saving offline messages to Redis

 	**$events/session_subscribed**: Subscribers subscribe to topics to trigger offline messages

​	**$events/message_acked**: After the subscriber replies to the message ACK, the offline message that has been received is triggered to delete

```bash
SELECT * FROM "t/#", "$events/session_subscribed", "$events/message_acked" WHERE topic =~ 't/#'
```

![](./assets/rule-engine/mysql_offline_msg_01.png)

Related actions:

Select "Add Action" on the "Response Action" interface, and then select "Save Offline Messages to MySQL" in the drop-down box of "Action".

![](./assets/rule-engine/mysql_offline_msg_02.png)


Now that the resource drop-down box is empty, and you can click "New" in the upper right corner to create a MySQL resource:

![](./assets/rule-engine/mysql_offline_msg_03.png)

"Create Resource" dialog box pops up

![](./assets/rule-engine/mysql_offline_msg_04.png)

Fill in the resource configuration:

Fill in the real MySQL server address and the corresponding values for other configurations, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/mysql_offline_msg_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/mysql_offline_msg_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/mysql_offline_msg_07.png)

The rule has been created, and a piece of data is sent through the WebSocket client of Dashboard**(The QoS of the published message must be greater than 0):**

![](./assets/rule-engine/mysql_offline_msg_08.png)

After the message is sent, you can check the message is saved in MySQL through mysql:

![](./assets/rule-engine/mysql_offline_msg_09.png)

Use another client to subscribe to the topic "t/1" **(The QoS of the subscribed topic must be greater than 0, otherwise the message will be received repeatedly)**:

![](./assets/rule-engine/mysql_offline_msg_10.png)

After subscribing, the offline messages saved in MySQL are received  immediately:

![](./assets/rule-engine/mysql_offline_msg_11.png)

After offline messages are received, they will be deleted in MySQL:

![](./assets/rule-engine/mysql_offline_msg_12.png)


## Save offline messages to PostgreSQL

Set upCreate the mqtt database: a PostgreSQL database, and take MacOS X as an example:
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

Create the mqtt_msg table:

```sql
$ psql -U postgres mqtt

CREATE TABLE mqtt_msg (
  id SERIAL8 primary key,
  msgid character varying(64),
  sender character varying(64),
  topic character varying(255),
  qos integer,
  retain integer,
  payload text,
  arrived timestamp without time zone
);
```

{% hint style="danger" %}

The message table structure cannot be modified, please use the above SQL statement to create

{% endhint %}

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules), Select the "Rules" tab on the left.

Then fill in the rule SQL:

FROM Descrption

 	**t/#**: Publishers publish a message to trigger saving offline messages to Redis

 	**$events/session_subscribed**: Subscribers subscribe to topics to trigger offline messages

​	**$events/message_acked**: After the subscriber replies to the message ACK, the offline message that has been received is triggered to delete

```bash
SELECT * FROM "t/#", "$events/session_subscribed", "$events/message_acked" WHERE topic =~ 't/#'
```

![](./assets/rule-engine/pg_offline_msg_01.png)

Related actions:

Select "Add Action" on the "Response Action" interface, and then select "Save Offline Messages to PostgreSQL" in the drop-down box of "Action".

![](./assets/rule-engine/pg_offline_msg_02.png)


Now that the resource drop-down box is empty, and you can click "New" in the upper right corner to create a PostgreSQL resource:

![](./assets/rule-engine/pg_offline_msg_03.png)

"Create Resource" dialog box pops up

![](./assets/rule-engine/pg_offline_msg_04.png)

Fill in the resource configuration:

Fill in the real PostgreSQL server address and the corresponding values for other configurations, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/pg_offline_msg_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/pg_offline_msg_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/pg_offline_msg_07.png)

The rule has been created, and a piece of data is sent through the WebSocket client of Dashboard**(The QoS of the published message must be greater than 0):**

![](./assets/rule-engine/pg_offline_msg_08.png)

After the message is sent, you can see that the message is saved in PostgreSQL through psql:

![](./assets/rule-engine/pg_offline_msg_09.png)

Use another client to subscribe to the topic "t/1" **(The QoS of the subscribed topic must be greater than 0, otherwise the message will be received repeatedly)**:

![](./assets/rule-engine/pg_offline_msg_10.png)

After subscribing, the offline messages saved in PostgreSQL  are received  immediately:

![](./assets/rule-engine/pg_offline_msg_11.png)

After offline messages are received, they will be deleted in PostgreSQL:

![](./assets/rule-engine/pg_offline_msg_12.png)


## Save offline messages to  Cassandra

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

Initialize the Cassandra tablespace:
```bash
$ cqlsh -uroot -ppublic

CREATE KEYSPACE mqtt WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '1'}  AND durable_writes = true;

```

Create the mqtt_msg table:
```sql
CREATE TABLE mqtt.mqtt_msg (
    topic text,
    msgid text,
    arrived timestamp,
    payload text,
    qos int,
    retain int,
    sender text,
    PRIMARY KEY (topic, msgid)
) WITH CLUSTERING ORDER BY (msgid DESC)
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

The message table structure cannot be modified, please use the above SQL statement to create

{% endhint %}

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules), Select the "Rules" tab on the left.

Then fill in the rule SQL:

FROM Descrption

​	 **t/#**: Publishers publish a message to trigger saving offline messages to Redis

 	**$events/session_subscribed**: Subscribers subscribe to topics to trigger offline messages

​	**$events/message_acked**: After the subscriber replies to the message ACK, the offline message that has been received is triggered to delete

```bash
SELECT * FROM "t/#", "$events/session_subscribed", "$events/message_acked" WHERE topic =~ 't/#'
```

![](./assets/rule-engine/cass_offline_msg_01.png)

Related actions:

Select "Add Action" on the "Response Action" interface, and then select "Save Offline Messages to Cassandra" in the drop-down box of "Action".

![](./assets/rule-engine/cass_offline_msg_02.png)


Now that the resource drop-down box is empty, and you can click "New" in the upper right corner to create a Cassandra resource:

![](./assets/rule-engine/cass_offline_msg_03.png)

"Create Resource" dialog box pops up

![](./assets/rule-engine/cass_offline_msg_04.png)

Fill in the resource configuration:

Fill in the real Cassandra  server address and the corresponding values for other configurations, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/cass_offline_msg_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/cass_offline_msg_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/cass_offline_msg_07.png)

The rule has been created, and a piece of data is sent through the WebSocket client of Dashboard**(The QoS of the published message must be greater than 0):**

![](./assets/rule-engine/cass_offline_msg_08.png)

After the message is sent,you can see the message is saved in Cassandra through cqlsh:

![](./assets/rule-engine/cass_offline_msg_09.png)

Use another client to subscribe to the topic "t/1" (The QoS of the subscribed topic must be greater than 0, otherwise the message will be received repeatedly):

![](./assets/rule-engine/cass_offline_msg_10.png)

After subscribing, the offline messages saved in Cassandra are received  immediately:

![](./assets/rule-engine/cass_offline_msg_11.png)

Offline messages will be deleted in Cassandra after being received:

![](./assets/rule-engine/cass_offline_msg_12.png)


## Save offline messages to MongoDB

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

Create the mqtt_msg table:
```bash
$ mongo 127.0.0.1/mqtt -uroot -ppublic
db.createCollection("mqtt_msg");
```

Create rules:

Open [EMQ X Dashboard](http://127.0.0.1:18083/#/rules), Select the "Rules" tab on the left.

Then fill in the rule SQL:

FROM Descrption

 	**t/#**: Publishers publish a message to trigger saving offline messages to Redis

 	**$events/session_subscribed**: Subscribers subscribe to topics to trigger offline messages

​	**$events/message_acked**: After the subscriber replies to the message ACK, the offline message that has been received is triggered to delete

```bash
SELECT * FROM "t/#", "$events/session_subscribed", "$events/message_acked" WHERE topic =~ 't/#'
```

![](./assets/rule-engine/mongo_offline_msg_01.png)

Related actions:

Select "Add Action" on the "Response Action" interface, and then select "Save Offline Messages to MongoDB" in the drop-down box of "Action".

![](./assets/rule-engine/mongo_offline_msg_02.png)


Now that the resource drop-down box is empty, you can click "New" in the upper right corner to create a MongoDB resource:

![](./assets/rule-engine/mongo_offline_msg_03.png)

"Create Resource" dialog box pops up

![](./assets/rule-engine/mongo_offline_msg_04.png)

Fill in the resource configuration:

Fill in the real MongoDB server address and the corresponding values for other configurations, and then click the "Test Connection" button to ensure that the connection test is successful.

Finally click the "OK" button.

![](./assets/rule-engine/mongo_offline_msg_05.png)

Return to the response action interface and click "OK".

![](./assets/rule-engine/mongo_offline_msg_06.png)

Return to the rule creation interface and click "Create".

![](./assets/rule-engine/mongo_offline_msg_07.png)

The rule has been created, and a piece of data is sent through the WebSocket client of Dashboard **(The QoS of the published message must be greater than 0):**

![](./assets/rule-engine/mongo_offline_msg_08.png)

After the message is sent, you can see that the message is saved in MongoDB through mongo:

```
db.mqtt_msg.find()
```

![](./assets/rule-engine/mongo_offline_msg_09.png)

Use another client to subscribe to the topic "t/1" (The QoS of the subscribed topic must be greater than 0, otherwise the message will be received repeatedly):

![](./assets/rule-engine/mongo_offline_msg_10.png)

After subscribing, the offline messages saved in  MongoDB are received  immediately:

![](./assets/rule-engine/mongo_offline_msg_11.png)

After offline messages are received, they will be deleted in MongoDB:

![](./assets/rule-engine/mongo_offline_msg_12.png)