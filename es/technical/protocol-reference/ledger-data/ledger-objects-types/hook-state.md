# Estado del Hook

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L157-L170)

_(Añadido por la \[enmienda Hooks]\[].)_

Un objeto `HookState` describe el estado de un hook, que es un fragmento de código que se ejecuta en Xahau y que puede interactuar con las transacciones. El objeto `HookState` almacena el estado del hook, el cual puede ser modificado por el código del hook.

### Ejemplo JSON

```json
{
  "OwnerNode": "0000000000000000",
  "HookStateKey": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0",
  "HookStateData": "46060241FABCF692D4D934BA2A6C4427CD4279083E38C77CBE642243E43BE291",
  "LedgerEntryType": "HookState",
  "index": "5463C6E08862A1FAE5EDAC12D70ADB16546A1F674930521295BC082494B62924"
}
```

### Campos

Un objeto `HookState` tiene los siguientes campos:

| Campo             | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                   |
| ----------------- | --------- | ------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `OwnerNode`       | String    | UInt64              | Sí       | Un indicio que señala qué página del directorio del propietario enlaza a este objeto, en caso de que el directorio tenga varias páginas. |
| `HookStateKey`    | String    | Hash256             | Sí       | La clave que identifica de manera única este estado del hook.                                                                             |
| `HookStateData`   | String    | VL                  | Sí       | Los datos almacenados por el hook. Esto puede ser cualquier dato que el código del hook decida almacenar.                                      |
| `LedgerEntryType` | String    | UInt16              | Sí       | El valor `0x0043`, asignado a la cadena HookState, indica que este objeto es un objeto `HookState`.                       |

#### Formato ID de HookState

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/Indexes.cpp#L193-L200)

El ID de un objeto `HookState` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio HookState (`0x0076`).
* El AccountID de la cuenta que posee el hook.
* La `HookStateKey` del objeto `HookState`.
* El `HookNamespace` del hook.