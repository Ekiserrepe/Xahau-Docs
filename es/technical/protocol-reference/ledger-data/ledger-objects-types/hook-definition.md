# Definición de Hook

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L157-L170)

_(Añadido en la \[enmienda Hooks]\[].)_

Un objeto `HookDefinition` describe un hook, que es un fragmento de código que se ejecuta en respuesta a ciertas transacciones. El hook puede modificar la transacción, emitir nuevas transacciones o realizar otras acciones.

### Ejemplo JSON

```json
{
  "HookHash": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0",
  "HookOn": "0000000000000000000000000000000000000000000000000000000000000000",
  "HookNamespace": "0000000000000000000000000000000000000000000000000000000000000000",
  "HookParameters": {
    "HookParameter": {
      "HookParameterName": "DEADBEEF",
      "HookParameterValue": "DEADBEEF",
    }
  },
  "HookApiVersion": 1,
  "CreateCode": "5463C6E08862A1FAE5EDAC12D70ADB16546A1F674930521295BC082494B62924",
  "HookSetTxnID": "0000000000000000",
  "ReferenceCount": "6",
  "Fee": "100000000",
  "HookCallbackFee": "200000000",
  "LedgerEntryType": "HookDefinition",
  "index": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0"
}
```

### Campos

Un objeto `HookDefinition` tiene los siguientes campos:

| Campo             | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                       |
| ----------------- | --------- | ------------------- | --------- | ----------------------------------------------------------------------------------------------------------------- |
| `HookHash`        | String    | Hash256             | Sí       | El identificador único del hook.                                                                                |
| `HookOn`          | String    | Hash256             | Sí       | La cuenta a la que está adjunto el hook.                                                                         |
| `HookNamespace`   | String    | Hash256             | Sí       | El espacio de nombres del hook.                                                                                        |
| `HookParameters`  | String    | Vector              | Sí       | Los parámetros que acepta el hook.                                                                             |
| `HookApiVersion`  | Number    | UInt16              | Sí       | La versión de la API del hook que utiliza el hook.                                                                   |
| `CreateCode`      | String    | VL                  | Sí       | El código que se ejecuta cuando se crea el hook.                                                               |
| `HookSetTxnID`    | String    | Hash256             | Sí       | El ID de la transacción que configuró el hook.                                                                      |
| `ReferenceCount`  | String    | UInt64              | Sí       | El número de referencias al hook.                                                                             |
| `Fee`             | String    | Amount              | Sí       | La tarifa por ejecutar el hook.                                                                                   |
| `HookCallbackFee` | String    | Amount              | No        | La tarifa por ejecutar la función de callback del hook.                                                               |
| `LedgerEntryType` | String    | UInt16              | Sí       | El valor `0x0043`, asignado a la cadena `HookDefinition`, indica que este objeto es un objeto HookDefinition. |

#### Formato de ID de Definición de Hook

El ID de un objeto `HookDefinition` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio HookDefinition (`0x0044`)
* El `HookHash` del hook
