# Hook

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L157-L170)

_(Añadido en la \[enmienda Hooks]\[].)_

Un objeto `Hook` describe un contrato inteligente que puede ser activado por una transacción para realizar operaciones predefinidas. Las operaciones son definidas por el creador del `Hook` y pueden interactuar con el libro mayor y las transacciones.

### Ejemplo JSON

```json
{
  "Account": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo",
  "OwnerNode": "0000000000000000",
  "PreviousTxnID": "5463C6E08862A1FAE5EDAC12D70ADB16546A1F674930521295BC082494B62924",
  "PreviousTxnLgrSeq": 6,
  "Hooks": [
    {
      "HookHash": "46060241FABCF692D4D934BA2A6C4427CD4279083E38C77CBE642243E43BE291",
      "HookParameters": []
    }
  ],
  "LedgerEntryType": "Hook",
  "index": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0"
}
```

### Campos

Un objeto `Hook` tiene los siguientes campos:

| Campo               | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                     |
| ------------------- | --------- | ------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`           | String    | Account             | Sí       | La cuenta que creó el Hook.                                                                                                                                              |
| `OwnerNode`         | String    | UInt64              | Sí       | Un indicio que señala qué página del directorio del propietario enlaza a este objeto, en caso de que el directorio tenga varias páginas.                                                   |
| `PreviousTxnID`     | String    | Hash256             | Sí       | El ID de la transacción que más recientemente modificó este objeto.                                                                                                              |
| `PreviousTxnLgrSeq` | Number    | UInt32              | Sí       | El \[índice del ledger]\[] que contiene la transacción que más recientemente modificó este objeto.                                                                     |
| `Hooks`             | Array     | Array               | Sí       | Un array de objetos hook. Cada objeto tiene los siguientes campos: `HookHash`, `CreateCode`, `HookGrants`, `HookNamespace`, `HookParameters`, `HookOn`, `HookApiVersion`, `Flags`. |
| `LedgerEntryType`   | String    | UInt16              | Sí       | El valor `0x0043`, asignado al string `Hook`, indica que este objeto es un objeto Hook.                                                                                   |

### Campos Hook

Los siguientes campos son utilizados en el objeto hook:

| Campo            | Tipo JSON | Tipo Interno | Descripción                    |
| ---------------- | --------- | ------------- | ------------------------------ |
| `HookHash`       | String    | Hash256       | El hash del hook.          |
| `HookParameters` | Array     | Array         | Los parametros del hook.    |
| `Flags`          | Number    | UInt32        | Flags adicionales para el hook. |

#### Formato de ID del Hook

El ID de un objeto `Hook` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio Hook (`0x0048`)
* El AccountID del remitente de la \[transacción SetHook]\[] que creó el objeto `Hook`.