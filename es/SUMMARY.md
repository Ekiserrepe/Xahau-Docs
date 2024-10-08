# Tabla de contenidos

* [🪝 Documentación de Xahau](README.md)
  * [¿Qué es diferente?](readme/what-is-different.md)
* [🪝 Hooks](readme-1.md)

## Características

* [Nodos Públicos (RPC)](features/public-nodes-rpc.md)
* [Enmiendas](features/amendments.md)
* [Firma de transacciones](features/transaction-signing/README.md)
  * [Tasas de transacción](features/transaction-signing/transaction-fees.md)
* [Herramientas para desarrolladores](features/developer-tooling/README.md)
  * [Herramientas seleccionadas](features/developer-tooling/hook-tools.md)
  * [Librerías cliente](features/developer-tooling/client-libraries.md)
  * [Trucos de desarrollador](features/developer-tooling/developer-tricks.md)
* [Características de la red](features/network-features/README.md)
  * [Gestión de cuentas](features/network-features/account-managment.md)
  * [Recompensas de balance](features/network-features/balance-rewards.md)
  * [Cheques](features/network-features/check.md)
  * [Escrow](features/network-features/escrow.md)
  * [Hooks](features/network-features/hooks.md)
  * [Ofertas](features/network-features/offer.md)
  * [Pagos](features/network-features/payments.md)
  * [URIToken](features/network-features/uritoken.md)
* [Grifo & Exploradores](features/faucet-and-explorer.md)
* [API de datos](features/data-apis.md)

## Infraestructura

* [Requisitos para nodos](infrastructure/node-requirements.md)
* [Ejecutando un nodo](infrastructure/peering/README.md)
  * [Ejecutando un nodo en Mainnet](infrastructure/peering/connect-to-xahau-mainnet.md)
  * [Ejecutando un nodo en Testnet](infrastructure/peering/peering-your-own-node.md)
* [Contruyendo Xahau (Dev)](infrastructure/building-xahau/README.md)
  * [Ubuntu - 22.04](infrastructure/building-xahau/ubuntu-22.04.md)
  * [Mac OS - 13.5.2](infrastructure/building-xahau/mac-os-13.5.2.md)

## Técnico

