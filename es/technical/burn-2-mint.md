---
description: >-
  Burn2Mint es un primitivo inter-cadena de bajo nivel y está destinado a usuarios empresariales que desean autoabastecerse de liquidez según sea necesario para sus propios fines.
---

# Burn 2 Mint (B2M)

{% hint style="danger" %}
Desde que la enmienda ZeroB2M fue aprobada en Xahau, Burn 2 Mint (B2M) ya no está disponible para acuñar nuevos activos. B2M sigue estando disponible, pero solo para **sincronización de claves** o para activar una cuenta (para lo cual la información y los procedimientos que se detallan a continuación siguen siendo relevantes)

[https://xahscan.com/amendment/7CA0426E7F411D39BB014E57CD9E08F61DE1750F0D41FCD428D9FB80BB7596B0](https://xahscan.com/amendment/7CA0426E7F411D39BB014E57CD9E08F61DE1750F0D41FCD428D9FB80BB7596B0)
{% endhint %}

## Manual Técnico de Burn2Mint (Xahau Testnet)

{% hint style="warning" %}
**Este proceso es deliberadamente complejo y no está diseñado para usuarios finales.** Los errores pueden resultar en una quema que no se pueda convertir en una acuñación.

Esta advertencia aparecerá en la versión de producción de este documento. Puedes ignorarla porque estás quemando XRP de prueba en esta instancia.
{% endhint %}

Burn2Mint es un primitivo inter-cadena de bajo nivel destinado a usuarios empresariales que desean autoabastecerse de liquidez según sea necesario para sus propios fines. Operar tus propios nodos y realizar esta operación tú mismo en tus nodos significa que tú y solo tú eres responsable del resultado del procedimiento.

La generación y recopilación de XPOPs depende fundamentalmente de la recopilación de mensajes de validación en la red de superposición, que son efímeros por naturaleza. Si tus nodos no están conectados de manera confiable, o si tu hardware, conexión de red o sistema operativo fallan en el momento incorrecto, es posible que el XPOP para una Transacción de Quema no se genere o no se genere correctamente. Esto puede llevar a la pérdida de fondos, ya que si no se capturó el XPOP de la Transacción de Quema a tiempo, es posible que los mensajes de validación se pierdan para siempre, lo que haría imposible una Transacción de Acuñación a pesar de una Transacción de Quema exitosa.

Si no te sientes cómodo asumiendo estos riesgos técnicos, no entiendes lo que estás haciendo, o no eres un usuario empresarial, entonces te recomendamos que obtengas tu liquidez a través de otras alternativas más amigables.

### Configuración

Para realizar B2M, el usuario debe operar _dos_ nodos de protocolo XRPL:

1. **Obtener xPOP de la quema**

{% tabs %}
{% tab title="xPOP Collector & Server (easier)" %}
Ejecuta esto (por ejemplo, usando Docker Compose; consulta los Docs del repositorio)\
[**https://github.com/Xahau/Validation-Ledger-Tx-Store-to-xPOP**](https://github.com/Xahau/Validation-Ledger-Tx-Store-to-xPOP)

* El servicio escucha mensajes de validación XRPL, ledgers cerrados y transacciones.
* Almacena estos datos en un sistema de archivos organizado.
* Es esencial para generar xPOPs, ya que los mensajes de validación XRPL son efímeros; sin almacenarlos, una transacción de quema no se puede convertir en una acuñación.
* **Cómo funciona**:
  * Un observador se conecta a varios nodos XRPL y escucha datos específicos.
  * Los datos capturados se guardan, organizan y utilizan para generar xPOPs.

Simplemente recupera el xPOP ahora desde:\
`http[s]://{your-host}:{your-port}/xpop/{tx-hash}`

Puedes recuperar/crear fácilmente xPOPs desde un nodo usando el repositorio mencionado anteriormente con este paquete NPM:

[**https://www.npmjs.com/package/xpop**](https://www.npmjs.com/package/xpop)
{% endtab %}

{% tab title="or: dedicated "Burning Node"" %}
**Nodo de Quema**, que incluye:

* Una instancia modificada de Rippled (modificada para registrar XPOPs)
* Binario universal para Linux aquí: [https://tvntezq.dlvr.cloud/rippled\_with\_xpop](https://tvntezq.dlvr.cloud/rippled\_with\_xpop)
* [https://github.com/RichardAH/rippled/tree/proof-of-burn](https://github.com/RichardAH/rippled/tree/proof-of-burn)
* Ejecuta con un rippled.cfg que tenga:
  * una cláusula **\[xpop\_dir]** que especifique un directorio de salida para los XPOPs generados.
  * una cláusula **\[network\_id]** que especifique network\_id: 1
    * (en producción esto sería la red 0)
  *   un archivo `validators.txt` que contenga:\\

      ```
      [validator_list_sites]
      https://vl.altnet.rippletest.net
      [validator_list_keys]
      ED264807102805220DA0F312E71FC2C69E1552C9C5790F6C25E3729DEB573D5860
      ```
{% endtab %}
{% endtabs %}

2. **Acuñar**

* En este escenario de prueba, te conectarás directamente a Xahau. No necesitas ejecutar tu propio nodo. En el escenario de producción, necesitarás ejecutar un nodo de Acuñación, que es simplemente un nodo estándar para la red de destino. Esto se debe a que es probable que los nodos públicos opten por no aceptar transacciones de Importación debido al riesgo legal.
  * Conéctate a **wss://xahau-test.net**
  * O descarga y ejecuta: [**https://github.com/Xahau/Xahau-Testnet-Docker**](https://github.com/Xahau/Xahau-Testnet-Docker)

## Ejemplo (nodejs)

Este ejemplo crea una cuenta de prueba, quema 10 XRP y luego importa esto en Xahau Testnet, usando el xPOP obtenido con las herramientas mencionadas anteriormente.

{% embed url="https://gist.github.com/WietseWind/cd8a7a8c88f218fe7b768f59a665685d" %}

## Resumen Técnico del procedimiento B2M

#### Nuevo Tipo de Transacción: `Import`

Xahau Testnet (_network_id\_=21338_) introduce un nuevo tipo de transacción llamado _**Import**_, que acepta un XPOP de la cadena testnet de Ripple (_network\_id=1_) y permite una transferencia de valor unidireccional de “burn-to-mint” y sincronización de claves/cuentas.

### Transacción de Quema

Una _**Transacción de Quema**_ es una transacción que quema XRP en una cadena XRPL de origen, la cual se utilizaría como prueba (XPOP) para acuñar en una cadena XRPL de destino.

Los siguientes tipos de transacciones son compatibles:

* AccountSet
* SetRegularKey
* SignerListSet.

{% hint style="warning" %}
Los Tickets _**no son aceptados**_ en **ninguna** Transacción de Quema y llevarían a que la Transacción de Quema no sea válida para acuñación. Para protegerse contra el spam, las transacciones deben validarse con un número de secuencia real y natural.
{% endhint %}

Ten en cuenta que otros tipos de transacciones no son actualmente compatibles y no se pueden usar para acuñar en Xahau Testnet. (Sin embargo, esto puede cambiar).

Los tres tipos de transacciones pueden usarse para acuñar. Esto significa que la _**Fee**_ quemada por la Transacción de Quema se acuña posteriormente en Xahau Testnet después de una `Import` exitosa.

Si se utiliza `SetRegularKey` o `SignerListSet`, entonces la sincronización de claves ocurre de acuerdo con las reglas estándar del tipo de transacción. Si se utiliza `AccountSet`, entonces no ocurre sincronización de claves en la cadena XRPL de destino (Xahau Testnet).

Un campo llamado _**OperationLimit**_ debe estar presente en la Transacción de Quema para ser el ID de la Red de la cadena de destino. Este campo es para evitar ataques de repetición en otras cadenas integradas con B2M.

La Transacción de Quema puede usarse para Acuñar si tiene un código de transacción **tesSUCCESS** o cualquiera de los códigos de transacción **tec**. Esto significa que _si_ se quemó la tarifa, la transacción puede usarse para acuñar. Sin embargo, la sincronización de claves _solo_ ocurre cuando el resultado de la Transacción de Quema fue tesSUCCESS.

Ejemplo de transacción de Quema:

```json
{
	"TransactionType": "AccountSet",
	"Fee": 10000000,
	"OperationLimit": 21338
}
```

### Colección XPOP

Antes de enviar la Transacción de Quema, asegúrate de que tu Nodo de Quema esté sincronizado con la cadena de prueba (Network ID: `1`).

Después de verificar el estado de tu Nodo de Quema, envía la Transacción de Quema firmada al Nodo de Quema.

El Nodo de Quema observa los ledgers cerrados en busca de transacciones que contengan el campo **OperationLimit** y utiliza los mensajes de validación recopilados para generar un XPOP (Prueba de Quema). Estos se escriben en un archivo en el directorio especificado en la cláusula **\[xpop\_dir]** en el `rippled.cfg` del Nodo de Quema.

Espera a que se cierre el ledger, luego navega por el xpop\_dir para encontrar el XPOP de la Transacción de Quema según su TXID. El archivo contiene un documento JSON, que es el XPOP que transmitirás a la cadena XRPL de destino (HooksV3).

Toma el contenido bruto de este archivo y codifícalo como HEX. Esto se convertirá en el contenido del campo \*_Blob_ en la transacción `Import`.

### Transacción de Acuñación

Es posible que tu códec binario no tenga los campos necesarios para construir la transacción `Import` (Acuñar). Si estás utilizando ripple-binary-codec, puedes actualizar su archivo `definitions.json` de la siguiente manera:

1. Cambia de directorio a `node_modules`
2. Ejecuta `find . | grep 'dist/enums/definitions.json'` para localizar el archivo relevante para actualizar.
3. Conéctate a **wss://hooks-testnet-v3.xrpl-labs.com**
4. Solicita: `{"command":"server_definitions"}`
5. Vuelca el contenido de la clave `"result"` en `definitions.json` desde el paso 2.

El tipo de transacción `Import` solo toma un campo no común: _**Blob.**_. Este debe contener el XPOP codificado en HEX desde la fase de Recopilación de XPOP.

* Puedes hacer esto con `cat xpopjsonfile | xxd -p | tr -d '\n'`

El campo `Account` y el campo `SigningPubKey` (o el array de Signers) deben coincidir exactamente entre la Transacción de Quema y la Transacción de Acuñación. Los usuarios solo pueden acuñar en la misma cuenta desde la que quemaron.

Si el campo `Account` especifica una cuenta que aún no existe en Xahau Testnet, se creará. En este caso, usa 0 para el campo `Sequence` en la Transacción de Acuñación. Si la cuenta ya existe en Xahau Testnet, usa el siguiente número de secuencia disponible en la cuenta en Xahau Testnet.

Ejemplo Import:

```json
{
	"Account": "<same as it was in the Burn Transaction>",
	"TransactionType": "Import",
	"Blob": "<hex encoded (upper case) XPOP>",
	"Sequence": 0
} 
```

IMPORTANTE: **¡La Transacción de Acuñación debe firmarse exactamente de la misma manera y por la misma cuenta que la Transacción de Quema!**

Codifica y firma la transacción de manera adecuada, obteniendo un blob de transacción firmado (hexadecimal).

Si no estás ejecutando tu propio nodo de Acuñación:

* Conéctate a **wss://xahau-testn.et**
* Envía la transacción al nodo:

```json
{
	"command": "submit",
	"tx_blob": "<HEX ENCODED IMPORT TXN>"
}
```

O, si estás ejecutando tu propio nodo de Acuñación:

* Asegúrate de que el Nodo de Acuñación esté en funcionamiento y sincronizado con el Network ID 21338.
* Usa la llamada RPC `submit` para enviar la Transacción de Acuñación.
  * Puedes hacer esto desde la línea de comando usando `./hooksv3d submit <hex aquí>`

### Consideraciones

Si la Cuenta se va a crear en Xahau Testnet pero la configuración de clave de la cuenta no está clara en el contexto de la Transacción de Quema, entonces la Cuenta se crea en un modo bloqueado. Posteriormente, se puede reconfigurar utilizando cualquier tipo de transacción de clave.

Si la Transacción de Quema es una transacción `SignerListSet` o `SetRegularKey`, entonces, siempre que haya tenido un resultado de transacción tesSUCCESS en la cadena XRPL de origen (testnet), esa misma operación de clave se aplica a esa misma cuenta en la cadena XRPL de destino (Xahau Testnet).

Durante las pruebas, las cuentas en Xahau Testnet _pueden_ eliminarse, sin embargo, en la cadena de producción habilitada para Hooks, no podrán eliminarse.

Las cuentas en Xahau Testnet tienen un campo opcional llamado _**ImportSequence**_ en la AccountRoot. Si alguna vez se ha utilizado **Import** en esa cuenta en Xahau Testnet, este campo está presente y se completa con el número de secuencia de la Transacción de Quema importada más reciente. Esto es para prevenir ataques de repetición, pero también significa que nunca debes enviar tus Burn2Mints fuera de secuencia, de lo contrario, las transacciones omitidas nunca serán aceptadas para Acuñación.
