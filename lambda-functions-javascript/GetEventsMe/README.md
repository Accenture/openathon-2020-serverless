# GetEventsMe - Javascript Version

En este caso no es necesario, ya que la función para obtener eventos ya tiene la lógica para obtener los eventos añadidos por un usuario.

```javascript
if(addedBy){
    const params = {
        TableName,
        IndexName: "addedBy-index",
        KeyConditionExpression: "#addedBy = :addedBy",
        ExpressionAttributeNames:{
            "#addedBy": "addedBy",
        },
        ExpressionAttributeValues: {
            ":addedBy": addedBy
        }
    };
    response = await dynamoDb.query(params).promise();
} else {
    response = await dynamoDb.scan({TableName}).promise();
}
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-obtener-los-eventos-de-un-usuarioget-eventsme) 
