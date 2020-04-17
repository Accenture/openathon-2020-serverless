# GetEventsMe - Java Version

1. Creamos una nueva clase pulsando con el botón derecho sobre el paquete com.accenture.aws.functions->New ->Class
2. Le ponemos un nombre significativo, por ejemplo ListEventsMe
3. Pegamos el siguiente código:

	 
```java
		package com.accenture.aws.functions;

		import java.util.ArrayList;
		import java.util.HashMap;
		import java.util.List;
		
		import com.accenture.aws.model.Event;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
		import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
		import com.amazonaws.services.dynamodbv2.document.DynamoDB;
		import com.amazonaws.services.dynamodbv2.document.Item;
		import com.amazonaws.services.dynamodbv2.document.ItemCollection;
		import com.amazonaws.services.dynamodbv2.document.ScanOutcome;
		import com.amazonaws.services.dynamodbv2.document.Table;
		import com.amazonaws.services.dynamodbv2.document.internal.IteratorSupport;
		import com.amazonaws.services.dynamodbv2.document.spec.ScanSpec;
		import com.amazonaws.services.lambda.runtime.Context;
		import com.amazonaws.services.lambda.runtime.RequestHandler;
		
		public class ListEventsMe implements RequestHandler<Event, List<Event>> {


		@Override
		public List<Event> handleRequest(Event event, Context context) {
			ItemCollection<ScanOutcome> items = null;
			
			System.out.println("Input "+event.getAddedBy());
	
			String addedBy = event.getAddedBy();
			
			//Configurar cliente de dynamodb y la tabla
			AmazonDynamoDB client = AmazonDynamoDBClientBuilder.standard().build();		
			DynamoDB dynamoDB = new DynamoDB(client);
		    Table table = dynamoDB.getTable("events_XXXX");
	
		    HashMap<String, String> nameMap = new HashMap<String, String>();
	        nameMap.put("#addedBy", "addedBy");
	
	        HashMap<String, Object> valueMap = new HashMap<String, Object>();
	        valueMap.put(":addedBy", addedBy);
	
	        // Construir la query
	        ScanSpec scanSpec = new ScanSpec()
	                .withFilterExpression("#addedBy = :addedBy").withNameMap(nameMap)
	                .withValueMap(valueMap);
		    try {	       
				items = table.scan(scanSpec);	       
		    }
		    catch (Exception e) {
		        System.err.println("Unable to read table by addedBy: " +addedBy);
		        System.err.println(e.getMessage());
		    }
		    List<Event> result = new ArrayList<Event>();
		    
		    
		    // Transformar los elementos en una lista de tipo Event
		    IteratorSupport<Item, ScanOutcome> iterator = items.iterator();
	        while (iterator.hasNext()) {
	            Item item = iterator.next();
	            Event eventItem = new Event(item.get("id").toString(),
		    			item.get("title").toString(), 
		    			item.get("date").toString(), 
		    			item.get("addedBy").toString(), 
		    			item.get("description").toString(), 
		    			item.get("location").toString());
		    	result.add(eventItem);
	        }
		    
		   
		    return result;
		}
	
	
		}
```
 >En esta clase, deberemos cambiar el nombre de la tabla "events_XXXX" por el que corresponda con la tabla que hemos creado en DynamoDB.
 
4. Subimos la función a AWS como explicamos en el [laboratorio 03](../EventsList#subir-la-funci%C3%B3n-a-aws)
5. Probamos la función lambda como explicamos también en el [laboratorio 03](..EventsList#comprobar-la-creaci%C3%B3n-de-la-funci%C3%B3n-en-aws-desde-eclipse), pero en este caso elegimos "Enter de JSON input for your function" y pegamos un json con el siguiente formato:
```json
{
    "addedBy": "aqui poner el addedBy que pusieras al crear el evento"
}
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-obtener-los-eventos-de-un-usuarioget-eventsme) 

