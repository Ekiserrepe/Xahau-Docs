# URIToken

Los URITokens son la implementación del Token No Fungible (NFT) nativa de la red Xahau. Existen como objetos on-ledger de primera clase, identificados unívocamente por el hash de su emisor y su Uniform Resource Identifier, Identificador Uniforme de Recursos (URI). Los URITokens pueden apuntar a cualquier contenido digital, existiendo sólo un objeto por URI por cuenta en el libro mayor.

El emisor tiene la capacidad de establecer un flag para permitir la quema del objeto en el futuro. La reserva de cada propietario también se bloquea al poseer el URIToken.

### Tipos de transacciones

#### URITokenMint

La transacción URITokenMint acuña un nuevo URIToken y asigna la propiedad a la cuenta especificada. El URIToken acuñado representa un activo digital único que puede utilizarse en diversas aplicaciones. El emisor puede optar por permitir que el URIToken acuñado se destruya en el futuro.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/uritokenmint.md" %}
[uritokenmint.md](../../technical/protocol-reference/transactions/transaction-types/uritokenmint.md)
{% endcontent-ref %}

#### URITokenBurn

La transacción URITokenBurn se utiliza para quemar un URIToken en Xahau. Quemar un URIToken lo retira permanentemente de la circulación. La transacción no tiene ningún requisito especial de coste de transacción. La cuenta que posee el URIToken que se va a quemar es necesaria para esta transacción.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/uritokenburn.md" %}
[uritokenburn.md](../../technical/protocol-reference/transactions/transaction-types/uritokenburn.md)
{% endcontent-ref %}

#### URITokenBuy

La transacción URITokenBuy permite a un usuario comprar un URIToken al emisor. Esta transacción se utiliza para transferir la propiedad de un URIToken del emisor al comprador. Para esta transacción se requiere la cuenta del comprador, el identificador único del URIToken que se va a comprar y la cantidad de moneda que se va a pagar por el URIToken.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/uritokenbuy.md" %}
[uritokenbuy.md](../../technical/protocol-reference/transactions/transaction-types/uritokenbuy.md)
{% endcontent-ref %}

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/uritokencreateselloffer.md" %}
[uritokencreateselloffer.md](../../technical/protocol-reference/transactions/transaction-types/uritokencreateselloffer.md)
{% endcontent-ref %}

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/uritokencancelselloffer.md" %}
[uritokencancelselloffer.md](../../technical/protocol-reference/transactions/transaction-types/uritokencancelselloffer.md)
{% endcontent-ref %}
