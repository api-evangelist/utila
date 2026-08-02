---
name: Provision a wallet and deposit address with Utila
description: Create an MPC wallet in a Utila vault, generate a chain-specific
  deposit address, and watch for incoming deposits.
api: openapi/utila-v2-openapi-original.json
operations:
  - Vaults_ListVaults
  - Wallets_CreateWallet
  - Blockchains_ListNetworks
  - Wallets_CreateWalletAddress
  - Balances_QueryWalletBalances
---

# Provision a wallet and deposit address with Utila

Authenticate with a service-account JWT bearer token
(authentication/utila-authentication.yml). The service account must be a
member of the target vault (admin quorum approval).

1. Find the vault with `Vaults_ListVaults`; resource names look like
   `vaults/3bf247bc8ee2c`.
2. Create the wallet with `Wallets_CreateWallet` under that vault.
3. Pick a network via `Blockchains_ListNetworks` (custom EVM networks
   come from `Blockchains_ListVaultNetworks`).
4. Generate a deposit address with `Wallets_CreateWalletAddress`. The
   address derives from the appropriate MPC key (ECDSA for
   Bitcoin/Ethereum, EdDSA for Solana). EVM-family addresses in the same
   wallet share one address across chains; multiple addresses per wallet
   are only supported on UTXO chains.
5. Confirm funds with `Balances_QueryWalletBalances`, or register the
   `WALLET_ADDRESS_CREATED` and `TRANSACTION_CREATED` webhooks
   (asyncapi/utila-webhooks.yml) instead of polling.
