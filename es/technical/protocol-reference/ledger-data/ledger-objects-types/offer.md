# Offer

[\[Fuernte\]](https://github.com/ripple/rippled/blob/5d2d88209f1732a0f8d592012094e345cbe3e675/src/ripple/protocol/impl/LedgerFormats.cpp#L57)

El registro de `Offer` (Oferta) describe una oferta para intercambiar divisas en el exchange descentralizado de Xahau. (En finanzas, esto se conoce más tradicionalmente como una _orden_). Una \[transacción OfferCreate]\[] solo crea una entrada de `Offer` en el ledger cuando la oferta no se puede ejecutar completamente de inmediato al consumir otras Òffers ya existentes en el ledger.

Una oferta puede quedarse sin fondos debido a otras actividades en la red mientras permanece en el ledger. Al procesar transacciones, la red automáticamente elimina cualquier oferta sin fondos que estas transacciones encuentren. (De lo contrario, las ofertas sin fondos permanecen porque solo las transacciones pueden cambiar el estado del ledger).

### Ejemplo JSON

```json
{
    "Account": "rBqb89MRQJnMPq8wTwEbtz4kvxrEDfcYvt",
    "BookDirectory": "ACC27DE91DBA86FC509069EAF4BC511D73128B780F2E54BF5E07A369E2446000",
    "BookNode": "0000000000000000",
    "Flags": 131072,
    "LedgerEntryType": "Offer",
    "OwnerNode": "0000000000000000",
    "PreviousTxnID": "F0AB71E777B2DA54B86231E19B82554EF1F8211F92ECA473121C655BFC5329BF",
    "PreviousTxnLgrSeq": 14524914,
    "Sequence": 866,
    "TakerGets": {
        "currency": "XAG",
        "issuer": "r9Dr5xwkeLegBeXq6ujinjSBLQzQ1zQGjH",
        "value": "37"
    },
    "TakerPays": "79550000000",
    "index": "96F76F27D8A327FC48753167EC04A46AA0E382E6F57F32FD12274144D00F1797"
}
```

### Campos

Un objeto `Offer` tiene los siguientes campos:

| Nombre                | Tipo JSON        | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                                                                                                              |
| ------------------- | ---------------- | ------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Account`           | String           | AccountID           | Sí       | La dirección de la cuenta que posee esta oferta.                                                                                                                                                                                                                         |
| `BookDirectory`     | String           | Hash256             | Sí       | El ID del Directorio de Ofertas que enlaza con esta oferta.                                                                                                                                                                                                                  |
| `BookNode`          | String           | UInt64              | Sí       | Una pista que indica qué página del directorio de ofertas enlaza con este objeto, en caso de que el directorio consista en varias páginas.                                                                                                                                              |
| `Expiration`        | Number           | UInt32              | No        | Indica el tiempo después del cual esta oferta se considera sin fondos. Ver \[Especificación de Tiempo]\[] para más detalles.                                                                                                                                                                 |
| `Flags`             | Number           | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para esta oferta.                                                                                                                                                                                                                       |
| `LedgerEntryType`   | String           | UInt16              | Sí       | El valor `0x006F`, mapeado a la cadena `Offer`, indica que este objeto describe una oferta.                                                                                                                                                                         |
| `OwnerNode`         | String           | UInt64              | Sí       | Una pista que indica qué página del directorio de propietarios enlaza con este objeto, en caso de que el directorio consista en varias páginas. **Nota:** La oferta no contiene un enlace directo al directorio del propietario que la contiene, ya que ese valor se puede derivar de la `Account`. |
| `PreviousTxnID`     | String           | Hash256             | Sí       | El hash identificador de la transacción que más recientemente modificó este objeto.                                                                                                                                                                                         |
| `PreviousTxnLgrSeq` | Number           | UInt32              | Sí       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que más recientemente modificó este objeto.                                                                                                                                                         |
| `Sequence`          | Number           | UInt32              | Sí       | El valor `Sequence` de la transacción \[OfferCreate]\[] que creó este objeto `Offer`. Se utiliza en combinación con la `Account` para identificar esta oferta.                                                                                                               |
| `TakerPays`         | String or Object | Amount              | Sí       | La cantidad restante y el tipo de divisa solicitada por el creador de la oferta.                                                                                                                                                                                                |
| `TakerGets`         | String or Object | Amount              | Sí       | La cantidad restante y el tipo de moneda proporcionada por el creador de la oferta.                                                                                                                                                                                           |

### Flags de la Oferta

Hay varias opciones que pueden estar habilitadas o deshabilitadas cuando una \[transacción OfferCreate]\[] crea un objeto de oferta. En el ledger, los flags se representan como valores binarios que se pueden combinar con operaciones bitwise-or. Los valores de bits para los flags en el ledger son diferentes de los valores utilizados para habilitar o deshabilitar esos flags en una transacción. Los flags del ledger tienen nombres que comienzan con **`lsf`**.

Los objetos `Offer` pueden tener los siguientes valores:

<table data-header-hidden><thead><tr><th width="135"></th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><td>Nombre del flag</td><td>Valor Hex</td><td>Valor decimal</td><td>Flag OfferCreate Correspondiente</td><td>Descripción</td></tr><tr><td><code>lsfPassive</code></td><td><code>0x00010000</code></td><td>65536</td><td><code>tfPassive</code></td><td>El objeto fue colocado como una oferta pasiva. Esto no tiene efecto en el objeto en el ledger.</td></tr><tr><td><code>lsfSell</code></td><td><code>0x00020000</code></td><td>131072</td><td><code>tfSell</code></td><td>El objeto fue colocado como una oferta de venta. Esto no tiene efecto en el objeto en el ledger (porque <code>tfSell</code> solo importa si obtienes una mejor tasa de la que pediste, lo cual no puede suceder después de que el objeto entre en el ledger)</td></tr></tbody></table>

### Formato de ID de Offer

El ID de un objeto `Offer` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio de la oferta (`0x006F`)
* El AccountID de la cuenta que coloca la oferta
*   El número de secuencia de la \[transacción OfferCreate]\[] que creó la oferta.
    Si la transacción OfferCreate usó un Ticket, utiliza el valor `TicketSequence` en su lugar.