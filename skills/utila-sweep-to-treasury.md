---
name: Sweep token balances to treasury with Utila
description: Consolidate a specific asset from many source wallets into a
  single treasury wallet using vault-wide balance queries and sponsored
  transfers.
api: openapi/utila-v2-openapi-original.json
operations:
  - Balances_QueryBalances
  - Balances_QueryWalletBalances
  - Transactions_EstimateTransactionFee
  - Transactions_InitiateTransaction
  - Transactions_GetTransaction
---

# Sweep token balances to treasury with Utila

Grounded in Utila's own sweeping guide
(https://docs.utila.io/reference/how-to-sweep-token-balances-to-treasury)
— the common pattern for treasury consolidation, gas cost reduction, and
omnibus architecture.

1. Query balances across the vault with `Balances_QueryBalances`, or use
   `Balances_QueryWalletBalances` with the `-` wildcard parent (AIP-159)
   to enumerate the asset across all wallets.
2. Filter eligible source wallets (minimum balance thresholds; skip the
   treasury destination itself). List/query methods support filtering
   (https://docs.utila.io/reference/filtering).
3. For each source wallet, estimate with
   `Transactions_EstimateTransactionFee`, then initiate a transfer with
   `Transactions_InitiateTransaction` to the treasury wallet. Use
   sponsored transfers (https://docs.utila.io/reference/sponsored-transfer)
   to offload gas fees to a designated fee-payer wallet so source wallets
   need no native gas balance.
4. Track each sweep with `Transactions_GetTransaction` or the
   `TRANSACTION_STATE_UPDATED` webhook until CONFIRMED.

Watch for error 10001 TRANSACTION_INSUFFICIENT_FUNDS on dust balances and
10003 on unactivated TRON source accounts
(errors/utila-problem-types.yml).
