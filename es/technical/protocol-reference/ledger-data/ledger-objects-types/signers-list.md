# Lista de Firmantes

[\[Fuente\]](https://github.com/ripple/rippled/blob/6d2e3da30696bd10e3bb11a5ff6d45d2c4dae90f/src/ripple/protocol/impl/LedgerFormats.cpp#L127)

_(Añadido por la \[enmienda MultiSign]\[].)_

El tipo de objeto `SignerList` representa una lista de partes que, como grupo, están autorizadas para firmar una transacción en lugar de una cuenta individual. Puedes crear, reemplazar o eliminar una lista de firmantes utilizando una \[transacción SignerListSet]\[].

### Ejemplo JSON

```json
{
    "Flags": 0,
    "LedgerEntryType": "SignerList",
    "OwnerNode": "0000000000000000",
    "PreviousTxnID": "5904C0DC72C58A83AEFED2FFC5386356AA83FCA6A88C89D00646E51E687CDBE4",
    "PreviousTxnLgrSeq": 16061435,
    "SignerEntries": [
        {
            "SignerEntry": {
                "Account": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW",
                "SignerWeight": 2
            }
        },
        {
            "SignerEntry": {
                "Account": "raKEEVSGnKSD9Zyvxu4z6Pqpm4ABH8FS6n",
                "SignerWeight": 1
            }
        },
        {
            "SignerEntry": {
                "Account": "rUpy3eEg8rqjqfUoLeBnZkscbKbFsKXC3v",
                "SignerWeight": 1
            }
        }
    ],
    "SignerListID": 0,
    "SignerQuorum": 3,
    "index": "A9C28A28B85CD533217F5C0A0C7767666B093FA58A0F2D80026FCC4CD932DDC7"
}
```

### Campos

Un objeto `SignerList` tiene los siguientes campos:

| Nombre                | Tipo JSON | Tipo Intero | ¿Requerido? | Descripción                                                                                                                                                                           |
| ------------------- | --------- | ------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Flags`             | Number    | UInt32        | Sí       | Un mapa de bits de flags booleanos habilitados para esta lista de firmantes. Para más información, consulta los Flags de SignerList.                                                                                  |
| `LedgerEntryType`   | String    | UInt16        | Sí       | El valor `0x0053`, mapeado a la cadena `SignerList`, indica que este objeto es un objeto SignerList.                                                                             |
| `OwnerNode`         | String    | UInt64        | Sí       | Una pista que indica qué página del directorio del propietario enlaza a este objeto, en caso de que el directorio consista en varias páginas.                                                           |
| `PreviousTxnID`     | String    | Hash256       | Sí       | El hash identificador de la transacción que más recientemente modificó este objeto.                                                                                                      |
| `PreviousTxnLgrSeq` | Number    | UInt32        | Sí       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que más recientemente modificó este objeto.                                                                      |
| `SignerEntries`     | Array     | Array         | Sí       | Un array de objetos Signer Entry que representan a las partes que forman parte de esta lista de firmantes.                                                                                           |
| `SignerListID`      | Number    | UInt32        | Sí       | Un ID para esta lista de firmantes. Actualmente siempre se establece en `0`. Si una enmienda futura permite múltiples listas de firmantes para una cuenta, esto podría cambiar.                                          |
| `SignerQuorum`      | Number    | UInt32        | Sí       | Un objetivo de número de pesos de firmantes. Para producir una firma válida para el propietario de esta SignerList, los firmantes deben proporcionar firmas válidas cuyos pesos sumen este valor o más. |

Los `SignerEntries` pueden ser cualquier combinación de direcciones financiadas y no financiadas que utilicen claves secp256k1 o ed25519.

#### Objeto Signer Entry

Cada miembro del campo `SignerEntries` es un objeto que describe a ese firmante en la lista. Un Signer Entry tiene los siguientes campos:

| Nombre            | Tipo JSON | Tipo Interno | Descripción                                                                                                                                                                       |
| --------------- | --------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`       | String    | AccountID     | Una dirección Xahau cuya firma contribuye a la firma múltiple. No necesita ser una dirección financiada en el ledger.                                                        |
| `SignerWeight`  | Number    | UInt16        | El peso de una firma de este firmante. Una firma múltiple solo es válida si la suma de pesos de las firmas proporcionadas cumple o excede el valor del `SignerQuorum` de la lista de firmantes. |
| `WalletLocator` | String    | Hash256       | _(Opcional)_ Datos hexadecimales arbitrarios. Esto se puede usar para identificar al firmante o para otros propósitos relacionados. _(Añadido por la \[enmienda ExpandedSignerList]\[])_.             |

Al procesar una transacción con firma múltiple, el servidor busca los valores `Account` con respecto al ledger en el momento de la ejecución de la transacción. Si la dirección _no_ corresponde a un objeto AccountRoot financiado, entonces solo la clave privada maestra asociada con esa dirección puede usarse para producir una firma válida. Si la cuenta _existe_ en el ledger, entonces depende del estado de esa cuenta. Si la cuenta tiene configurada una Clave Regular, se puede usar la Clave Regular. La clave maestra de la cuenta solo puede usarse si no está deshabilitada. Una firma múltiple no se puede usar como parte de otra firma múltiple.

### Flags de \{{currentpage.name\}}

_(Añadido por la \[enmienda MultiSignReserve]\[].)_

Los objetos SignerList pueden tener el siguiente valor de flag:

| Nombre Flag          | Valor Hex    | Valor Decimal | Descripción                                                                                                                                                                                                                                                                                                         |
| ------------------ | ------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lsfOneOwnerCount` | `0x00010000` | 65536         | Si este flag está habilitado, esta SignerList cuenta como un solo elemento para propósitos de la reserva de propietario. De lo contrario, esta lista cuenta como N+2 elementos, donde N es el número de firmantes que contiene. Este flag se habilita automáticamente si añades o actualizas una lista de firmantes después de que se habilite la \[enmienda MultiSignReserve]\[]. |

### Listas de Firmantes y Reservas

Una lista de firmantes contribuye al requisito de reserva de su propietario.

La \[enmienda MultiSignReserve]\[] (habilitada el 17 de abril de 2019) hizo que cada lista de firmantes cuente como un solo objeto, independientemente de cuántos miembros tenga. Como resultado, la reserva de propietario asociada con una nueva lista de firmantes es de 2 XAH.

Una lista de firmantes creada antes de la \[enmienda MultiSignReserve]\[] cuenta como dos objetos, y cada miembro de la lista cuenta como uno. Como resultado, la reserva total de propietario asociada con la lista de firmantes puede ser desde 3 veces hasta 10 veces la reserva requerida por una sola línea de confianza (RippleState) u objeto Offer en el ledger. Para actualizar una lista de firmantes y utilizar la nueva reserva reducida, actualiza la lista de firmantes enviando una \[transacción SignerListSet]\[].

### Formato de ID de SignerList

El ID de un objeto SignerList es el SHA-512Half de los siguientes valores, concatenados en orden:

* La clave de espacio RippleState (`0x0053`)
* El AccountID del propietario de la lista de firmantes
* El `SignerListID` (actualmente siempre `0`)
