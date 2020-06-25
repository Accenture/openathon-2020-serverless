# Edit Event - Javascript Version

Primero tenemos que crear la funcion lambda, de la misma forma que en [lab-03](../lambda-functions-javascript/EventsList), pero el código fuente es el siguiente:
> :warning: **Recuerda sustituir el nombre de la tabla por el tuyo**

```javascript
/**
 * This lambda function is integrated with the following API methods:
 * PUT /events/{id}
 *
 * Its purpose is to edit an event from our DynamoDB table
 */

const AWS = require('aws-sdk');
const dynamoDb = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event, context) => {
    
    console.log('Initiating EditEvent...');
    console.log(`Received event from API Gateway: ${JSON.stringify(event)}`);
    
    const TableName = 'events_xxxx';
    const { id, addedBy, date, description, title, location } = event
    
    const params = {
        TableName,
        Key: {"id": id},
        ExpressionAttributeNames:{
           "#addedBy": "addedBy",
           "#date": "date",
           "#description": "description",
           "#title": "title",
           "#location": "location"
        },
        ExpressionAttributeValues: {
           ":addedBy": addedBy,
           ":date": date,
           ":description": description,
           ":title": title,
           ":location": location
        },
        UpdateExpression: 
            `SET #addedBy = :addedBy,
            #date = :date, 
            #description = :description, 
            #title = :title, 
            #location = :location`,
        ReturnValues: 'UPDATED_NEW'
    };

    let response;
    try {
        response = await dynamoDb.update(params).promise();
        console.log('UpdateItem succeeded:');
    	console.log(`Received response from DynamoDB: ${JSON.stringify(response)}`);
    } catch (err) {
        console.log(`${err.code} ${err.message}`);
        return {statusCode: err.code, error: err.message};
    }
    return {statusCode: 200, body: response};
};
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-editar-eventos-put-eventseventsid) 
