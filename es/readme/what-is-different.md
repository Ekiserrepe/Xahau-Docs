---
description: Una lista de diferencias notables entre XRP Ledger y Xahau.
---

# ¿Qué es Diferente?

{% hint style="warning" %}
## No documentado

* Las cuentas en Xahau comienzan en una secuencia que coincide con la marca de tiempo del ledger anterior.
* Documentación de pseudo-transacciones faltantes: UNLReport, EmitFailure, etc.
* Límite de tamaño de URIToken.
* AccountDelete todavía se enumera como una transacción, pero está deshabilitada en Xahau.
{% endhint %}

#### Escrow de Tokens IOU y PayChannels

Xahau introduce el Escrow de Tokens IOU y PayChannels como características únicas, mejorando la flexibilidad y seguridad de las transacciones. Facilitan la retención temporal de tokens IOU bajo condiciones predefinidas (escrow) y el establecimiento de canales de pago para transacciones eficientes fuera del libro mayor.

{% content-ref url="../features/network-features/payments.md" %}
[payments.md](../features/network-features/payments.md)
{% endcontent-ref %}

#### URITokens NO son NFTokens

En lugar de usar NFTokens como en XRPL, Xahau emplea URITokens. Los URITokens son una forma de activos digitales no fungibles con identificadores únicos y metadatos, ofreciendo un enfoque novedoso para la representación de activos en la blockchain.

{% content-ref url="../features/network-features/uritoken.md" %}
[uritoken.md](../features/network-features/uritoken.md)
{% endcontent-ref %}

#### Repositorio Diferente

Xahau y XRPL operan desde repositorios diferentes, lo que significa que son protocolos separados con sus propias trayectorias de desarrollo y documentación. Esta distinción implica que Xahau puede ofrecer características y optimizaciones únicas que no se encuentran en XRPL.

[https://github.com/Xahau/xahaud](https://github.com/Xahau/xahaud)

#### Proceso de Construcción Diferente (WASM & LLVM)

El proceso de construcción de Xahau incorpora WebAssembly (WASM) y la Máquina Virtual de Bajo Nivel (LLVM), que no se describen en el proceso de construcción de XRPL. Xahau utiliza estas tecnologías para mejorar las capacidades de contratos inteligentes y para mejorar el rendimiento y la compatibilidad multiplataforma de su código base.

{% content-ref url="../infrastructure/building-xahau/" %}
[building-xahau](../infrastructure/building-xahau/)
{% endcontent-ref %}

#### Tiempo de Enmienda Diferente (5 días)

El proceso de enmienda en Xahau tiene un marco de tiempo especificado de 5 días, diferente al de XRPL. Las enmiendas son cambios en el protocolo, y el período de 5 días se refiere al tiempo que tienen los validadores para alcanzar un consenso e implementar estos cambios.

{% content-ref url="../features/amendments.md" %}
[amendments.md](../features/amendments.md)
{% endcontent-ref %}

#### Secuencia de Inicio Diferente

Xahau emplea una secuencia de inicio diferente para las cuentas que XRPL. La secuencia inicial en la cuenta es el tiempo de la época de Ripple cuando la cuenta fue creada o importada en Xahau.

#### Importación / Burn 2 Mint

Importar desde XRPL a Xahau otorga a los usuarios 2 tokens XAH, lo que indica un mecanismo de incentivos para fomentar la migración de activos o el puenteo desde XRPL a Xahau, potencialmente para mejorar la adopción de la red y la liquidez.

{% content-ref url="../technical/burn-2-mint.md" %}
[burn-2-mint.md](../technical/burn-2-mint.md)
{% endcontent-ref %}

#### Recompensas de Balance (Recompensas por usar XAH)

Xahau ofrece recompensas de balance por utilizar XAH, una característica ausente en XRPL. Este es un sistema de recompensas por mantener o utilizar el token nativo de Xahau, XAH, para promover la participación en la red y su estabilidad.

{% content-ref url="../features/network-features/balance-rewards.md" %}
[balance-rewards.md](../features/network-features/balance-rewards.md)
{% endcontent-ref %}

#### Hooks

Los Hooks en Xahau, que no están presentes en XRPL, son scripts programables o funciones similares a contratos inteligentes que pueden adjuntarse a cuentas. Añaden una capa de programabilidad y automatización a las operaciones de la red.

{% content-ref url="../readme-1.md" %}
[readme-1.md](../readme-1.md)
{% endcontent-ref %}

#### Estructura de Gobernanza

La estructura de gobernanza de Xahau difiere de la de XRPL. Xahau tiene su propio enfoque para la toma de decisiones, sistemas de propuestas o roles de validadores, lo que podría influir en la evolución de la red.

{% content-ref url="../technical/governance-game.md" %}
[governance-game.md](../technical/governance-game.md)
{% endcontent-ref %}

#### Requisitos para el Nodo

Ejecutar un nodo de Xahau tiene requisitos diferentes en comparación con un nodo de XRPL. Estas diferencias podrían estar relacionadas con las especificaciones técnicas necesarias para soportar las características únicas y las demandas de la red de Xahau.

{% content-ref url="../infrastructure/node-requirements.md" %}
[node-requirements.md](../infrastructure/node-requirements.md)
{% endcontent-ref %}

#### Token nativo

El token nativo de Xahau es XAH, distinto del XRP de XRPL. Como la moneda principal dentro de la red Xahau, XAH probablemente sirve como el medio de intercambio principal y una reserva de valor, central en el marco económico de la red.

{% content-ref url="../technical/protocol-reference/data-types/currency-formats.md" %}
[currency-formats.md](../technical/protocol-reference/data-types/currency-formats.md)
{% endcontent-ref %}

#### Versionado (Usa Fechas)

Xahau emplea un sistema de versionado basado en fechas, a diferencia del enfoque de versionado de XRPL. Este método puede proporcionar un medio más intuitivo para rastrear las actualizaciones de la red y su desarrollo histórico.

{% content-ref url="../technical/versioning-process.md" %}
[versioning-process.md](../technical/versioning-process.md)
{% endcontent-ref %}
