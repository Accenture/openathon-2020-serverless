# CreateEvent - Python Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```python
# This lambda function is integrated with the following API methods:
# POST /events
#
# Its purpose is create events in DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError
import uuid

def lambda_handler(event, context):

    print('Initiating CreateEvent...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))
    
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events_XXXX')
    
    try:
	event["id"] = str(uuid.uuid4())
	response = table.put_item(Item=event)
    except ClientError as e:
	print(e.response['Error']['Message'])
    else:
	print("Post event succeeded:")
	print("Received response from DynamoDB: " + json.dumps(response, indent=2))
	return event

```


[< Volver al Laboratorio 07 ](../../lab-07#lab-07#crear-endpoint-para-dar-de-alta-eventos-post-events) 

