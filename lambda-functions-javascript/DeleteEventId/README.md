# DeleteEvent - Javascript Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../lambda-functions-javascript/EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```javascript
/**
 * This lambda function is integrated with the following API methods:
 * DELETE /events/{id}
 *
 * Its purpose is to delete a event in DynamoDB table
 */

const AWS = require('aws-sdk');
const dynamoDb = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {

    console.log('Initiating DeleteEvents...');
    console.log(`Received event from API Gateway: ${JSON.stringify(event)}`);
    
    const TableName = 'events_xxxx';
    const { id } = event;

    const params = {
        TableName,
        Key: {"id": id},
        ReturnValues: "ALL_OLD"
    };
    
    let response;
    try {
        response = await dynamoDb.delete(params).promise();
    } catch (err) {
        console.log(`${err.code} ${err.message}`);
        console.log('Check your DynamoDB table...');
        
        return {statusCode: err.code, error: err.message};
    }
    console.log("DeleteItem succeeded:");
    console.log(`Received response from DynamoDB: ${JSON.stringify(response)}`);
    return {statusCode: 200, body: JSON.stringify(response)};
};
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-eliminar-un-eventodelete-eventseventid)
