## MongoDB

### Python
> use `pymongo`

```python
# coding=utf-8

import pymongo
import sys
import traceback

MONGODB_CONFIG = {
    'host': '127.0.0.1',
    'port': 27017,
    'db_name': 'admin',
    'username': 'admin',
    'password': 'password'
}

class MongoConn(object):
    def __init__(self):
        try:
            self.conn = pymongo.MongoClient(MONGODB_CONFIG['host'], MONGODB_CONFIG['port'])
            self.db = self.conn[MONGODB_CONFIG['db_name']]
            self.username = MONGODB_CONFIG['username']
            self.password = MONGODB_CONFIG['password']

            if self.username and self.password:
                self.connected = self.db.authenticate(self.username, self.password)
            else:
                self.connected = True
        except Exception:
            print traceback.format_exc()
            print 'Connect Mongodb Database Fail.'
            sys.exit(1)
```

## Mysql
### Python
> use `MySqldb`

```python
# coding=utf-8

import MySQLdb
import sys
import traceback

MYSQL_CONFIG = {
    'host': '127.0.0.1',
    'db_name': 'admin',
    'username': 'root',
    'password': 'password'
}

class MysqlConn(object):
    def __init__(self):
        try:
            self.conn = MySQLdb.connect(
                MYSQL_CONFIG['host'],
                MYSQL_CONFIG['username'],
                MYSQL_CONFIG['password'],
                MYSQL_CONFIG['db_name'],
                charset="utf8"
            )
            self._cursor = self.conn.cursor()
        except Exception:
            print traceback.format_exc()
            print 'Connect Mysql Database Fail.'
            sys.exit(1)

    def excuse(self, query):
        try:
            cursor = self._cursor
            cursor.execute(query)
            return cursor.fetchall()
        except Exception:
            print traceback.format_exc()
```