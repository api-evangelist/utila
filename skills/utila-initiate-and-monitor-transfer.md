---
name: Initiate and monitor a transfer with Utila
description: Estimate, initiate, and track an asset transfer from a Utila MPC
  wallet, handling signing states and replacement/cancellation correctly.
api: openapi/utila-v2-openapi-original.json
operations:
  - Balances_QueryWalletBalances
  - Transactions_EstimateTransactionFee
  - Transactions_InitiateTransaction
  - Transactions_GetTransaction
  - Transactions_CancelTransaction
  - Transactions_ReplaceTransaction
---

# Initiate and monitor a transfer with Utila

Authenticate with a service-account JWT: sign an RS256 JWT with claims
`sub` (service account email), `aud: https://api.utila.io/`, and `exp`
(max 1 hour), then send `Authorization: Bearer <token>` on every call
(see authentication/utila-authentication.yml).

1. Check the source balance with `Balances_QueryWalletBalances`
   (parent `vaults/{vault}/wallets/{wallet}`; `-` wildcards across wallets
   per AIP-159).
2. Estimate cost with `Transactions_EstimateTransactionFee` before
   initiating.
3. Initiate with `Transactions_InitiateTransaction`. Transactions may
   require quorum approval and a designated signer (Co-Signer) before
   they execute.
4. Poll `Transactions_GetTransaction`, or subscribe to the
   `TRANSACTION_CREATED` / `TRANSACTION_STATE_UPDATED` webhooks instead
   of polling (see asyncapi/utila-webhooks.yml).
5. To back out: `Transactions_CancelTransaction` works only BEFORE the
   transaction is signed (error 10004 TRANSACTION_ALREADY_SIGNED
   otherwise). After signing but before mining, use
   `Transactions_ReplaceTransaction` (EVM nonce management, BTC RBF/CPFP;
   TRON does not support replacement — it expires instead).

Errors follow gRPC status semantics with Utila codes
(errors/utila-problem-types.yml): 10001 insufficient funds,
10002 execution reverted, 10003 source account not activated (TRON),
1001/1002 token expired or invalid — regenerate the JWT.