* [Referencia al protocolo](technical/protocol-reference/README.md)
  * [Transacciones](technical/protocol-reference/transactions/README.md)
    * [Campos comunes de transacción](technical/protocol-reference/transactions/transaction-common-fields.md)
    * [Tipos de transacciones](technical/protocol-reference/transactions/transaction-types/README.md)
      * [AccountDelete](technical/protocol-reference/transactions/transaction-types/accountdelete.md)
      * [AccountSet](technical/protocol-reference/transactions/transaction-types/accountset.md)
      * [CheckCancel](technical/protocol-reference/transactions/transaction-types/checkcancel.md)
      * [CheckCash](technical/protocol-reference/transactions/transaction-types/checkcash.md)
      * [CheckCreate](technical/protocol-reference/transactions/transaction-types/checkcreate.md)
      * [ClaimReward](technical/protocol-reference/transactions/transaction-types/claimreward.md)
      * [DepositPreauth](technical/protocol-reference/transactions/transaction-types/depositpreauth.md)
      * [EscrowCancel](technical/protocol-reference/transactions/transaction-types/escrowcancel.md)
      * [EscrowCreate](technical/protocol-reference/transactions/transaction-types/escrowcreate.md)
      * [EscrowFinish](technical/protocol-reference/transactions/transaction-types/escrowfinish.md)
      * [GenesisMint - (Tx emitida)](technical/protocol-reference/transactions/transaction-types/genesismint-emitted-txn.md)
      * [Import](technical/protocol-reference/transactions/transaction-types/import.md)
      * [Invoke](technical/protocol-reference/transactions/transaction-types/invoke.md)
      * [OfferCancel](technical/protocol-reference/transactions/transaction-types/offercancel.md)
      * [OfferCreate](technical/protocol-reference/transactions/transaction-types/offercreate.md)
      * [Payment](technical/protocol-reference/transactions/transaction-types/payment.md)
      * [PaymentChannelClaim](technical/protocol-reference/transactions/transaction-types/paymentchannelclaim.md)
      * [PaymentChannelCreate](technical/protocol-reference/transactions/transaction-types/paymentchannelcreate.md)
      * [PaymentChannelFund](technical/protocol-reference/transactions/transaction-types/paymentchannelfund.md)
      * [Remit](technical/protocol-reference/transactions/transaction-types/remit.md)
      * [SetHook](technical/protocol-reference/transactions/transaction-types/sethook.md)
      * [SetRegularKey](technical/protocol-reference/transactions/transaction-types/setregularkey.md)
      * [SignerListSet](technical/protocol-reference/transactions/transaction-types/signerlistset.md)
      * [TicketCreate](technical/protocol-reference/transactions/transaction-types/ticketcreate.md)
      * [TrustSet](technical/protocol-reference/transactions/transaction-types/trustset.md)
      * [URITokenBurn](technical/protocol-reference/transactions/transaction-types/uritokenburn.md)
      * [URITokenBuy](technical/protocol-reference/transactions/transaction-types/uritokenbuy.md)
      * [URITokenCancelSellOffer](technical/protocol-reference/transactions/transaction-types/uritokencancelselloffer.md)
      * [URITokenCreateSellOffer](technical/protocol-reference/transactions/transaction-types/uritokencreateselloffer.md)
      * [URITokenMint](technical/protocol-reference/transactions/transaction-types/uritokenmint.md)
    * [Tipos de pseudo transacciones](technical/protocol-reference/transactions/pseudo-transaction-types/README.md)
      * [EmitFailure](technical/protocol-reference/transactions/pseudo-transaction-types/emitfailure.md)
      * [UNLReport](technical/protocol-reference/transactions/pseudo-transaction-types/unlreport.md)
      * [UNLModify](technical/protocol-reference/transactions/pseudo-transaction-types/unlmodify.md)
      * [SetFee](technical/protocol-reference/transactions/pseudo-transaction-types/setfee.md)
      * [EnableAmendment](technical/protocol-reference/transactions/pseudo-transaction-types/enableamendment.md)
    * [Transaction Results](technical/protocol-reference/transactions/transaction-results/README.md)
      * [TES Codes](technical/protocol-reference/transactions/transaction-results/tes-codes.md)
      * [TER Codes](technical/protocol-reference/transactions/transaction-results/ter-codes.md)
      * [TEM Codes](technical/protocol-reference/transactions/transaction-results/tem-codes.md)
      * [TEL Codes](technical/protocol-reference/transactions/transaction-results/tel-codes.md)
      * [TEF Codes](technical/protocol-reference/transactions/transaction-results/tef-codes.md)
      * [TEC Codes](technical/protocol-reference/transactions/transaction-results/tec-codes.md)
    * [Transaction Metadata](technical/protocol-reference/transactions/transaction-metadata.md)
  * [Ledger Data](technical/protocol-reference/ledger-data/README.md)
    * [Ledger Objects Types](technical/protocol-reference/ledger-data/ledger-objects-types/README.md)
      * [AccountRoot](technical/protocol-reference/ledger-data/ledger-objects-types/accountroot.md)
      * [Amendments](technical/protocol-reference/ledger-data/ledger-objects-types/amendments.md)
      * [Check](technical/protocol-reference/ledger-data/ledger-objects-types/check.md)
      * [Deposit Pre Auth](technical/protocol-reference/ledger-data/ledger-objects-types/deposit-pre-auth.md)
      * [Directory Node](technical/protocol-reference/ledger-data/ledger-objects-types/directory-node.md)
      * [Emitted Txn](technical/protocol-reference/ledger-data/ledger-objects-types/emitted-txn.md)
      * [Escrow](technical/protocol-reference/ledger-data/ledger-objects-types/escrow.md)
      * [Fee Settings](technical/protocol-reference/ledger-data/ledger-objects-types/fee-settings.md)
      * [Hook](technical/protocol-reference/ledger-data/ledger-objects-types/hook.md)
      * [Hook Definition](technical/protocol-reference/ledger-data/ledger-objects-types/hook-definition.md)
      * [Hook State](technical/protocol-reference/ledger-data/ledger-objects-types/hook-state.md)
      * [Import VL Sequence](technical/protocol-reference/ledger-data/ledger-objects-types/import-vl-sequence.md)
      * [Ledger Hashes](technical/protocol-reference/ledger-data/ledger-objects-types/ledger-hashes.md)
      * [Negative UNL](technical/protocol-reference/ledger-data/ledger-objects-types/negative-unl.md)
      * [Offer](technical/protocol-reference/ledger-data/ledger-objects-types/offer.md)
      * [Pay Channel](technical/protocol-reference/ledger-data/ledger-objects-types/pay-channel.md)
      * [Ripple State](technical/protocol-reference/ledger-data/ledger-objects-types/ripple-state.md)
      * [Signers List](technical/protocol-reference/ledger-data/ledger-objects-types/signers-list.md)
      * [Ticket](technical/protocol-reference/ledger-data/ledger-objects-types/ticket.md)
      * [UNL Report](technical/protocol-reference/ledger-data/ledger-objects-types/unl-report.md)
      * [URIToken](technical/protocol-reference/ledger-data/ledger-objects-types/uritoken.md)
    * [Ledger Header](technical/protocol-reference/ledger-data/ledger-header.md)
    * [Ledger Object IDs](technical/protocol-reference/ledger-data/ledger-object-ids.md)
  * [Data Types](technical/protocol-reference/data-types/README.md)
    * [Currency Formats](technical/protocol-reference/data-types/currency-formats.md)
    * [Base 58 Encodings](technical/protocol-reference/data-types/base-58-encodings.md)
  * [Binary Format](technical/protocol-reference/binary-format.md)
* [Balance Adjustments](technical/balance-adjustments.md)
* [Governance Game](technical/governance-game.md)
* [Burn 2 Mint (B2M)](technical/burn-2-mint.md)
* [Versioning Process](technical/versioning-process.md)

## Compliance

* [Security Audits](compliance/security-audit/README.md)
  * [Responsible Disclosure](compliance/security-audit/responsible-disclosure.md)

## Support

* [Help Us](support/help-us.md)
