# GetEventsMe - Python Version

En este caso no es necesario, ya que la función para obtener eventos ya tiene la lógica para obtener los eventos añadidos por un usuario.

```python
if "addedBy" in event:
  response = table.query(
      IndexName="addedBy-index",
      KeyConditionExpression=Key('addedBy').eq(event["addedBy"])
      )
else:
  response = table.scan()
```

[< Volver al Laboratorio 07 ](../../lab-07#crear-endpoint-para-obtener-los-eventos-de-un-usuarioget-eventsme) 
