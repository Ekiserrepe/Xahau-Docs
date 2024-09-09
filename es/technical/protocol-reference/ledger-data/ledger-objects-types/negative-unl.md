# UNL Negativa

_(Añadido por la \[enmienda NegativeUNL]\[].)_

El tipo de objeto `NegativeUNL` contiene el estado actual de la Lista UNL Negativa, que es una lista de validadores confiables que actualmente se cree que están offline.

Cada versión de ledger contiene **como máximo un** objeto `NegativeUNL`. Si no hay validadores actualmente deshabilitados o programados para ser deshabilitados, no hay ningún objeto `NegativeUNL` en el ledger.

### Ejemplo JSON

```json
{
  "DisabledValidators": [
    {
      "DisabledValidator": {
        "FirstLedgerSequence": 1609728,
        "PublicKey": "ED6629D456285AE3613B285F65BBFF168D695BA3921F309949AFCD2CA7AFEC16FE"
      }
    }
  ],
  "Flags": 0,
  "LedgerEntryType": "NegativeUNL",
  "index": "2E8A59AA9D3B5B186B0B9E0F62E6C02587CA74A4D778938E957B6357D364B244"
}
```

Un objeto `NegativeUNL` tiene los siguientes campos:

| Nombre                  | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                           |
| --------------------- | --------- | ------------------- | --------- | --------------------------------------------------------------------------------------------------------------------- |
| `DisabledValidators`  | Array     | Array               | No        | Una lista de objetos `DisabledValidator` (ver abajo), cada uno representando un validador confiable que actualmente está deshabilitado.  |
| `Flags`               | Number    | UInt32              | Sí       | Un mapa de bits de flags booleanos. No se definen flags para el tipo de objeto NegativeUNL, por lo que este valor siempre es `0`.        |
| `LedgerEntryType`     | String    | UInt16              | Sí       | El valor `0x004E`, mapeado a la cadena `NegativeUNL`, indica que este objeto es la Lista UNL Negativa.               |
| `ValidatorToDisable`  | String    | Blob                | No        | La clave pública de un validador confiable que está programado para ser deshabilitado en el próximo ledger flag.                       |
| `ValidatorToReEnable` | String    | Blob                | No        | La clave pública de un validador confiable en la Lista UNL Negativa que está programado para ser habilitado nuevamente en el próximo ledger flag. |

### Objetos DisabledValidator

Cada objeto `DisabledValidator` representa un validador deshabilitado. En JSON, un objeto `DisabledValidator` tiene un campo, `DisabledValidator`, que a su vez contiene otro objeto con los siguientes campos:

| Nombre                  | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                              |
| --------------------- | --------- | ------------------- | ------------------------------------------------------------------------ |
| `FirstLedgerSequence` | Number    | UInt32              | El \[índice del ledger]\[] cuando el validador fue agregado a la Lista UNL Negativa. |
| `PublicKey`           | String    | Blob                | La clave pública maestra del validador, en hexadecimal.                  |

### Formato de ID de NegativeUNL

El ID del objeto `NegativeUNL` es el hash de la clave de espacio `NegativeUNL` (`0x004E`) únicamente. Esto significa que el ID del objeto `NegativeUNL` en un ledger es siempre:

```
2E8A59AA9D3B5B186B0B9E0F62E6C02587CA74A4D778938E957B6357D364B244
```
