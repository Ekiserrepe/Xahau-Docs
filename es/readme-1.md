---
description: >-
  Introducing Hooks: Enhancing XRP Ledger Protocol with Smart Contract
  Functionality
cover: .gitbook/assets/cdn.feather.webp
coverY: 0
---

# 🪝 Hooks



{% hint style="info" %}
Los Hooks son pequeños y eficientes módulos WebAssembly diseñados específicamente para el XRPL. Podrían denominarse **Contratos inteligentes para el protocolo XRP Ledger**.

Los hooks pueden escribirse en cualquier lenguaje (compilable con WebAssembly), y la mayoría de los conceptos de lógica de negocio y smart contracts pueden implementarse en un hook.

[**Desarrollado por XRPL Labs**](https://xrpl-labs.com/#team)
{% endhint %}

### ¿Qué son los Hooks? <a href="#what-are-hooks" id="what-are-hooks"></a>

Los hooks permiten la creación de lógica personalizada y automatización dentro del XRPL, haciendo las transacciones más inteligentes y convenientes. Estos módulos pequeños y eficientes añaden funcionalidad personalizada en el ledger, como la creación de triggers personalizados para eventos específicos en el ledger&#x20;

Estos triggers pueden utilizarse para enviar acciones en el ledger o ejecutar otras acciones en respuesta al evento especificado. Actualmente los hooks están disponibles en la [red Xahau](https://xahau.network/).

>_Hay un_ [_sitio para contruir Hooks_](https://hooks.xrpl.org/) _donde puedes desarrollar, probar, depurar y desplegar tus propios Hooks en testnet en tu navegador, usando nuestros ejemplos o construyendo los tuyos desde cero._

### ¿Por qué los Hooks son algo importante? <a href="#why-are-hooks-a-big-deal" id="why-are-hooks-a-big-deal"></a>

En pocas palabras, los hooks añaden una sólida funcionalidad de smart contract al XRPL, lo que le permite construir y desplegar aplicaciones con funcionalidades a medida que se adaptan a sus necesidades y requisitos específicos.

Los hooks proporcionan una plataforma versátil, ya que pueden utilizarse para implementar un amplio espectro de lógica de negocio y paradigmas de contratos inteligentes. Una vez que se configura un hook en una cuenta, te permite hacer lo siguiente:

* Bloquear o permitir transacciones desde y hacia la cuenta.
* Cambiar y realizar un seguimiento del estado interno y la lógica del hook para tomar decisiones programáticas.
* Iniciar de forma autónoma nuevas transacciones en nombre de la cuenta.

Los hooks pueden escribirse en C o cualquier otro lenguaje preferido y luego compilarse en WebAssembly.

<figure><img src="https://cdn.feather.blog/?src=https%3A%2F%2Fusenotioncms.com%2Fproxy%2Fblock%2Fd315cbec-a4e7-4345-ba48-07d936239181%252F02a28a46-80d0-4e64-a96d-33497950a60f%252Fimage_(7).png&#x26;optimizer=image" alt="Con Hooks Builder, puedes desarrollar, probar, depurar y desplegar sus propios hooks en nuestra testnet, utilizando nuestros ejemplos o construyendo los tuyos desde cero." height="100%"><figcaption></figcaption></figure>

El Hooks Builder sirve como un entorno de desarrollo integrado, facilitando la creación, prueba, depuración y despliegue de tus Hooks en nuestra red de pruebas.

Tanto si utilizas nuestros ejemplos como si construyes desde cero, Hooks Builder proporciona un entorno útil para perfeccionar y desplegar tus soluciones de smart contract.

### Algunos ejemplos de Hooks específicos y casos de uso <a href="#some-examples-of-specific-hooks" id="some-examples-of-specific-hooks"></a>

Mostrando el potencial de los Hooks con estos ejemplos concretos, cada uno ilustrando una aplicación única de la funcionalidad de los smart contracts en el XRPL:

* **Hook de Auto-Ahorro**: Automatizar el ahorro configurando un Hook para transferir una cantidad determinada de XRP a una cuenta de ahorro separada en el ledger. Esto podría hacerse para ayudar a ahorrar una porción de XRP y acumular ahorros en intervalos especificados - diaria, semanal o mensualmente. Este mecanismo de transferencia recurrente puede ser una base para desarrollar aplicaciones de finanzas personales o modelos basados en suscripciones.
* **Hook de Compensación de carbono**: Cada transacción desencadena una transferencia adicional del 1% del importe a una cuenta de compensación de carbono gestionada por una organización no gubernamental (ONG) de confianza que destina el dinero a una buena causa. Esta función puede utilizarse como base para crear aplicaciones que contribuyan a la sostenibilidad medioambiental con cada transacción realizada.
* **Hook Cortafuegos**: Filtrando las transacciones entrantes y salientes. El Hook Cortafuegos puede bloquear transacciones maliciosas originadas en cuentas fraudulentas conocidas o que contengan memos sospechosos. Al recuperar una lista de bloqueo actualizada de un gancho en una cuenta diferente, el cortafuegos mantiene una sólida defensa contra el fraude sin necesidad de intervención manual. Además, implementar límites de gasto para denegar retiradas no autorizadas de gran valor podría ser una función crucial para las aplicaciones financieras.

### **Distinguiendo Hooks de la Ethereum Virtual Machine (EVM)**

XRPL Hooks y la EVM permiten a los desarrolladores construir y desplegar lógica personalizada y automatización dentro de sus plataformas. Sin embargo, algunas diferencias clave entre estas dos tecnologías las diferencian.&#x20;

* **Compatibilidad de plataformas**: Los Hooks están adaptados para el XRP Ledger, mientras que los smart contracts están diseñados para blockchains basadas en Ethereum.
* **Eficiencia de ejecución**: Los Hooks utilizan WebAssembly (WASM), superando al bytecode utilizado por el EVM en términos de velocidad y eficiencia.
* **Tiempo de ejecución predecible**: Los Hooks XRPL utilizan guardias para garantizar que el tiempo máximo de ejecución esté bien delimitado y se conozca de antemano, lo que mejora la eficiencia.

### Alternativas a los Hooks en el XRPL

Ripple y Peersyst han anunciado que ya existe una sidechain compatible con EVM [en la devnet de la empresa](https://opensource.ripple.com/docs/evm-sidechain/intro-to-evm-sidechain/). Esta sidechain funciona como una cadena de bloques autónoma, con su propio protocolo de consenso y reglas de transacción. La sidechain de EVM es una alternativa a Hooks que añade la funcionalidad de los smart contracts al ecosistema.&#x20;

Sin embargo, es esencial tener en cuenta que los contratos sidechain EVM funcionan en Layer (Capa) 2, lo que requiere un proceso de dos pasos en el que XRP pasa a la sidechain para la ejecución del contrato y luego de vuelta al ledger. Una vez en la sidechain, otra vez en el XRPL, lo que significa que los contratos inteligentes de Layer 2 no pueden influir en el flujo. Los Hooks pueden decidir si una transacción está permitida en primer lugar. La layer 2 puede tomar una decisión retroactiva, pero la transacción inicial ya se ha producido.

Los Hooks están más estrechamente integrados con XRPL, operando directamente en la Layer 1 de XRPL, por lo que están más estrechamente integrados con la tecnología blockchain subyacente que la sidechain compatible con EVM para aprovechar las características y capacidades específicas de la plataforma XRPL. Con la escalabilidad y el rendimiento inherentes de WebAssembly, los Hooks son óptimos para mejorar la funcionalidad de XRPL.

### Los Hooks ampliarán la funcionalidad del ledger y ayudarán al crecimiento del XRPL

Los Hooks añaden capacidades nativas de smart contracts al XRPL, permitiendo la creación de aplicaciones personalizadas que satisfagan las necesidades únicas de los usuarios, aportando nuevas funcionalidades y abriendo dominios de funcionalidad completamente nuevos. Con Hooks, las posibilidades son prácticamente ilimitadas.

A medida que el XRPL siga creciendo, no hay duda de que Hooks desempeñará un papel importante en el impulso de la innovación y la adopción de la plataforma por parte de los usuarios minoristas y empresariales.
