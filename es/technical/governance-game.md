---
description: >-
  The Governance Game is an innovative governance mechanism within the Xahau
  ecosystem to ensure a community-centric approach towards decision-making.
cover: ../../.gitbook/assets/Blogheader - Xahau Governance.png
coverY: 36
---

# Governance Game

### Visión general

El "Governance Game" de Xahau permite que hasta 400 partes interesadas participen democráticamente en la gestión de la red Xahau a través del "Governance Hook" instalado en la Cuenta Génesis.

### Capa 1

El juego consta de una mesa de "Capa 1" en la que hay 20 asientos. Una cuenta de Xahau (dirección r) puede ocupar cada asiento, o puede estar vacío. Cuando un asiento está ocupado, se dice que un miembro de la mesa está sentado allí.

Para jugar el juego, los miembros de la mesa emiten votos. Los votos se realizan para uno de tres tipos de temas:

* Temas de Asiento
* Temas de Hook
* Temas de Recompensa

Los temas de Asiento son S00 a S19 y representan un voto sobre quién (si alguien) ocupa actualmente ese asiento. Un voto del 80% es suficiente para hacer un cambio. El proceso de votación es continuo, y el voto final que cruza el umbral ejecuta el cambio.

Los temas de Hook son H0 a H9 y representan qué Hooks, incluido el propio Governance Hook, están instalados en la cuenta de la mesa. Estos temas requieren que el 100% de los miembros sentados en esa mesa estén de acuerdo antes de que se pueda realizar un cambio. Esto permite que el Governance Game se actualice y que se agreguen más funciones de la cuenta Génesis con el tiempo.

Los temas de Recompensa son RR y RD, que representan Tasa de Recompensa y Retraso de Recompensa, respectivamente. Estos temas también requieren que el 100% de los miembros de la mesa estén de acuerdo para hacer un cambio. Estos parámetros afectan el sistema de Ajustes de Balance: cuánto puede reclamar cada usuario activo en la red y con qué frecuencia.

### Capa 2

Los miembros que ocupan asientos en la mesa de Capa 1 son cuentas de Xahau (direcciones r). El juego de gobernanza está diseñado para ser estructuralmente recursivo, de modo que una de estas cuentas puede ser en sí misma una mesa que consta de otros 20 asientos. Esto se llama una mesa de Capa 2, y los asientos son asientos de Capa 2.

Dentro de una mesa de Capa 2, existen los mismos temas de votación de asientos y hooks, con las mismas reglas de votación que la mesa de Capa 1. Esto permite que una mesa gobierne su propia membresía y los hooks que se ejecutan allí.

Además de estos temas, una mesa de Capa 2 también puede, mediante un voto del 51%, elevar un voto a la mesa de Capa 1. Este es un voto en nombre de la dirección r en la que existe la mesa de Capa 2 y cuenta como un solo voto en la mesa de Capa 1.

El voto de la mesa de Capa 2 puede caer por debajo del 51%, en cuyo caso el voto originalmente elevado a la mesa de Capa 1 **no** se retira. Solo un nuevo voto (diferente) que alcance el 51% puede cambiar el voto de la mesa en la Capa 1.

Las mesas de Capa 2 solo pueden votar sobre los temas de Recompensa mediante un voto elevado a la Capa 1.

En resumen, los miembros de la Capa 2 pueden votar por:

* Asientos y Hooks para su propia mesa, y
* Asientos, Hooks y temas de Recompensa para la mesa L1 a través del asiento de Capa 1 en el que reside su mesa de Capa 2.

### Restricciones del Juego

* Cualquier mesa puede tener al menos 2 miembros y como máximo 20 miembros.
* Una sola dirección r solo puede ocupar un asiento en una mesa dada, pero puede ocupar un asiento en cada una de varias mesas diferentes.
* El "Governance Game" no está diseñado para recursar más allá de dos capas. No hay ninguna inviabilidad técnica en implementar una mesa de Capa 3, pero el Governance Hook actual no la admite.

### Recompensas para Validadores

Las recompensas para validadores son un incentivo para ejecutar un validador en la red. Las recompensas son generadas por la red y otorgadas a la intersección de los miembros de la Capa 1 y los validadores activos de UNL. Para calificar para las recompensas de validador dentro de un bloque dado de 256 ledgers, lo siguiente debe ser cierto:

* El validador está en la UNL de Xahau.
* El validador valida con éxito a los ojos de otros validadores de UNL.
* Cuando la clave pública maestra del validador se convierte en una dirección r, esa cuenta está sentada en la Mesa de L1.

Las recompensas son ad-hoc y se basan en los Ajustes de Balance de los usuarios de Xahau. Cuando un usuario realiza un Ajuste de Balance, una cantidad igual a su ajuste dividida por 20 se envía a la dirección r de cada uno de los validadores activos que cumplen con los criterios anteriores.

## Especificación Técnica

El Governance Hook está instalado en la cuenta génesis por la enmienda XahauGenesis varios libros mayores después del libro 1 en una nueva red. Esta es la mesa de L1. Para crear una mesa de L2, instala el Hook en una cuenta diferente y luego asigna esa cuenta en la mesa de L1.

### Parámetros del Governance Hook

Cuando se instala el Governance Hook, se instala con un conjunto de HookParameters. Estos especifican la composición inicial de la mesa.

Cada HookParameter tiene un nombre de 3 bytes que consiste en 3 caracteres ASCII o 2 caracteres ASCII y un identificador como se indica a continuación. LE = Little Endian.

