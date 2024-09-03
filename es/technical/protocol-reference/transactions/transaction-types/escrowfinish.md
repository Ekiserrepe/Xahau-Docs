---
description: Entrega XAH o IOU de un pago en depósito al destinatario.
---

# EscrowFinish

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_Añadido por la \[enmienda Escrow]\[]._

### Finalizar con OfferSequence

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "EscrowFinish",
    "Owner": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "OfferSequence": 7,
    "Condition": "A0258020E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855810100",
    "Fulfillment": "A0028000"
}
```

### Finalizar con EscrowID

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "EscrowFinish",
    "Owner": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "EscrowID": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0",
    "Condition": "A0258020E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855810100",
    "Fulfillment": "A0028000"
}
```

### Campos

| Campo           | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                         |
| --------------- | --------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Owner`         | String    | AccountID           | Dirección de la cuenta fuente que financió el pago en depósito.                                                                                                                                         |
| `OfferSequence` | Number    | UInt32              | _(Opcional)_ Secuencia de la transacción de \[EscrowCreate]\[] que creó el pago en depósito a finalizar.                                                                                        |
| `Condition`     | String    | Blob                | _(Opcional)_ Valor hexadecimal que coincide con la \[condición criptográfica PREIMAGE-SHA-256]\[](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02#section-8.1) previamente suministrada del pago en depósito.         |
| `Fulfillment`   | String    | Blob                | _(Opcional)_ Valor hexadecimal del [cumplimiento de la condición criptográfica PREIMAGE-SHA-256](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02#section-8.1.4) que coincide con la `Condition` del pago en depósito. |
| `EscrowID`      | String    | Hash256             | _(Opcional)_ El ID del objeto de ledger Escrow a finalizar, como una cadena hexadecimal de 64 caracteres.                                                                                                    |

Cualquier cuenta puede enviar una transacción EscrowFinish.

* Si el pago en depósito tiene un tiempo `FinishAfter`, no se puede ejecutar antes de este tiempo. Específicamente, si la \[transacción EscrowCreate]\[] correspondiente especificó un tiempo `FinishAfter` que es posterior al tiempo de cierre del ledger más recientemente cerrado, la transacción EscrowFinish falla.
* Si el pago en depósito tiene una `Condition`, no se puede ejecutar a menos que se proporcione un `Fulfillment` coincidente para la condición.
* No se puede ejecutar un pago en depósito después de que haya expirado. Específicamente, si la \[transacción EscrowCreate]\[] correspondiente especificó un tiempo `CancelAfter` que es anterior al tiempo de cierre del ledger más recientemente cerrado, la transacción EscrowFinish falla.

**Nota:** El costo mínimo de transacción para enviar una transacción EscrowFinish aumenta si contiene un cumplimiento. Si la transacción no tiene cumplimiento, el costo de la transacción es el estándar de 10 drops. Si la transacción contiene un cumplimiento, el costo de la transacción es de 330 [drops de XAH][] más otros 10 drops por cada 16 bytes del tamaño del preimage.

En redes de no-producción, es posible que se pueda eliminar la cuenta de destino de un depósito en espera. En este caso, un intento de finalizar el depósito falla con el resultado `tecNO_TARGET`, pero el objeto de depósito permanece a menos que haya expirado normalmente. Si otro pago vuelve a crear la cuenta de destino, el depósito se puede finalizar con éxito. La cuenta de destino de un depósito solo se puede eliminar si el depósito se creó antes de que la enmienda fix1523 se habilitara. No existen tales depósitos en el Xahau de producción, por lo que este caso límite no es posible en el Xahau de producción. Este caso límite tampoco es posible en redes de prueba que habiliten las enmiendas fix1523 y Escrow al mismo tiempo, lo cual es el valor predeterminado cuando se inicia un nuevo ledger genesis.
