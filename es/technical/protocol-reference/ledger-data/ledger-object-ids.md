# IDs de Obejtos del Ledger

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/Indexes.cpp)

Cada objeto en los datos de estado de un ledger tiene un ID único. El ID se deriva al realizar un hash de los contenidos importantes del objeto, junto con un [identificador de espacio de nombres](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/LedgerFormats.h#L99). El tipo de objeto del ledger determina qué identificador de espacio de nombres utilizar y qué contenidos incluir en el hash. Esto asegura que cada ID sea único. Para calcular el hash, `xahaud` utiliza SHA-512 y luego trunca el resultado a los primeros 256 bits. Este algoritmo, conocido informalmente como **SHA-512Half**, proporciona una seguridad comparable a SHA-256, pero se ejecuta más rápido en procesadores de 64 bits.

Generalmente, el ID de un objeto del ledger se devuelve como el campo `index` en JSON, al mismo nivel que el contenido del objeto. En los metadatos de la transacción, el ID del objeto del ledger en JSON es `LedgerIndex`.

**Consejo**: El campo `index` o `LedgerIndex` de un objeto en el ledger es el ID del objeto del ledger. Esto no es lo mismo que un \[índice del ledger]\[].

\{{ include\_svg("img/ledger-object-ids.svg", "Diagrama: xahaud utiliza SHA-512Half para generar IDs para los objetos ledger. La clave de espacio evita que los IDs de diferentes tipos de objetos entren en conflicto.") \}}

### Ver también

* Para más información sobre cómo Xahau crea y utiliza hashes, consulta Hashes.
* Para conceptos básicos sobre ledgers, consulta Ledgers.
