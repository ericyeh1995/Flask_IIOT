# Flask IIOT
A IIOT application for traditional factory

![alt text][plan]

[plan]: https://github.com/ericyeh1995/Flask_IIOT/blob/master/plan_1.jpg "plan 1"

## User
Not usable as it is a work in progress
## Flask Contributor
### Data Collector: OPC-UA
Simple Connection
```python
from opcua import Client, ua

client = Client("opc.tcp://localhost:4840")
try:
    client.connect()
    print("OPCUA connection success!")
finally:
    client.disconnect()
```
Reading node
```python
from opcua import Client, ua

client = Client("opc.tcp://localhost:4840")
try:
    client.connect()
    node = client.get_node('node_id')
    while True:
        print(node.get_value())
        sleep(1)
finally:
    client.disconnect()
```
### Data Collector: Modbus TCP/IP
Reading holding registers from address 0 to 124 at port 502.
```python
from pymodbus.client.sync import ModbusTcpClient
client = ModbusTcpClient('localhost', port=502)
print(client.read_holding_registers(address=0, count=124, unit=1).registers)
client.close()
```
### MQTT Publisher
Publish message at port 1883
```python
from time import sleep
import paho.mqtt.publish as publish
import paho.mqtt.client as mqtt

def on_connect(client, userdata, flags, rc):
    print("rc: "+str(rc))
    
def on_publish(client, userdata, mid):
    print("mid: "+str(mid))
    
client = mqtt.Client()
client.on_connect = on_connect
client.on_publish = on_publish
client.connect(host='test.mosquitto.org', port=1883, keepalive=60)
client.loop_start()

while True:
    client.publish('test/123', 'message', qos=2)
    sleep(1)
```
### Web App
```python
@app.route('/')
def hello_world():
    return 'Hello, World!'
```
### ES Indexer
Index data to ES
```python
from datetime import datetime
from elasticsearch import Elasticsearch
msg = {
    "workingcode": "yaypython!",
    "string": "abcABC",
    "whole": 123,
    "float": 654.321,
    "timestamp": datetime.now(),
}
es = Elasticsearch()
es.indices.create(index='python-helloworld', ignore=400)
es.index(index="python-helloworld",
         doc_type="test-jsontype",
         id=42,
         body=msg)
```
getting data
```python
es.get(index="python-helloworld", doc_type="test-jsontype", id=42)
```
Using bulk action
```python
import json
from time import sleep
from datetime import datetime

from elasticsearch import Elasticsearch
from elasticsearch import helpers

es = Elasticsearch()

dict_data = {}
while True:
	# update dict_data
    actions = [
        {
            "_index": "python_data",
            "_type": "data",
            "_source": dict_data
        }]
    helpers.bulk(es, actions)
```
## Web Contributor
### MQTT Subscriber


## Future
 * IOT homepage: app status




