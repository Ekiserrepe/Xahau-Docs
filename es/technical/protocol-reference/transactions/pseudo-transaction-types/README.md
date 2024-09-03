# Tipos de Pseudo Transacciones

Las pseudo-transacciones nunca son enviadas por los usuarios ni propagadas a través de la red. En su lugar, un servidor puede elegir inyectar pseudo-transacciones en un ledger propuesto directamente según reglas de protocolo específicas. Si suficientes servidores proponen la misma pseudo-transacción, el proceso de consenso la aprueba, y la pseudo-transacción se incluye en los datos de transacción de ese ledger.

### Valores Especiales para Campos Comunes

Algunos de los \[campos comunes]\[] requeridos para las transacciones normales no tienen sentido para las pseudo-transacciones. Las pseudo-transacciones usan los siguientes valores especiales para estos campos comunes:

| Campo           | Tipo JSON | \[Tipo Interno]\[] | Valor               |
| --------------- | --------- | ------------------- | ------------------- |
| `Account`       | String    | AccountID           | ACCOUNT\_ZERO       |
| `Fee`           | String    | Amount              | `0`                 |
| `Sequence`      | Number    | UInt32              | `0`                 |
| `SigningPubKey` | String    | Blob                | `""` (Empty string) |
| `TxnSignature`  | String    | Blob                | `""` (Empty string) |

Las pseudo-transacciones usan los siguientes campos comunes de manera normal:

* `TransactionType`
* `Flags`

| Campo             | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                               |
| ----------------- | --------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `TransactionType` | String    | UInt16              | _(Requerido)_ El tipo de transacción.                                                                                     |
| `Flags`           | Number    | UInt32              | _(Opcional)_ Un conjunto de flags de bits para esta transacción. El significado de flags específicos varía según el tipo de transacción. |
