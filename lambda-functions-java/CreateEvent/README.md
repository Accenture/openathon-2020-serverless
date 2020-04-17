# CreateEvent - Java Version

1. Creamos una nueva clase pulsando con el botón derecho sobre el paquete com.accenture.aws.functions->New ->Class
2. Le ponemos un nombre significativo, por ejemplo CreateEventItem
3. Pegamos el siguiente código:
```java
		package com.accenture.aws.functions;
		
		import java.util.UUID;
		
		import com.accenture.aws.model.Event;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
		import com.amazonaws.services.dynamodbv2.document.DynamoDB;
		import com.amazonaws.services.dynamodbv2.document.Item;
		import com.amazonaws.services.dynamodbv2.document.PutItemOutcome;
		import com.amazonaws.services.dynamodbv2.document.Table;
		import com.amazonaws.services.lambda.runtime.Context;
		import com.amazonaws.services.lambda.runtime.RequestHandler;
		
		
		public class CreateEventItem implements RequestHandler<Event, Event> {
		
		@Override
		public Event handleRequest(Event event, Context context) {
		
		     // Creamos el cliente DynamoDB y
		     AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();
		     DynamoDB dynamoDB = new DynamoDB(client);
		     Table table = dynamoDB.getTable("events_XXXX");
		
		     // Creamos un id aleatorio
		     UUID id = UUID.randomUUID();
		     event.setId(id.toString());
		
		try {
		    System.out.println("Adding a new item..."+ event.toString());
		    PutItemOutcome outcome = table
			.putItem(new Item().withPrimaryKey("id",  id.toString()).
					with("addedBy", event.getAddedBy()).
					with("date", event.getDate()).
					with("description", event.getDescription()).
					with("location", event.getLocation()).
					with("title", event.getTitle()));
		
		    System.out.println("Add item succeeded:\n" + outcome.getPutItemResult());
		
		}
		catch (Exception e) {
		    System.err.println("Unable to add item: " + event.getTitle());
		    System.err.println(e.getMessage());
		}
		
		
		return event;
		
		}
		
		
	}
```
 >En esta clase, deberemos cambiar el nombre de la tabla "events_XXXX" por el que corresponda con la tabla que hemos creado en DynamoDB.
 
4. Subimos la función a AWS como explicamos en el [laboratorio 03](../EventsList#subir-la-funci%C3%B3n-a-aws)
5. Probamos la función lambda como explicamos también en el [laboratorio 03](..EventsList#comprobar-la-creaci%C3%B3n-de-la-funci%C3%B3n-en-aws-desde-eclipse), pero en este caso elegimos "Enter de JSON input for your function" y pegamos un json con el siguiente formato:
```json
{
    "title": "Evento 1",
    "location": "Málaga",
    "date": "1997-01-01",
    "description": "primer evento de prueba",
    "addedBy": "user@test.com",
    "id": ""
}
```






[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-dar-de-alta-eventos-post-events)

