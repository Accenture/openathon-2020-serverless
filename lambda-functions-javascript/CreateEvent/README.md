# CreateEvent - Javascript Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```javascript
/**
 * # This lambda function is integrated with the following API methods:
 * POST /events
 *
 * Its purpose is create events in DynamoDB table
 */

const AWS = require('aws-sdk');
const dynamoDb = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event, context) => {
    
    Object.assign(event, {id: context.awsRequestId});
    
    console.log('Initiating CreateEvent...');
    console.log(`Received event from API Gateway: ${JSON.stringify(event)}`);

    const TableName = 'events_xxxx';

    const params = {
        TableName,
        Item: event
    }

    let response;
    try {
        response = await dynamoDb.put(params).promise();
    } catch (err) {
        console.log(`${err.code} ${err.message}`);
        return {statusCode: err.code, error: err.message};
    }
    console.log('Post event succeeded:');
    console.log(`Received response from DynamoDB: ${JSON.stringify(response)}`);
    return {statusCode: 200, body: response};
};

```


[< Volver al Laboratorio 07 ](../../lab-07#lab-07#crear-endpoint-para-dar-de-alta-eventos-post-events) 

