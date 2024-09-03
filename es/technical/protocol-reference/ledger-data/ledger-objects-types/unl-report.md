# Informe UNL

_(Añadido por la \[enmienda Hooks]\[].)_

Un objeto `UNLReport` describe un informe de la Lista de Nodos Únicos (UNL, por sus siglas en inglés), que es una lista de nodos validadores en los que la red confía para validar transacciones.

### Ejemplo JSON

```json
{
  "LedgerEntryType": "UNLReport",
  "PreviousTxnID": "5463C6E08862A1FAE5EDAC12D70ADB16546A1F674930521295BC082494B62924",
  "PreviousTxnLgrSeq": 6,
  "ImportVLKeys": [
    {
      "PublicKey": "n9LigbVAi4pQc6pU2KJvQZV5wqJ8C3sVvZvBZUopchH8vqa6PEKy",
      "Account": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo"
    }
  ],
  "ActiveValidators": [
    {
      "PublicKey": "n9LigbVAi4pQc6pU2KJvQZV5wqJ8C3sVvZvBZUopchH8vqa6PEKy",
      "Account": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo"
    }
  ],
  "index": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0"
}
```

### Campos

Un objeto `UNLReport` tiene los siguientes campos:

| Campo               | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                         |
| ------------------- | --------- | ------------------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `LedgerEntryType`   | String    | UInt16              | Sí       | El valor `0x0073`, mapeado a la cadena `UNLReport`, indica que este objeto es un objeto UNLReport.                                             |
| `PreviousTxnID`     | String    | Hash256             | Sí       | El hash identificador de la transacción que más recientemente modificó este objeto.                                                                    |
| `PreviousTxnLgrSeq` | Number    | UInt32              | Sí       | El índice del ledger que contiene la transacción que más recientemente modificó este objeto.                                                      |
| `ImportVLKeys`      | Array     | Array               | No        | Un array de objetos, cada uno representando una clave de validador que ha sido importada. Cada objeto tiene un campo `PublicKey` y un campo opcional `Account`. |
| `ActiveValidators`  | Array     | Array               | No        | Un array de objetos, cada uno representando un validador activo. Cada objeto tiene un campo `PublicKey` y un campo opcional `Account`.                    |

### Campos ImportVLKey

| Campo       | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                     |
| ----------- | --------- | ------------------- | --------- | ------------------------------------------------------------------------------- |
| `PublicKey` | String    | VL                  | Sí       | La clave pública del validador importado.                                       |
| `Account`   | String    | Account             | No        | 	La cuenta asociada con la clave del validador importado. Este campo es opcional. |

### ActiveValidator Fields

| Campo       | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                               |
| ----------- | --------- | ------------------- | --------- | ------------------------------------------------------------------------- |
| `PublicKey` | String    | VL                  | Sí       | La clave pública del validador activo.                                   |
| `Account`   | String    | Account             | No        | La cuenta asociada con el validador activo. Este campo es opcional. |

### Formato de ID de UNLRepor

El ID del objeto `UNLReport` es el hash de la clave de espacio `UNLReport` (`0x0052`) únicamente. Esto significa que el ID del objeto `UNLReport` en un ledger es siempre:

```
61E32E7A24A238F1C619D5F9DDCC41A94B33B66C0163F7EFCC8A19C9FD6F28DC
```
