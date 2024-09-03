# Emitted Txn

_(Agregado por la \[enmienda Hooks]\[].)_

Un objeto `EmittedTxn` describe una transacción que ha sido emitida por un hook. El objeto contiene todos los campos de la transacción original, junto con detalles adicionales sobre la emisión.

### Ejemplo JSON

```json
{
  "Account": "rMPwD1b8dJUaqZHaBgEvFx4ENhtpPVvDsv",
  "Amount": "999999",
  "Destination": "rfCarbonVNTuXckX6x2qTMFmFSnm6dEWGX",
  "DestinationTag": 0,
  "EmitDetails": {
    "EmitBurden": "1",
    "EmitCallback": "rMPwD1b8dJUaqZHaBgEvFx4ENhtpPVvDsv",
    "EmitGeneration": 1,
    "EmitHookHash": "A9B5411F4A4368008B4736EEE47A34B0EFCBE74016B9B94CC6208FBC0BF5C0C2",
    "EmitNonce": "6B2A27D6864903A479614581A79D18E8C8ADCE01E3440C6E993BE07298ADC2A4",
    "EmitParentTxnID": "9763EB6B74AEF0F55F642243AD51F48490594434439002A6142E545E47318D56"
  },
  "Fee": "31",
  "FirstLedgerSequence": 7186113,
  "Flags": 2147483648,
  "LastLedgerSequence": 7186117,
  "Sequence": 0,
  "SigningPubKey": "000000000000000000000000000000000000000000000000000000000000000000",
  "SourceTag": 0,
  "TransactionType": "Payment"
}
```

### Campos

Un objeto `EmittedTxn` tiene los siguientes campos:

| Campo             | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                                                                                              |
| ----------------- | --------- | ------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `EmitDetails`     | Object    | Object              | Sí       | Contiene detalles sobre la emisión. Esto incluye la generación de la emisión, la carga de la emisión, la dirección de callback, el hash del hook que emitió la transacción, el nonce de la emisión y el ID de la transacción padre. |
| `TransactionType` | String    | UInt16              | Sí       | El tipo de transacción que fue emitida.                                                                                                                                                                                                            |
| `Account`         | String    | Account             | Sí       | La cuenta que emitió la transacción.                                                                                                                                                                                                                |
| `Fee`             | String    | Amount              | Sí       | La tarifa pagada por la transacción.                                                                                                                                                                                                                        |
| `Sequence`        | Number    | UInt32              | Sí       | El número de secuencia de la transacción.                                                                                                                                                                                                                  |
| `SigningPubKey`   | String    | Blob                | Sí       | La clave pública que firma la transacción.                                                                                                                                                                                                               |

El objeto `EmittedTxn` también contiene todos los campos de la transacción original.

### Campos EmitDetails

Un objeto `EmitDetails` tiene los siguientes campos:

| Campo             | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                                                                                                                     |
| ----------------- | --------- | ------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `EmitGeneration`  | Number    | UInt32              | Sí       | Este campo rastrea una cadena de transacciones emitidas que, a su vez, causan que se emitan otras transacciones.                                                                                                                                                                  |
| `EmitBurden`      | String    | UInt64              | Sí       | Este campo es una heurística para detectar ataques de tipo forkbomb. Las tarifas se basan en la carga y aumentarán exponencialmente cuando se inicie una reacción en cadena para evitar que la red se sature con transacciones emitidas auto-reforzantes.                                                   |
| `EmitParentTxnID` | String    | Hash256             | Sí       | La Ejecución del Hook que emitió la transacción está conectada a la Transacción Originante. Por lo tanto, este campo siempre es necesario para un seguimiento eficiente del comportamiento.                                                                                                    |
| `EmitNonce`       | String    | Hash256             | Sí       | Las Transacciones Emitidas serían idénticas con los mismos campos y, por lo tanto, tendrían hashes de transacción idénticos si no se usara un nonce. Sin embargo, cada nodo en la red necesita acordar sobre el nonce, por lo que se pone a disposición una API especial de Hook para producir un nonce determinista. |
| `EmitCallback`    | String    | AccountID           | No        | Este campo es utilizado por xahld cuando necesita iniciar un callback, de manera que sabe en qué Hook y cuenta iniciar el callback. Los callbacks ocurren cuando una transacción emitida es aceptada en un ledger.                                                                 |
| `EmitHookHash`    | String    | Hash256             | Sí       | El SHA512H del Hook en el momento en que fue ejecutado.                                                                                                                                                                                                                            |
