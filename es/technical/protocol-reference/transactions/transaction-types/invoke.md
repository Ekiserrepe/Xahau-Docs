---
description: >-
  Una transacción Invoke se utiliza para llamar a un hook, que es un fragmento de código que se ejecuta en respuesta a ciertas operaciones del ledger.
---

# Invoke

\[[Fuente](https://github.com/ripple/rippled/blob/develop/src/ripple/app/tx/impl/Invoke.cpp)]

_(Añadido por la \[enmienda Hooks]\[].)_

### Ejemplo

```json
{
    "TransactionType": "Invoke",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "Blob": "697066733A2F2F4445414442454546",
    "Destination": "rPT1Sjq2YGrBMTttX4GZHjKu9dyfzbpAYe"
}
```

| Campo         | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                        |
| ------------- | --------- | ------------------- | -------------------------------------------------------------------------------------------------- |
| `Account`     | String    | AccountID           | La dirección de la cuenta que invoca el hook.                                              |
| `Blob`        | String    | Blob                | _(Opcional)_ Un blob de datos que se pasa al hook. (máximo 512 bytes)                            |
| `Destination` | String    | AccountID           | _(Opcional)_ La dirección de la cuenta que es el objetivo del hook.                            |
| `InvoiceID`   | String    | Hash256             | _(Opcional)_ Hash arbitrario de 256 bits que representa una razón específica o identificador para este pago. |

### Coste Especial de la Transacción

La transacción Invoke tiene un coste de transacción estándar, más un coste adicional basado en el tamaño de los campos Blob y HookParameters.

### Casos de error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones Invoke pueden resultar en los siguientes códigos de resultado de transacción:

| Código de error      | Descripción                                                              |
| --------------- | ------------------------------------------------------------------------ |
| `temDISABLED`   | Ocurre si la enmienda Hooks no está habilitada.                            |
| `temMALFORMED`  | Ocurre si el campo Blob es mayor de 128 KiB.                         |
| `terNO_ACCOUNT` | Ocurre si la cuenta remitente o la cuenta de destino no existe. |
