
emqttd_auth_redis
=================

emqttd Redis Plugin

Build Plugin
------------

```
make && make tests
```

Configure Plugin
----------------

File: etc/emqttd_auth_redis.conf

```erlang
{redis_pool, [
  %% pool options
  {pool_size, 8},
  {auto_reconnect, 2},

  %% redis options
  {host, "127.0.0.1"},
  {port, 6379},
  {database, 0},
  {password, ""}
]}.

%% Variables: %u = username, %c = clientid

%% HMGET mqtt_user:%u password
{authcmd, "HGET mqtt_user:%u password"}.

%% Password hash algorithm: plain, md5, sha, sha256, pbkdf2?
{password_hash, sha256}.

%% HMGET mqtt_user:%u is_superuser
{supercmd, "HGET mqtt_user:%u is_superuser"}.

%% HGETALL mqtt_acl:%u
{aclcmd, "HGETALL mqtt_acl:%u"}.

%% If no rules matched, return...
{acl_nomatch, deny}.

%% Load Subscriptions form Redis when client connected.
{subcmd, "HGETALL mqtt_sub:%u"}.
```

Super User
----------

```
HSET mqtt_user:<username> is_superuser 1
```

User Hash with Password
-----------------------

Set a 'user' hash with 'password' field, for example:

```
HSET mqtt_user:<username> password "passwd"
```

ACL Rule Hash
-------------

The plugin uses a redis hash to store ACL rules:

```
HSET mqtt_acl:<username> topic1 1
HSET mqtt_acl:<username> topic2 2
HSET mqtt_acl:<username> topic3 3
```

NOTE: 1: subscribe, 2: publish, 3: pubsub

Subscription Hash
-----------------

The plugin could store the static subscriptions into a redis Hash:

```
HSET mqtt_sub:<username> topic1 0
HSET mqtt_sub:<username> topic2 1
HSET mqtt_sub:<username> topic3 2
```

Load Plugin
-----------

```
./bin/emqttd_ctl plugins load emqttd_auth_redis
```

Author
------

Feng Lee <feng@emqtt.io>
