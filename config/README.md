# Delpho relayer configs

`config.dev.hyperEvm.json` and `config.prod.hyperEvm.json` are loaded on every relayer start (`RESET_STORAGE_ON_START=true`). A push to `dev` deploys the dev file; a push to `prod` deploys the prod file.

`policies.whitelist_receivers` is the only check on where a signer may send: the relayer compares the transaction's `to` (lowercased) and never reads calldata. Signing typed data (HyperCore actions such as `evmUserModify`) skips it.

## Liquidator signers (`liquidator_bot_hyper_evm_*`)

Every liquidator signer in an environment has the same list. Addresses come from `delpho_severless_ecs`.

| Receiver | dev | prod | Used by |
|---|---|---|---|
| CDP manager (USDC market) | `0xcab521444968cd7f326c7340999cb244b687894c` | `0x41346cfc861ac683c6f575ff041a9a5aa36b4b9b` | liquidations |
| CDP manager (WHYPE market) | `0x5608458e3dbd0875b02150e4a5e09e2bd7854646` | `0xf296cc9c08a92e86b1c3a04b10ccf711d996ea50` | liquidations |
| Delpho router (`DELPHO_ROUTER`) | `0x572d924566715d726a70a510bb8b889e69c1b9cc` | `0xe8ec5ae94e8efa68914000b528eb7b7a6470384d` | reward sweep: withdrawal request, collateral claim |
| USDV (`DELPHO_STABLE`) | `0xd4a48d5840ff0a80ed555d3fdc3c72cfaa5489b3` | `0x8c6eb3c8d1fddc752684274fb4a3eb98dbe9cd26` | reward sweep |
| WHYPE | `0x5555555555555555555555555555555555555555` | same | reward sweep |
| kHYPE | `0xfd739d4e423301ce9385c1fb8850539d657c296d` | same | reward sweep |
| USDC | `0xb88339cb7199b77e23db6e890353e22632ba630f` | same | reward sweep |
| USDT0 | `0xb8ce59fc3717ada4c02eadf9682a9e934f625ebb` | same | reward sweep |

Sources: `shared/src/environments/{dev,prod}.ts`, `cdp-management/src/liquidation/liquidationSweep.ts` (`stepTarget`) and `cdp-management/src/liquidation/liquidationReward.ts` (`LIQUIDATION_REWARD_TOKENS`).

When the backend sends a liquidator transaction to a new contract, add it to every liquidator signer in both files and to this table.
