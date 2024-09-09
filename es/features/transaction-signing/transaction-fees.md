---
description: >-
  Los contratos inteligentes Xahau (Hooks) necesitan tarifas específicas de transacción y destino.
  Puede obtener fácilmente la tarifa requerida del comando RPC `fee`.
---

# Tarifas de transacción

Mientras que las librerías pueden encargarse de la fijación de las tarifas por ti (ver [.](./ "mention")), cuando construyas tus propias integraciones con el ledger de Xahau, puede que tengas que implementar la fijación dinámica de las tarifas basándote en la transacción, la cuenta de origen y la de destino.

Como el remitente de una transacción tendrá que pagar las tarifas requeridas para los Hooks invocados para el tipo de transacción específica, donde los Hooks pueden vivir tanto en la cuenta de origen como en la de destino, puedes enviar un TX Blob (firmado con una cuenta dummy) al comando `fee`, tras lo cual Xahau devolverá las tarifas específicas requeridas para la transacción específica.

### Ayudante RPC para las tarifas

Las tarifas de transacción en un ledger con la Enmienda de Hooks activada no son triviales de calcular para los usuarios finales y/o las aplicaciones de wallet. Esto se debe a que los hooks fuertes deben ser pagados por el originador de una transacción, y puede haber hasta 4 hooks fuertes en la cuenta emisora y 4 en la cuenta receptora, así como cualquier otro actor transaccional fuerte involucrado (como puede ser el caso de algunos tipos de transacciones exóticas). Además, si la transacción es un SetHook, el tamaño de los parámetros, el tamaño del código y si es una operación _create_ o una operación _install_ determinan el tamaño de la tarifa.

Por lo tanto, se recomienda encarecidamente que **todas** las transacciones se ejecuten a través de la llamada RPC de tasa actualizada antes de que se envíen al ledger.

#### Para invocar la llamada RPC:

1. Abrir una conexión websocket al nodo Hooks con el que vas a trabajar.
2. Componer la transacción serializada de la que se desea conocer la tarifa con lo siguiente:

* `Fee: 0`
* `SigningPubKey: ""` (Eso es: 0 byte VL del tipo 0x73. En hex:`0x7300`.)
* **No** firmes la transacción.

3. Enviar como un blob hexadecimal al RPC como sigue:

```json
{"command":"fee", "tx_blob":"<hex blob>"}
```

Para HTTP POST RPC enviar como sigue:

```json
{"method":"fee", "params": [{"tx_blob":"<hex blob>"}] }
```

La respuesta debería ser algo parecido a

```json
{
  result: {
    drops: {
      base_fee: '130520',
    },
  //...
  },
  type: 'response'
}
```

Tomar la tarifa base y establecer como campo `Fee`en la transacción. Ahora firmar y enviar siguiendo el proceso den envío de una transacción normal.

Si hay un valor no válido para `tx_blob` o falta `tx_blob`, se devolverá un resultado JSON normal con una `base_fee` de 10.

{% hint style="success" %}
Fuente: [https://xrpl-hooks.readme.io/docs/hook-fees](https://xrpl-hooks.readme.io/docs/hook-fees)
{% endhint %}
