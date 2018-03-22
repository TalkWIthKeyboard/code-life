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

## Influxdb
### Typescript
> use 'Influx'

```Typescript
const config = require('config')
import * as Influx from 'influx'

const influxDB = new Influx.InfluxDB({
  host: config.has('influx.host') ? config.get('influx.host') : 'localhost',
  port: config.has('influx.port') ? config.get('influx.port') : '8086',
  database: 'test',
  schema: [
    {
      measurement: 'record_data',
      fields: { default: Influx.FieldType.INTEGER },
      tags: ['CPU', 'memory'],
    },
  ],
})

let pointsQueue: any[] = []
let lastWriteTime = Date.now()

async function influxSaveData(measurement, tagObject) {
  if (config.has('influx')) {
    pointsQueue.push({
      measurement,
      tags: tagObject,
      fields: { default: 1 },
      timestamp: Date.now() * 1e6,
    })
    if (pointsQueue.length >= 100 || Date.now() - lastWriteTime > 20000) {
      try {
        await influxDB.writePoints(pointsQueue)
        debug('It was success to save to influxDB.')
      } catch (err) {
        console.error(`Error saving db to influxDB, ${err.stack}`)
      }
      pointsQueue = []
      lastWriteTime = Date.now()
    }
  }
}
```