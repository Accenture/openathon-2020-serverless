# DeleteEvent - Java Version

1. Creamos una nueva clase pulsando con el botón derecho sobre el paquete com.accenture.aws.functions->New ->Class
2. Le ponemos un nombre significativo, por ejemplo DeleteEventItem
3. Pegamos el siguiente código:
```java
package com.accenture.aws.functions;

import com.accenture.aws.model.Event;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.document.DeleteItemOutcome;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.document.PrimaryKey;
import com.amazonaws.services.dynamodbv2.document.Table;
import com.amazonaws.services.dynamodbv2.document.spec.DeleteItemSpec;
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;

public class DeleteEventItem implements RequestHandler<Event, Event> {


	@Override
	public Event handleRequest(Event event, Context context) {

		DeleteItemOutcome outcome = null;

		// Configuramos el cliente de dynamodb y la tabla
		AmazonDynamoDB client = AmazonDynamoDBClientBuilder.defaultClient();

		DynamoDB dynamoDB = new DynamoDB(client);
	    Table table = dynamoDB.getTable("events_XXXX");

		// Creamos la query    
	    DeleteItemSpec deleteItemSpec = new DeleteItemSpec()
		    .withPrimaryKey(new PrimaryKey("id", event.getId()));

		try {
		    System.out.println("Attempting a conditional delete...");
		    outcome = table.deleteItem(deleteItemSpec);
		    System.out.println("DeleteItem succeeded "+outcome.getItem());
		}
		catch (Exception e) {
		    System.err.println("Unable to delete item: " + event.getId());
		    System.err.println(e.getMessage());
		}


		return new Event();
	}

}
```
 >En esta clase, deberemos cambiar el nombre de la tabla "events_XXXX" por el que corresponda con la tabla que hemos creado en DynamoDB.
 
4. Subimos la función a AWS como explicamos en el [laboratorio 03](../EventsList#subir-la-funci%C3%B3n-a-aws)
5. Probamos la función lambda como explicamos también en el [laboratorio 03](..EventsList#comprobar-la-creaci%C3%B3n-de-la-funci%C3%B3n-en-aws-desde-eclipse), pero en este caso elegimos "Enter de JSON input for your function" y pegamos un json con el siguiente formato:
```json
{
    "id": "aqui poner el id de un evento que hayas creado"
}
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-eliminar-un-eventodelete-eventseventid)
