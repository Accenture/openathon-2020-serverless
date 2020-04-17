# Edit Event - Python Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../lambda-functions-python/EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```python
# This lambda function is integrated with the following API methods:
# PUT /events/{id}
#
# Its purpose is to edit an event from our DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError
import uuid

def lambda_handler(event, context):

    print('Initiating EditEvent...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))

    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events')
    
    try:
        response = table.update_item(
            Key={"id": event["id"]},
            ExpressionAttributeNames={
                "#addedBy": "addedBy",
                "#date": "date",
                "#description": "description",
                "#title": "title",
                "#location": "location"
            },
            ExpressionAttributeValues= {
                ":addedBy":event["addedBy"],
                ":date":event["date"],
                ":description":event["description"],
                ":title":event["title"],
                ":location":event["location"]
            },
            UpdateExpression =  ("SET #addedBy = :addedBy,"  
                        "#date = :date,"  
                        "#description = :description," 
                        "#title = :title," 
                        "#location = :location")
            )
    except ClientError as e:
    	print(e.response['Error']['Message'])
    	print('Check your DynamoDB table...')
    else:
    	print("UpdateItem succeeded:")
    	print("Received response from DynamoDB: " + json.dumps(response, indent=2))
    	return event
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-editar-eventos-put-eventseventsid) 
