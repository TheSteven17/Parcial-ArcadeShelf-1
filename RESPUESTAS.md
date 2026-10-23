# Respuestas del parcial

1. ¿Qué error encontraste en la validación de `stock`?
El esquema GameCreate permitía valores mayores o iguales a cero (ge=0), lo que violaba la regla de negocio. Se corrigió cambiando el límite inferior a uno (ge=1) para garantizar que el stock esté entre 1 y 50.
2. ¿Qué hacía que un juego pareciera creado pero no quedara persistido?
En la función create_game del servicio, faltaba ejecutar el comando session.commit() después de agregar el juego a la sesión (session.add(game)). Al no confirmar la transacción, el registro solo existía en memoria temporalmente y no se guardaba en SQLite.
3. ¿Qué problema tenía el endpoint que lista juegos?
El decorador del endpoint GET /games en el router tenía definido su modelo de respuesta como un único objeto (response_model=GameResponse). Se modificó a una lista (response_model=list[GameResponse]) para que FastAPI pueda procesar y devolver múltiples registros correctamente.
4. ¿Cómo solucionaste la consulta de un ID inexistente?
En la función get_game del servicio, el método session.get retornaba None si el ID no existía, pero no lanzaba un error HTTP. Se agregó una validación if not game: para levantar manualmente una excepción HTTPException con código de estado 404 y el detalle "Game not found".
5. ¿Por qué DELETE podía responder correctamente y aun así no eliminar el registro de SQLite?
En la función delete_game del servicio, el registro se borraba de la sesión con session.delete(game), pero faltaba ejecutar session.commit() justo después. Sin el commit, la base de datos nunca recibía la instrucción final para eliminar el dato de forma permanente.