```
Parameter Name: {'I', 'R', 'R'}
Parameter Value: Initial Reward Rate <8 byte XFL fraction between 0 and 1, LE>
Parameter Name: {'I', 'R', 'D'}
Parameter Value: Initial Reward Delay <8 byte LE XFL seconds between rewards>
Parameter Name: {'I', 'M', 'C'}
Parameter Value: Initial Member Count <1 byte>
Parameter Name: {'I', 'S', 0x00}
Parameter Value: Initial seat #0's member's 20 byte Account ID.
Parameter Name: {'I', 'S', 0x01}
Parameter Value: Initial seat #1's member's 20 byte Account ID.
... etc ... up to at most Seat 19.

```

Para iniciar el juego, se debe enviar una transacción de Invoke al Hook. Esto puede ser enviado por cualquier cuenta. No se requiere Blob ni HookParameters. Esta transacción de Invoke activa el Hook por primera vez y lo impulsa a crear entradas de estado para cada asiento inicial, tasa de recompensa y retraso de recompensa.

### Estado del Governance Hook

El estado del Governance Hook se almacena en el espacio de nombres cero: `0000000000000000000000000000000000000000000000000000000000000000.`

Hay varios tipos de entrada de estado. Los primeros son lo que se denominan claves de miembro hacia adelante y hacia atrás. Estos mapean cada número de asiento al miembro que se sienta allí y cada miembro al asiento en el que se sienta.

```
Key: 0x0000000000000000000000000000000000000000000000000000000000000005
Val: <20 byte AccountID of the member at seat 5 or all 0's or absent.>

Key: <20 byte AccountID of the member at seat 3>
Val: 0x03
```

Luego, hay algunas entradas de estado únicas. Recuento de Miembros, Tasa de Recompensa y Retraso de Recompensa, respectivamente:

```
Key in Ascii: MC
Key: 0x0000000000000000000000000000000000000000000000000000000000004D43
Val: <1 byte member count (how many seats are occupied)>

Key in Ascii: RR
Key: 0x0000000000000000000000000000000000000000000000000000000000005252
Val: <8 byte LE XFL reward rate (between 0 and 1 (1 being 100%))>

Key in Ascii: RD
Key: 0x0000000000000000000000000000000000000000000000000000000000005244
Val: <8 byte LE XFL reward delay in seconds>
```

Finalmente, los votos y contadores de votos también se almacenan en el estado del Hook. Cuando un asiento emite un voto, se registra en el estado del Hook de la siguiente manera:

```
Vote key is 32 bytes comprising:
    'V' (0x56) - vote
    'H' (0x48) or 'R' (0x52) or 'S' (0x53) - topic type
    'R' (0x52) or 'D' (0x44) or 0 (0x00) to 19 (0x13) - topic detail
     1  (0x01) or  2  (0x02) - target layer for this vote
    0x00 00 00 00 00 00 00 00 - 8 bytes of padding
    20 byte Account ID - the voter
Vote data:
    20 byte Account ID or 8 byte XFL
```

Cuando se emite un voto, incrementa una entrada de estado de contador de votos. Este contador realiza un seguimiento de cuántos votos hay actualmente para este par de tema-datos y permite que el Hook ejecute el voto cuando los votos cruzan el umbral requerido. El estado del contador es el siguiente:

```
Counter key is 32 bytes comprising:
    'C' (0x43) - count
    'H' (0x48) or 'R' (0x52) or 'S' (0x53) - topic type
    'R' (0x52) or 'D' (0x44) or 0 (0x00) to 19 (0x13) - topic detail
     1  (0x01) or  2  (0x02) - target layer for this vote
     0's for padding
     vote data or left-truncated vote data
```

### Transacciones del Governance Hook

El Governance Hook es interactuado por sus miembros usando transacciones ttINVOKE. Además de esto, el Hook también puede emitir sus propias transacciones ttINVOKE si es una mesa de L2 que eleva un voto a la mesa de L1.

Una transacción de voto contiene un array de HookParameters en el nivel superior de la transacción:

```
{
    Account: <member's account>,
    TransactionType: Invoke,
    NetworkID: 21337,
    Destination: <table's account>,
    HookParameters:
    [
        {
            HookParameter:
            {
                HookParameterName: "4C",     // L - the target layer
                HookParameterValue: "01",    // 01 for L1 table, 02 for L2 table
                                             // note: this is the table the vote is
                                             // intended for, not the table you're at
                                             // i.e. for a L2 table you can vote on
                                             // your own membership or on L1's
            }
        },
        {
             HookParameter:
             {
                 HookParameterName: "54",    // T - topic type
                 HookParameterValue: "4801", // H [0x00-0x09] or 
                                             // S [0x00-0x13] or
                                             // RR or RD
             }
        },
        {
             HookParameter:
             {
                 HookParameterName: "56",    // V - vote data
                 HookParameterValue: <32 or 20 or 8 bytes of vote data>
             }
         }
    ]
}
    
```

### Limpiar un Voto

No hay forma de "eliminar un voto" como tal. Puedes cambiar tu voto para reflejar la posición actual.&#x20;

Por ejemplo, si nadie ocupa el asiento 8 y has votado para que la cuenta A se siente allí, y luego cambias de opinión, puedes emitir un voto para vaciar el asiento 8 (aunque ya esté vacío), alineando así tu voto con el estado actual del asiento.&#x20;

Para hacer esto, vota con todos los ceros en los datos del voto, con la misma longitud que normalmente requiere el tema del voto. Así que, para un voto de asiento, esto es 20 bytes de ceros.



