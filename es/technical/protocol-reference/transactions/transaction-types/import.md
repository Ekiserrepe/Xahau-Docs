---
description: >-
  Import es una nueva transacción que acepta un XPOP de la cadena XRPL Mainnet (network_id=0) o Testnet (network_id=1) y proporciona sincronización de cuentas.
---

# Import

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda Import]\[].)_

### Ejemplo

```json
{
  "TransactionType": "Import",
  "Sequence": 0,
  "Fee": "0",
  "Account": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo",
  "Blob" : "DEADBEEF"
}
```

### Campos

| Campo         | Tipo JSON | \[Tipo Interno]\[] | Descripción                                          |
| ------------- | --------- | ------------------- | ---------------------------------------------------- |
| `Blob`        | String    | Blob                | Valor hexadecimal que representa un XPOP                       |
| `Issuer`      | String    | AccountID           | (Opcional) Dirección que puede ser utilizada dentro del Hook. |
| `Destination` | String    | AccountID           | (Opcional) Dirección que puede ser utilizada dentro del Hook.
 |

### Casos de error

* Si la cuenta no está activada, entonces `Sequence` debe ser 0 y la `Fee` también debe ser 0.
* Si la cuenta está activada, entonces `Sequence` y la `Fee` se calculan de la manera estándar.
* Si el campo `Issuer` está presente, entonces `Fee` debe calcularse utilizando el método estándar.

### Notas

_Se recomienda que si utiliza una `SignersList` o `RegularKey` para firmar sus transacciones, que asegure sus cuentas **PRIMERO** antes de intentar B2M XAH_._

* Si la transacción interna (xpop) es `AccountSet`, los flags existentes en la mainet serán transferidos a la nueva red.
* Si la transacción interna (xpop) es `SetRegularKey` con el campo `RegularKey` omitido o vacío, y no existe una lista de firmantes para la cuenta, entonces la bandera `lsfDisableMaster` se establecerá en la cuenta.
* Si la transacción interna (xpop) es `SetRegularKey`, entonces la bandera `lsfPasswordSpent` se establecerá en la cuenta.
* `TicketSequence` no está disponible en `Import`.

### Importación para el Emisor

Para los emisores, hay pasos adicionales a seguir antes de que sus titulares de activos puedan importar transacciones.

Primero, los emisores necesitan instalar un hook. Hay dos opciones para esto: `B2MNFToken` o `B2MPayment`.

#### B2MNFToken

Una transacción `NFTokenBurn` en mainnet resultará en una transacción `URITokenMint` en la red.

> c hook: https://example.com

#### B2MPayment

Una transacción `Payment` en mainnet al Emisor resultará en una transacción `Payment` desde el Emisor en la red.

> c hook: https://example.com

_Ten en cuenta que el proceso de importación para el emisor implica tipos de transacciones específicas y requiere una configuración cuidadosa. Asegúrate siempre de que los hooks estén configurados correctamente y de que las transacciones sean válidas para las operaciones previstas._
