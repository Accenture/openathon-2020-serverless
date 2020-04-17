# DeleteEvent - Python Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../lambda-functions-python/EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```python
# This lambda function is integrated with the following API methods:
# DELETE /events/{id}
#
# Its purpose is to delete a event in DynamoDB table

from __future__ import print_function
import boto3
import json
from boto3.dynamodb.conditions import Key
from botocore.exceptions import ClientError

def lambda_handler(event, context):

    print('Initiating DeleteEvents...')
    print("Received event from API Gateway: " + json.dumps(event, indent=2))

    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('events')

    try:
	    response = table.delete_item(Key={"id":event["id"]})
    except ClientError as e:
	    print(e.response['Error']['Message'])
	    print('Check your DynamoDB table...')
    else:
	    print("DeleteItem succeeded:")
	    print("Received response from DynamoDB: " + json.dumps(response, indent=2))
	    return
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-eliminar-un-eventodelete-eventseventid)
