---
description: >-
  Una transacción TicketCreate reserva uno o más [números de secuencia][Sequence Number] como Tickets.
---

# TicketCreate

[\[Fuente\]](https://github.com/ripple/rippled/blob/develop/src/ripple/app/tx/impl/CreateTicket.cpp)

_(Añadido en la \[enmienda TicketBatch]\[].)_

### Ejemplo

```json
{
    "TransactionType": "TicketCreate",
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "Fee": "10",
    "Sequence": 381,
    "TicketCount": 10
}
```

| Campo         | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                            |
| ------------- | --------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `TicketCount` | Number    | UInt32              | Cantidad de Tickets a crear. Debe ser un número positivo y no puede causar que la cuenta posea más de 250 Tickets después de ejecutar esta transacción. |

Si la transacción no puede crear todos los Tickets solicitados (ya sea debido al límite de 250 Tickets o a la reserva del propietario), falla y no crea ningún Ticket. Para verificar cuántos Tickets posee actualmente una cuenta, utiliza el \[método account_info]\[] y revisa el campo `account_data.TicketCount`.

**Consejo:** Esta transacción aumenta el [número de secuencia][Sequence Number] de la cuenta que envía en 1 _más_ el número de tickets creados (`TicketCount`). Esta es la única transacción que aumenta el número de secuencia de una cuenta en más de 1.

### Casos de error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones de tipo \{{currentpage.name\}} pueden resultar en los siguientes códigos de resultado de transacción:

| Código de error                | Descripción                                                                                                                                                 |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `temINVALID_COUNT`        | El campo `TicketCount` es inválido. Debe ser un entero entre 1 y 250.                                                                                    |
| `tecDIR_FULL`             | Esta transacción causaría que la cuenta posea más del límite de 250 Tickets a la vez, o más del número máximo de objetos del libro mayor en general. |
| `tecINSUFFICIENT_RESERVE` | La cuenta que envía no tiene suficiente XAH para cumplir con la reserva del propietario para todos los Tickets solicitados.                                                        |
