# AccountRoot

[\[Source\]](https://github.com/XRPLF/rippled/blob/264280edd79b7f764536e02459f33f66a59c0531/src/ripple/protocol/impl/LedgerFormats.cpp#L36-L60)

An `AccountRoot` ledger entry type describes a single account, its settings, and XAH balance.

### Example JSON

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "AccountTxnID": "0D5FB50FA65C9FE1538FD7E398FFFE9D1908DFA4576D8D7A020040686F93C77D",
    "AccountIndex": "59",
    "Balance": "148446663",
    "Domain": "6D64756F31332E636F6D",
    "EmailHash": "98B4375E1D753E5B91627516F6D70977",
    "Flags": 8388608,
    "LedgerEntryType": "AccountRoot",
    "MessageKey": "0000000000000000000000070000000300",
    "OwnerCount": 3,
    "PreviousTxnID": "0D5FB50FA65C9FE1538FD7E398FFFE9D1908DFA4576D8D7A020040686F93C77D",
    "PreviousTxnLgrSeq": 14091160,
    "Sequence": 336,
    "TransferRate": 1004999999,
    "index": "13F1A95D7AAB7108D5CE7EEAF504B2894B8C674E6D68499076441C4837282BF8"
}
```

### Fields

An `AccountRoot` object has the following fields:

| Field                  | JSON Type | \[Internal Type]\[] | Required? | Description                                                                                                                                                                                                                                                                            |
| ---------------------- | --------- | ------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`              | String    | AccountID           | Yes       | The identifying (classic) address of this account.                                                                                                                                                                                                                                     |
| `Sequence`             | Number    | UInt32              | Yes       | The sequence number of the next valid transaction for this account.                                                                                                                                                                                                                    |
| `Balance`              | String    | Amount              | No        | The account's current \[XAH balance in drops]\[XAH, in drops], represented as a string.                                                                                                                                                                                                |
| `OwnerCount`           | Number    | UInt32              | Yes       | The number of objects this account owns in the ledger, which contributes to its owner reserve.                                                                                                                                                                                         |
| `PreviousTxnID`        | String    | Hash256             | Yes       | The identifying hash of the transaction that most recently modified this object.                                                                                                                                                                                                       |
| `PreviousTxnLgrSeq`    | Number    | UInt32              | Yes       | The \[index of the ledger]\[Ledger Index] that contains the transaction that most recently modified this object.                                                                                                                                                                       |
| `AccountTxnID`         | String    | Hash256             | No        | The identifying hash of the transaction most recently sent by this account. This field must be enabled to use the `AccountTxnID` transaction field. To enable it, send an AccountSet transaction with the `asfAccountTxnID` flag enabled.                                              |
| `RegularKey`           | String    | AccountID           | No        | The address of a key pair that can be used to sign transactions for this account instead of the master key. Use a \[SetRegularKey transaction]\[] to change this value.                                                                                                                |
| `EmailHash`            | String    | Hash128             | No        | The md5 hash of an email address. Clients can use this to look up an avatar through services such as [Gravatar](https://en.gravatar.com/).                                                                                                                                             |
| `WalletLocator`        | String    | Hash256             | No        | An arbitrary 256-bit value that users can set.                                                                                                                                                                                                                                         |
| `WalletSize`           | Number    | UInt32              | No        | Unused. (The code supports this field but there is no way to set it.)                                                                                                                                                                                                                  |
| `MessageKey`           | String    | Blob                | No        | A public key that may be used to send encrypted messages to this account. In JSON, uses hexadecimal. Must be exactly 33 bytes, with the first byte indicating the key type: `0x02` or `0x03` for secp256k1 keys, `0xED` for Ed25519 keys.                                              |
| `TransferRate`         | Number    | UInt32              | No        | A transfer fee to charge other users for sending currency issued by this account to each other.                                                                                                                                                                                        |
| `Domain`               | String    | Blob                | No        | A domain associated with this account. In JSON, this is the hexadecimal for the ASCII representation of the domain. [Cannot be more than 256 bytes in length.](https://github.com/xrplf/rippled/blob/55dc7a252e08a0b02cd5aa39e9b4777af3eafe77/src/ripple/app/tx/impl/SetAccount.h#L34) |
| `TickSize`             | Number    | UInt8               | No        | How many significant digits to use for exchange rates of Offers involving currencies issued by this address. Valid values are `3` to `15`, inclusive. _(Added by the \[TickSize amendment]\[].)_                                                                                       |
| `TicketCount`          | Number    | UInt32              | No        | How many Tickets this account owns in the ledger. This is updated automatically to ensure that the account stays within the hard limit of 250 Tickets at a time. This field is omitted if the account has zero Tickets. _(Added by the \[TicketBatch amendment]\[].)_                  |
| `NFTokenMinter`        | String    | AccountID           | No        | Another account that can mint non-fungible tokens on behalf of this account. _(Added by the \[NonFungibleTokensV1\_1 amendment]\[])_                                                                                                                                                   |
| `MintedNFTokens`       | Number    | UInt32              | No        | How many total non-fungible tokens have been minted by and on behalf of this account. _(Added by the \[NonFungibleTokensV1\_1 amendment]\[])_                                                                                                                                          |
| `BurnedNFTokens`       | Number    | UInt32              | No        | How many total of this account's issued non-fungible tokens have been burned. This number is always equal or less than `MintedNFTokens`.                                                                                                                                               |
| `HookStateCount`       | Number    | UInt32              | No        | The number of states on the Hook.                                                                                                                                                                                                                                                      |
| `HookNamespaces`       | Array     | Vector256           | No        | A unique value to prevent state conflicts between multiple Hooks installed on the same XAHL account.                                                                                                                                                                                   |
| `RewardLgrFirst`       | Number    | UInt32              | No        | The ledger number of the last ClaimReward transaction.                                                                                                                                                                                                                                 |
| `RewardLgrLast`        | Number    | UInt32              | No        | The ledger sequence number of the last transaction in or out of your account.                                                                                                                                                                                                          |
| `RewardTime`           | Number    | UInt32              | No        | The ledger time the last reward was claimed.                                                                                                                                                                                                                                           |
| `RewardAccumulator`    | String    | UInt64              | No        | The area under of the balance-time graph for your account since last ClaimReward transaction.                                                                                                                                                                                          |
| `FirstNFTokenSequence` | Number    | UInt32              | No        | The account's \[Sequence Number]\[] at the time it minted its first non-fungible-token. _(Added by the \[fixNFTokenRemint amendment]\[] :not\_enabled:)_                                                                                                                               |
| `ImportSequence`       | Number    | UInt32              | No        | The current sequence number from the importing account on the XAHL Ledger.                                                                                                                                                                                                             |
| `GovernanceFlags`      | String    | Hash256             | No        |                                                                                                                                                                                                                                                                                        |
| `GovernanceMarks`      | String    | Hash256             | No        |                                                                                                                                                                                                                                                                                        |
| `AccountIndex`         | String    | UInt64              | No        | The account's \[Index Number]\[]. _(Added by the \[xahauGenesis amendment]\[] :enabled:)_                                                                                                                                                                                              |

### AccountRoot Flags

There are several options which can be either enabled or disabled for an account. These options can be changed with an \[AccountSet transaction]\[]. In the ledger, flags are represented as binary values that can be combined with bitwise-or operations. The bit values for the flags in the ledger are different than the values used to enable or disable those flags in a transaction. Ledger flags have names that begin with **`lsf`**.

fAccountRoot objects can have the following flag values:

| Flag Name                         | Hex Value    | Decimal Value | Corresponding AccountSet Flag     | Description                                                                                                                                                                       |
| --------------------------------- | ------------ | ------------- | --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lsfDefaultRipple`                | `0x00800000` | 8388608       | `asfDefaultRipple`                | Enable rippling on this addresses's trust lines by default. Required for issuing addresses; discouraged for others.                                                               |
| `lsfDepositAuth`                  | `0x01000000` | 16777216      | `asfDepositAuth`                  | This account has DepositAuth enabled, meaning it can only receive funds from transactions it sends, and from preauthorized accounts. _(Added by the \[DepositAuth amendment]\[])_ |
| `lsfDisableMaster`                | `0x00100000` | 1048576       | `asfDisableMaster`                | Disallows use of the master key to sign transactions for this account.                                                                                                            |
| `lsfDisallowIncomingCheck`        | `0x08000000` | 134217728     | `asfDisallowIncomingCheck`        | This account blocks incoming Checks. _(Requires the \[DisallowIncoming amendment]\[] :not\_enabled:.)_                                                                            |
| `lsfDisallowIncomingNFTokenOffer` | `0x04000000` | 67108864      | `asfDisallowIncomingNFTokenOffer` | This account blocks incoming NFTokenOffers. _(Requires the \[DisallowIncoming amendment]\[] :not\_enabled:.)_                                                                     |
| `lsfDisallowIncomingPayChan`      | `0x10000000` | 268435456     | `asfDisallowIncomingPayChan`      | This account blocks incoming Payment Channels. _(Requires the \[DisallowIncoming amendment]\[] :not\_enabled:.)_                                                                  |
| `lsfDisallowIncomingTrustline`    | `0x20000000` | 536870912     | `asfDisallowIncomingTrustline`    | This account blocks incoming trust lines. _(Requires the \[DisallowIncoming amendment]\[] :not\_enabled:.)_                                                                       |
| `lsfDisallowIncomingRemit`        | `0x80000000` | 2147483648    | `asfDisallowIncomingRemit`        | This account blocks incoming remit transactions. _(Requires the \[DisallowIncoming amendment]\[] :not\_enabled:.)_                                                                |
| `lsfDisallowXRP`                  | `0x00080000` | 524288        | `asfDisallowXRP`                  | Client applications should not send XAH to this account. (Advisory; not enforced by the protocol.)                                                                                |
| `lsfGlobalFreeze`                 | `0x00400000` | 4194304       | `asfGlobalFreeze`                 | All assets issued by this account are frozen.                                                                                                                                     |
| `lsfNoFreeze`                     | `0x00200000` | 2097152       | `asfNoFreeze`                     | This account cannot freeze trust lines connected to it. Once enabled, cannot be disabled.                                                                                         |
| `lsfPasswordSpent`                | `0x00010000` | 65536         | (None)                            | This account has used its free SetRegularKey transaction.                                                                                                                         |
| `lsfRequireAuth`                  | `0x00040000` | 262144        | `asfRequireAuth`                  | This account must individually approve other users for those users to hold this account's tokens.                                                                                 |
| `lsfRequireDestTag`               | `0x00020000` | 131072        | `asfRequireDest`                  | Requires incoming payments to specify a Destination Tag.                                                                                                                          |
| `lsfTshCollect`                   | `0x02000000` | 33554432      | `asfTshCollect`                   | The TSH pays for the execution of their own Hook Chain.                                                                                                                           |
| `lsfURITokenIssuer`               | `0x04000000` | 67108864      | `asfURITokenIssuer`               | This account cannot be deleted after it has issued a URIToken.                                                                                                                    |

### AccountRoot ID Format

The ID of an AccountRoot object is the \[SHA-512Half]\[] of the following values, concatenated in order:

* The Account space key (`0x0061`)
* The AccountID of the account
