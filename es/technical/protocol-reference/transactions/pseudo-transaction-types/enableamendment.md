# EnableAmendment

Una pseudo-transacción `EnableAmendment` marca un cambio en el estado de una enmienda propuesta cuando:

* Obtiene la aprobación de supermayoría de los validadores.
* Pierde la aprobación de supermayoría.
* Se habilita en el protocolo Xahau.

### Ejemplo JSON

```json
{
  "Account": "rrrrrrrrrrrrrrrrrrrrrhoLvTp",
  "Amendment": "42426C4D4F1009EE67080A9B7965B44656D7714D104A72F9B4369F97ABF044EE",
  "Fee": "0",
  "LedgerSequence": 21225473,
  "Sequence": 0,
  "SigningPubKey": "",
  "TransactionType": "EnableAmendment"
}  
```

| Campo            | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                        |
| ---------------- | --------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Amendment`      | String    | Hash256             | Un identificador único para la enmienda. No está destinado a ser un nombre legible por humanos. Consulta Amendments para una lista de enmiendas conocidas.            |
| `LedgerSequence` | Number    | UInt32              | El \[índice del ledger]\[] donde aparece esta pseudo-transacción. Esto distingue la pseudo-transacción de otras ocurrencias del mismo cambio. |

### Flags de EnableAmendment

El valor `Flags` de la pseudo-transacción EnableAmendment indica el estado de la enmienda en el momento en que se incluye la pseudo-transacción en el ledger.

Un valor de `Flags` de `0` (sin flags) o un campo `Flags` omitido indica que la enmienda ha sido habilitada y se aplica a todos los ledgers posteriores. Otros valores de `Flags` son los siguientes:

| Nombre del Flag        | Valor Hex    | Valor Decimal | Descripción                                                                                                    |
| ---------------- | ------------ | ------------- | -------------------------------------------------------------------------------------------------------------- |
| `tfGotMajority`  | `0x00010000` | 65536         | El apoyo a esta enmienda aumentó al menos al 80% de los validadores de confianza a partir de esta versión del ledger.  |
| `tfLostMajority` | `0x00020000` | 131072        | El apoyo a esta enmienda disminuyó a menos del 80% de los validadores de confianza a partir de esta versión del ledger. |
