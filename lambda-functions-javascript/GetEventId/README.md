# Get Event by id - Javascript Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../lambda-functions-javascript/EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```javascript
/**
 * This lambda function is integrated with the following API methods:
 * GET /events/{id}
 *
 * Its purpose is to get an event from our DynamoDB table
 */

const AWS = require('aws-sdk');
const dynamoDb = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {

    console.log('Initiating GetEventId...');
    console.log(`Received event from API Gateway: ${JSON.stringify(event)}`);
    
    const TableName = 'events_jcs';
    const { id } = event;

    const params = {
        TableName,
        KeyConditionExpression: "#id = :id",
        ExpressionAttributeNames:{
           "#id": "id"
        },
        ExpressionAttributeValues: {
          ":id": id
        }
    };
    
    let response
    try {
        response = await dynamoDb.query(params).promise();
    } catch (err) {
        console.log(`${err.code} ${err.message}`);
        console.log('Check your DynamoDB table...');
        
        return {statusCode: err.code, error: err.message};
    }
    console.log("GetItem succeeded:");
    console.log(`Received response from DynamoDB: ${JSON.stringify(response)}`);
    return {statusCode: 200, body: JSON.stringify(response)};
};
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-recuperar-el-detalle-de-un-eventoget-eventseventid) 
