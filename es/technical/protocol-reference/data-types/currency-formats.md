# Formatos de divisas

Xahau tiene dos tipos de activos digitales: XAH y tokens. Ambos tipos tienen alta precisión, aunque sus formatos son diferentes.

### Comparación

La siguiente tabla resume algunas de las diferencias entre XAH y los tokens en Xahau:

| XAH                                                                                  | Tokens                                                                                         |
| ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------- |
| No tiene emisor.                                                                       | Siempre emitido por una cuenta Xahau.                                                              |
| Se especifica como una cadena de texto.                                                               | Se especifica como un objeto.                                                                        |
| Se rastrea en cuentas.                                                                 | Se rastrea en líneas de confianza (trust lines).                                                                        |
| Nunca se puede crear; solo se puede destruir.                                         | Se puede emitir o canjear libremente.                                                              |
| Valor mínimo: `0`. (No puede ser negativo.)                                            | Valor mínimo: `-9999999999999999e80`. Valor absoluto mínimo distinto de cero: `1000000000000000e-96`. |
| Valor máximo `100000000000` (1011) XAH. Eo es `100000000000000000` (1017) "drops". | Valor máximo `9999999999999999e80`.                                                           |
| Preciso hasta el "drop" más cercano (0.000001 XAH)                                         | 15 dígitos decimales de precisión.                                                                |
| No se puede congelar.                                                                     | El emisor puede congelar saldos.                                                                |
| Sin tarifas de transferencia; Los pagos XAH-a-XAH son siempre directos.                             | Pueden tomar rutas indirectas con cada emisor cobrando un porcentaje de tarifa por transferencia.                   |
| Puede ser utilizado en Payment Channels y Escrow.                                          | Puede ser utilizado en Payment Channels o Escrow.                                                   |

Para más información, consulta ¿Qué es XAH? y Tokens.

### Especificar Cantidades de divisas

Usa el formato apropiado para el tipo de moneda que deseas especificar:

* Cantidades de XAH 
* Cantidades de Tokens

#### Cantidades de XAH

Para especificar una cantidad de XAH, utiliza un String Number (Número de Cadena) que indique _drops_ de XAH, donde cada drop equivale a 0.000001 XAH. Por ejemplo, para especificar 13.1 XAH:

```
"13100000"
```

**No especifiques XAH como un objeto.**

Las cantidades de XAH no pueden ser negativas.

#### Cantidades de tokens

Para especificar una cantidad de un token (fungible), utiliza un objeto Amount. Este es un objeto JSON con tres campos:

| `Campo`    | Tipo                   | Descripción                                                                                                                                                                                                                                                                                                       |
| ---------- | ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `currency` | String - Código de divisa | Código de moneda arbitrario para el token. No puede ser XAH.                                                                                                                                                                                                                                                             |
| `value`    | String Number          | Representación decimal entre comillas de la cantidad del token. Esto puede incluir notación científica, como `1.23e11` que significa 123,000,000,000. Tanto `e` como `E` pueden ser utilizados. Esto puede ser negativo al mostrar saldos, pero los valores negativos están prohibidos en otros contextos como especificar cuánto enviar. |
| `issuer`   | String                 | Generalmente, la cuenta que emite este token. En casos especiales, esto puede referirse a la cuenta que posee el token en su lugar (por ejemplo, en una transacción de Clawback).                                                                                                                                             |

**Precaución:** Estos nombres de campos distinguen entre mayúsculas y minúsculas.

Por ejemplo, para representar 153.75$ dólares estadounidenses emitidos por la cuenta `r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59`, deberías especificar:

```json
{
    "currency": "USD",
    "value": "153.75",
    "issuer": "r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59"
}
```

#### Especificar sin cantidades

En algunos casos, necesitas definir un activo (que podría ser XAH o un token) sin una cantidad específica, como cuando defines un libro de órdenes en el exchange descentralizado.

Para describir un token sin una cantidad, especifícalo como un objeto de moneda, pero omite el campo `value`. Por ejemplo:

```json
{
  "currency": "TST",
  "issuer": "rP9jPyP5kyvFRb6ZiRghAGw5u8SGAmU4bd"
}
```

Para describir XAH sin una cantidad, especifícalo como un objeto JSON con solo un campo `currency`. Nunca incluyas un campo `issuer` para XAH. Por ejemplo:

```json
{
  "currency": "XAH"
}
```

### String Numbers (Números de cadena)

### Precicisón de XAH

XAH tiene la misma precisión que un entero sin signo de 64 bits, donde cada unidad equivale a 0.000001 XAH. Utiliza matemáticas enteras, por lo que cualquier cantidad menor a un drop completo se redondea hacia abajo.

### Precisión de Tokens

Los tokens pueden representar una amplia variedad de activos, incluidos aquellos que normalmente se miden en denominaciones muy pequeñas o muy grandes. Este formato utiliza dígitos significativos y un exponente de potencia de diez de manera similar a la notación científica. El formato admite dígitos significativos y exponentes positivos y negativos dentro del rango especificado. A diferencia de las representaciones típicas de punto flotante de números no enteros, este formato utiliza matemáticas enteras para todos los cálculos, por lo que siempre mantiene 15 dígitos decimales de precisión. Las operaciones de multiplicación y división tienen ajustes para compensar el redondeo excesivo en los dígitos menos significativos.

Al enviar cantidades de tokens en la red peer-to-peer de Xahau, los servidores serializan la cantidad en un valor binario de 64 bits.

**Consejo:** Para tokens que no deben ser divisibles en absoluto, consulta Tokens No Fungibles (NFTs).

### Códigos de divisa

#### Códigos de divisa estándar

El formato estándar para los códigos de moneda es una cadena de tres caracteres como `USD`. Este formato está destinado a su uso con [Códigos de divisa ISO 4217](https://www.xe.com/iso4217.php). Se aplican las siguientes reglas:

* Los códigos de divisa deben tener exactamente 3 caracteres ASCII de longitud. Se permiten los siguientes caracteres: todas las letras mayúsculas y minúsculas, dígitos, así como los símbolos `?`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `*`, `<`, `>`, `(`, `)`, `{`, `}`, `[`, `]`, y `|`.
* Los códigos de divisa distinguen entre mayúsculas y minúsculas.
* El código de divisa XAH (todo en mayúsculas) está prohibido. El XAH real generalmente no utiliza un código de divisa en el protocolo de Xahau.

A nivel de protocolo, este formato se serializa en un valor binario de 160 bits que comienza con `0x00`.

#### Códigos de divisa no estándar

También puedes usar una cadena hexadecimal de 160 bits (40 caracteres) como `015841551A748AD2C1F76FF6ECB0CCCD00000000` como el código de divisa. Para evitar que se trate como un código de divisa "estándar", los primeros 8 bits NO DEBEN ser `0x00`.

**Obsoleto:** Algunas versiones anteriores de [ripple-lib](https://github.com/XRPLF/xrpl.js) admitían un tipo de código de divisa "portador de intereses" o "demora". Estos códigos tienen los primeros 8 bits `0x01`. Las divisas con demora / portadoras de intereses ya no son compatibles, pero puedes encontrarlas en los datos del libro mayor. Para obtener más información, consulta Demora.