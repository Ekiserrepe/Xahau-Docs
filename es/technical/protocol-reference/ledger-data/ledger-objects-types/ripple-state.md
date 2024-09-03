# Estado de Ripple

[\[Fuente\]](https://github.com/ripple/rippled/blob/5d2d88209f1732a0f8d592012094e345cbe3e675/src/ripple/protocol/impl/LedgerFormats.cpp#L70)

El tipo de objeto `RippleState` conecta dos cuentas en una sola divisa. Conceptualmente, un objeto `RippleState` representa dos líneas de confianza entre las cuentas, una desde cada lado. Cada cuenta puede cambiar la configuración de su lado del objeto `RippleState`, pero el saldo es un valor compartido único. Una línea de confianza que está completamente en su estado predeterminado se considera igual a una línea de confianza que no existe, por lo que `rippled` elimina los objetos `RippleState` cuando sus propiedades están completamente en su estado predeterminado.

### Cuenta Alta vs. Cuenta Baja

Solo puede haber un objeto `RippleState` por divisa para cualquier par de cuentas dado. Dado que ninguna cuenta tiene privilegios en Xahau, un objeto `RippleState` ordena las direcciones de las cuentas numéricamente para asegurar una forma canónica. La dirección que sea numéricamente inferior cuando se decodifica se considera la "cuenta baja" y la otra es la "cuenta alta". El saldo neto de la línea de confianza se almacena desde la perspectiva de la cuenta baja.

El "emisor" del saldo en una línea de confianza depende de si el saldo es positivo o negativo. Si un objeto `RippleState` muestra un saldo positivo, la cuenta alta es el emisor. Si el saldo es negativo, la cuenta baja es el emisor. A menudo, el emisor tiene su límite establecido en 0 y la otra cuenta tiene un límite positivo, pero esto no es confiable porque los límites pueden cambiar sin afectar un saldo existente.

### Ejemplo JSON

```json
{
    "Balance": {
        "currency": "USD",
        "issuer": "rrrrrrrrrrrrrrrrrrrrBZbvji",
        "value": "-10"
    },
    "Flags": 393216,
    "HighLimit": {
        "currency": "USD",
        "issuer": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
        "value": "110"
    },
    "HighNode": "0000000000000000",
    "LedgerEntryType": "RippleState",
    "LowLimit": {
        "currency": "USD",
        "issuer": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW",
        "value": "0"
    },
    "LowNode": "0000000000000000",
    "PreviousTxnID": "E3FE6EA3D48F0C2B639448020EA4F03D4F4F8FFDB243A852A0F59177921B4879",
    "PreviousTxnLgrSeq": 14090896,
    "LockCount": 1,
    "LockedBalance": {
        "currency": "USD",
        "issuer": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW",
        "value": "10"
    }
    "index": "9CA88CDEDFF9252B3DE183CE35B038F57282BC9503CDFA1923EF9A95DF0D6F7B"
}
```

### Campos

Un objeto `RippleState` tiene los siguientes campos:

| Nombre                | Tipo JSON | Tipo Interno | ¿Requerido? | Descripción                                                                                                                                                                                                                            |
| ------------------- | --------- | ------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Balance`           | Object    | Amount        | Sí       | El saldo de la línea de confianza, desde la perspectiva de la cuenta baja. Un saldo negativo indica que la cuenta alta posee tokens emitidos por la cuenta baja. El emisor en este caso siempre se establece en el valor neutro ACCOUNT\_ONE. |
| `Flags`             | Number    | UInt32        | Sí       | Un mapa de bits de opciones booleanas habilitadas para este objeto.                                                                                                                                                                                  |
| `HighLimit`         | Object    | Amount        | Sí       | El límite que la cuenta alta ha establecido en la línea de confianza. El `issuer` es la dirección de la cuenta alta que estableció este límite.                                                                                                        |
| `HighNode`          | String    | UInt64        | Sí       | (Omitido en algunos ledgers históricos) Un indicio que indica qué página del directorio de propietarios de la cuenta alta enlaza a este objeto, en caso de que el directorio consista en varias páginas.                                                        |
| `HighQualityIn`     | Number    | UInt32        | No        | La calidad de entrada establecida por la cuenta alta, como un entero en la relación implícita `HighQualityIn`:1,000,000,000. Como un caso especial, el valor 0 es equvicalente a 1 billón, o valor nominal.                                               |
| `HighQualityOut`    | Number    | UInt32        | No        | La calidad de salida establecida por la cuenta alta, como un entero en la relación implícita `HighQualityOut`:1,000,000,000. Como caso especial, el valor 0 es equivalenet a 1 billón o un valor nominal.                                             |
| `LedgerEntryType`   | String    | UInt16        | Sí       | El valor `0x0072`, mapeado a la cadena `RippleState`, indica que este objeto es un objeto RippleState.                                                                                                                            |
| `LowLimit`          | Object    | Amount        | Sí       | El límite que la cuenta baja ha establecido en la línea de confianza. El `issuer` es la dirección de la cuenta baja que estableció este límite.                                                                                                          |
| `LowNode`           | String    | UInt64        | Sí       | (Omitido en algunos ledgers históricos) Un indicio que indica qué página del directorio de propietarios de la cuenta baja enlaza a este objeto, en caso de que el directorio consista en varias páginas.                                                         |
| `LowQualityIn`      | Number    | UInt32        | No        | La calidad de entrada establecida por la cuenta baja, como un entero en la relación implícita `LowQualityIn`:1,000,000,000 o valor nominal.                                                 |
| `LowQualityOut`     | Number    | UInt32        | No        | La calidad de salida establecida por la cuenta baja, como un entero en la relación implícita LowQualityOut:1,000,000,000. Como caso especial, el valor 0 es equeivalente a 1 billón o al valor nominal.                                               |
| `PreviousTxnID`     | String    | Hash256       | Sí       | 	El hash identificador de la transacción que modificó este objeto más recientemente.                                                                                                                                                       |
| `PreviousTxnLgrSeq` | Number    | UInt32        | Sí       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que más recientemente modificó este objeto.                                                                                                                       |
| `LockCount`         | Number    | UInt32        | No        | El número total de saldos bloqueados en un objeto de ledger RippleState.                                                                                                                                                                       |
| `LockedBalance`     | Object    | Amount        | No        | La cantidad actual de tokens bloqueados para una línea de confianza específica.                                                                                                                                                                           |

### Flags de RippleState

Hay varias opciones que se pueden habilitar o deshabilitar para una línea de confianza. Estas opciones se pueden cambiar con una \[transacción TrustSet]\[]. En el ledger, los flags se representan como valores binarios que se pueden combinar con operaciones bitwise-or. Los valores de bits para los flags en el ledger son diferentes de los valores utilizados para habilitar o deshabilitar esos flags en una transacción. Los flags en el ledger tienen nombres que comienzan con **`lsf`**.

Los objetos RippleState pueden tener los siguientes valores de flags:

| Nombre del Flag         | Valor Hex    | Valor Decimal | Flag TrustSet Correspondiente | Descripción                                                                                         |
| ----------------- | ------------ | ------------- | --------------------------- | --------------------------------------------------------------------------------------------------- |
| `lsfLowReserve`   | `0x00010000` | 65536         | (Ninguno)                      | Este objeto RippleState contribuye a la reserva de propietario de la cuenta baja.                             |
| `lsfHighReserve`  | `0x00020000` | 131072        | (Ninguno)                      | Este objeto RippleState contribuye a la reserva de propietario de la cuenta alta.                            |
| `lsfLowAuth`      | `0x00040000` | 262144        | `tfSetAuth`                 | La cuenta baja ha autorizado a la cuenta alta a mantener tokens emitidos por la cuenta baja.           |
| `lsfHighAuth`     | `0x00080000` | 524288        | `tfSetAuth`                 | La cuenta alta ha autorizado a la cuenta baja a mantener tokens emitidos por la cuenta alta.          |
| `lsfLowNoRipple`  | `0x00100000` | 1048576       | `tfSetNoRipple`             | La cuenta baja ha deshabilitado el rippling desde esta línea de confianza.                                         |
| `lsfHighNoRipple` | `0x00200000` | 2097152       | `tfSetNoRipple`             | La cuenta alta ha deshabilitado el rippling desde esta línea de confianza.                                        |
| `lsfLowFreeze`    | `0x00400000` | 4194304       | `tfSetFreeze`               | La cuenta baja ha congelado la línea de confianza, impidiendo que la cuenta alta transfiera el activo. |
| `lsfHighFreeze`   | `0x00800000` | 8388608       | `tfSetFreeze`               | La cuenta alta ha congelado la línea de confianza, impidiendo que la cuenta baja transfiera el activo. |

### Contribuyendo a la Reserva de Propietario

Si una cuenta modifica una línea de confianza para ponerla en un estado no predeterminado, entonces esa línea de confianza cuenta para la reserva de propietario de la cuenta. En un objeto RippleState, los flags `lsfLowReserve` y `lsfHighReserve` indican qué cuenta(s) son responsables de la reserva de propietario. El servidor `rippled` establece automáticamente estos flags cuando modifica una línea de confianza.

Los valores que cuentan hacia el estado no predeterminado de una línea de confianza son los siguientes:

The values that count towards a trust line's non-default state are as follows:

| La cuenta alta es responsable si...                          | La cuenta baja es esponsable si...                          |
| ------------------------------------------------------- | ------------------------------------------------------ |
| El `Balance` es negativo (la cuenta alta posee divisa) | El `Balance` es positivo (la cuenta baja posee divisa) |
| `HighLimit` no es `0`                                  | `LowLimit` no es `0`                                  |
| `LowQualityIn` no es `0` y no es `1000000000`          | `HighQualityIn` no es `0` y no es `1000000000`        |
| `LowQualityOut` no es `0` y no es `1000000000`         | `HighQualityOut` no es `0` y no es `1000000000`       |
| El flag `lsfHighNoRipple` no está en su estado predeterminado      | El flag `lsfLowNoRipple` no está en su estado predeterminado      |
| El flag `lsfHighFreeze` está habilitado                         | El flag `lsfLowFreeze` está habilitado                         |

Los flags **`lsfLowAuth`** y **`lsfHighAuth`** no cuentan contra el estado predeterminado, porque no se pueden deshabilitar.

El estado predeterminado de los dos flags No Ripple depende del estado del flag `lsfDefaultRipple` en sus objetos AccountRoot correspondientes. Si Default Ripple está deshabilitado (el predeterminado), entonces el estado predeterminado del flag `lsfNoRipple` está habilitado para todas las líneas de confianza de una cuenta. Si una cuenta habilita Default Ripple, entonces el flag `lsfNoRipple` está _deshabilitado_ (el rippling está habilitado) para las líneas de confianza de una cuenta por defecto.

**Nota:** Antes de la introducción del flag Default Ripple en la versión 0.27.3 de `rippled` (10 de marzo de 2015), el estado predeterminado para todas las líneas de confianza era con ambos flags No Ripple deshabilitados (rippling habilitado).

Afortunadamente, `rippled` utiliza evaluación perezosa para calcular la reserva de propietario. Esto significa que, incluso si una cuenta cambia el estado predeterminado de todas sus líneas de confianza al cambiar el flag Default Ripple, la reserva de esa cuenta permanece igual inicialmente. Si una cuenta modifica una línea de confianza, `rippled` vuelve a evaluar si esa línea de confianza individual está en su estado predeterminado y si debe contribuir a la reserva de propietario.

### Formato de ID de RippleState

El ID de un objeto RippleState es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio RippleState (`0x0072`)
* El AccountID de la cuenta baja
* El AccountID de la cuenta alta
* El código de moneda de 160 bits de la(s) línea(s) de confianza
