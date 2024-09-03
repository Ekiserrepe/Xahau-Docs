# Secuencia de importación VL

_(Añadido por la \[enmienda Import]\[].)_

El objeto `ImportVLSequence` ayuda a rastrear y validar el orden de las operaciones durante el proceso de importación. Se utiliza para garantizar que ocurra la secuencia correcta de eventos y para manejar cualquier discrepancia o actualización en la dUNL (lista de nodos únicos de validación) en el ledger de "quema".

### Ejemplo JSON

```json
{
  "LedgerEntryType": "ImportVLSequence",
  "Flags": 0,
  "ImportSequence": 2023102101,
  "PublicKey": "n9LigbVAi4pQc6pU2KJvQZV5wqJ8C3sVvZvBZUopchH8vqa6PEKy",
  "index": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0"
}
```

### Campos

Un objeto `ImportVLSequence` tiene los siguientes campos:

| Campo             | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                           |
| ----------------- | --------- | ------------------- | --------- | --------------------------------------------------------------------------------------------------------------------- |
| `LedgerEntryType` | String    | UInt16              | Sí       | El valor `0x0049`, asignado a la cadena `ImportVLSequence`, indica que este objeto es un objeto ImportVLSequence. |
| `Flags`           | Number    | UInt32              | Sí       | Un mapa de bits de flags booleanos. No se definen flags para el tipo de objeto `ImportVLSequence`, por lo que este valor siempre es `0`. |
| `ImportSequence`  | Number    | UInt32              | Sí       | El número de secuencia actual de la lista dUNL en el ledger de "quema".                                                |
| `PublicKey`       | String    | Blob                | Sí       | La `PublicKey` de la lista dUNL del ledger de "quema".                                                           |

#### Formato de ID Secuencia de Importación VL

El ID de un objeto `ImportVLSequence` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio de Import VL Sequence (`0x0049`)
La Clave Pública de la Lista de Validadores de Importación.
