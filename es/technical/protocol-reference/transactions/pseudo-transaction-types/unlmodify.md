# UNLModify

_(Añadido por la \[enmienda NegativeUNL]\[].)_

Una pseudo-transacción `UNLModify` marca un cambio en la Lista UNL Negativa, indicando que un validador confiable se ha desconectado o ha vuelto a estar en línea.

**Nota:** No puedes enviar una pseudo-transacción, pero puedes encontrar una al procesar ledgers.

### Ejemplo JSON

```json
{
  "Account": "",
  "Fee": "0",
  "LedgerSequence": 1600000,
  "Sequence": 0,
  "SigningPubKey": "",
  "TransactionType": "UNLModify",
  "UNLModifyDisabling": 1,
  "UNLModifyValidator": "ED6629D456285AE3613B285F65BBFF168D695BA3921F309949AFCD2CA7AFEC16FE",
}
```

| Nombre                 | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                      |
| -------------------- | --------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `TransactionType`    | String    | UInt16              | El valor `0x0066`, mapeado al string `UNLModify`, indica que este objeto es una pseudo-transacción `UNLModify`.                                                           |
| `LedgerSequence`     | Number    | UInt32              | El \[índice del ledger]\[] donde aparece esta pseudo-transacción. Esto distingue la pseudo-transacción de otras ocurrencias del mismo cambio.                               |
| `UNLModifyDisabling` | Number    | UInt8               | Si es `1`, este cambio representa la adición de un validador a la Lista UNL Negativa. Si es `0`, este cambio representa la eliminación de un validador de la Lista UNL Negativa. (No se permiten otros valores). |
| `UNLModifyValidator` | String    | Blob                | El validador a añadir o eliminar, identificado por su clave pública maestra.                                                                                                          |
