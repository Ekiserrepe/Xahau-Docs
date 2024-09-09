---
description: >-
  Dado que las redes habilitadas para Hooks requieren campos de transacción específicos y ofrecen más tipos de transacciones, no todos los clientes funcionarán de forma inmediata. `xrpl-accountlib`
---

# Firmar transacciones

## Diferencias principales

1. Las redes habilitadas para Hooks permiten obtener definiciones de red dinámicamente. Permitiendo a los clientes adaptar los tipos de transacciones, objetos de ledger, propiedades y tipos de valores disponibles. Cuando se implementan correctamente, las librerías de firma y codificación no tienen que actualizarse cuando la red añade tipos/propiedades de transacción/objeto. <mark style=«color:blue;»>**Las librerías siguientes implementan esto y lo manejarán por ti.**</mark>
2. Las redes habilitadas para hooks requieren un **NetworkID** con cada transacción para evitar la repetición de transacciones en otra cadena. El **NetworkID** también será devuelto por un comando RPC `server_info` en el campo `network_id` (por ejemplo **`21338`** para Hooks V3 testnet).
3. Las transacciones en una red habilitada para Hooks pueden necesitar tarifas más altas para entregar una transacción a otra cuenta, en función de los Hooks que se ejecutarán enviando desde la cuenta de envío y recibiendo en la cuenta de destino. Una tarifa razonable para satisfacer la ejecución de Hooks puede obtenerse dinámicamente de un nodo emitiendo el comando `fee` mientras se proporciona una transacción como `tx_blob`. <mark style=«color:blue;»>**Las siguientes librerías implementan esto y lo manejarán por ti.**</mark>

## Javascript/Typescript

El [**paquete npm `xrpl-accountlib`**](https://www.npmjs.com/package/xrpl-accountlib) puede firmar transacciones para redes habilitadas para Hooks, ya que ofrece soporte completo de características de red dinámica obteniendo definiciones de red en tiempo de ejecución.

El [**paquete npm `xrpl-client`**](https://www.npmjs.com/package/xrpl-client) se integra perfectamente con `xrpl-accountlib` (y viene como dependencia) para obtener dinámicamente las definiciones de red y los valores de cuenta antes mencionados, ayudando a enviar la transacción.

### Ejemplo de código

```javascript
import {
  derive,
  utils,
  signAndSubmit,
} from "xrpl-accountlib"

const wss = 'wss://xahau-test.net'
const account = derive.familySeed("s...")

const networkInfo = await utils.txNetworkAndAccountValues(wss, account)

const tx = {
  TransactionType: "SetHook",
  Hooks: [ { Hook: {
    CreateCode: "0061736D01000000011C0460057F7F7F7F7F017E60037F7F7E017E60027F7F017F60017F017E02230303656E76057472616365000003656E7606616363657074000103656E76025F670002030201030503010002062B077F0141B088040B7F004180080B7F0041A6080B7F004180080B7F0041B088040B7F0041000B7F0041010B07080104686F6F6B00030AC4800001C0800001017F230041106B220124002001200036020C41920841134180084112410010001A410022002000420010011A41012200200010021A200141106A240042000B0B2C01004180080B254163636570742E633A2043616C6C65642E00224163636570742E633A2043616C6C65642E22",
    Flags: 1,
    HookApiVersion: 0,
    HookNamespace: "F".repeat(64),
    HookOn: "F".repeat(58) + "BFFFFE",
  }
  }],
  ...networkInfo.txValues,
  // ^^ This adds autmatically fetched values for you:
  // Sequence, Account, LastLedgerSequence,
  // Fee (Hooks enabled: autodetect (from ledger))
}

/**
 * Note: the code above and `signAndSubmit` results in automatically
 * fetching and setting a fee for you. If you want to check the fee
 * for min/max/..., get your own fee (string in drops) using:
 *    utils.networkTxFee(wss, tx)
 * 
 * e.g.
 *    const Fee = await utils.networkTxFee(wss, tx)
 *    assert(Number(Fee) < 50_000, "Auto fee above 50k drops, abort")
 *    Object.assign(tx, { Fee, })
 */

const submitted = await signAndSubmit(tx, wss, account)

console.log(submitted)
```
