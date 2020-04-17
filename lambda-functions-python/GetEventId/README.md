# Get Event by id - Python Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../lambda-functions-python/EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```python
# This lambda function is integrated with the following API methods:
# GET /events/{id}
#
# Its purpose is to get an event from our DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError

def lambda_handler(event, context):

    print('Initiating GetEventId...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))

    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events')

    try:
    	response_event = table.get_item(Key={'id': event["id"]})
    	item = response_event["Item"]
    except ClientError as e:
    	print(e.response['Error']['Message'])
    	print('Check your DynamoDB table...')
    else:
    	print("GetItem succeeded:")
    	print("Received response from DynamoDB: " + json.dumps(response_event, indent=2))
    	return item
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-recuperar-el-detalle-de-un-eventoget-eventseventid) 
