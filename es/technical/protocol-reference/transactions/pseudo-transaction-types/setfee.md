# SetFee

Una pseudo-transacción `SetFee` marca un cambio en el costo de transacción o en los requisitos de reserva como resultado de la Votación de Tarifas.

**Nota:** No puedes enviar una pseudo-transacción, pero puedes encontrar una al procesar ledgers.

### Ejemplo JSON

```json
{
    "Account": "rrrrrrrrrrrrrrrrrrrrrhoLvTp",
    "BaseFee": "000000000000000A",
    "Fee": "0",
    "ReferenceFeeUnits": 10,
    "ReserveBase": 20000000,
    "ReserveIncrement": 5000000,
    "Sequence": 0,
    "SigningPubKey": "",
    "TransactionType": "SetFee",
    "date": 439578860,
    "hash": "1C15FEA3E1D50F96B6598607FC773FF1F6E0125F30160144BE0C5CBC52F5151B",
    "ledger_index": 3721729,
}
```

| Campo               | Tipo JSON        | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                              |
| ------------------- | ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `BaseFee`           | String           | UInt64              | El cargo, en drops de XAH, para la transacción de referencia, como hexadecimal. (Este es el costo de la transacción antes de escalar por carga).                                                                                             |
| `ReferenceFeeUnits` | Unsigned Integer | UInt32              | El costo, en unidades de tarifa, de la transacción de referencia.                                                                                                                                                                     |
| `ReserveBase`       | Unsigned Integer | UInt32              | La reserva base, en drops                                                                                                                                                                                               |
| `ReserveIncrement`  | Unsigned Integer | UInt32              | El incremento de la reserva, en drops                                                                                                                                                                                        |
| `LedgerSequence`    | Number           | UInt32              | _(Omitido para algunas pseudo-transacciones históricas `SetFee`)_ El índice de la versión del ledger donde aparece esta pseudo-transacción. Esto distingue la pseudo-transacción de otras ocurrencias del mismo cambio. |

Si la _\[enmienda XAHFees está habilitada]\[]_, las pseudo-transacciones `SetFee` usan estos campos en su lugar:

| Campo                   | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                              |
| ----------------------- | --------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `BaseFeeDrops`          | String    | Amount              | El cargo, en drops de XAH, para la transacción de referencia. (Este es el coste de la transacción antes de escalar por carga).                                                                                                      |
| `ReserveBaseDrops`      | String    | Amount              | La reserva base, en drops                                                                                                                                                                                               |
| `ReserveIncrementDrops` | String    | Amount              | El incremento de la reserva, en drops                                                                                                                                                                                        |
| `LedgerSequence`        | Number    | UInt32              | _(Omitido para algunas pseudo-transacciones históricas `SetFee`)_ El índice de la versión del ledger donde aparece esta pseudo-transacción. Esto distingue la pseudo-transacción de otras ocurrencias del mismo cambio. |
