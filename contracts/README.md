# ScoreVault contracts

Solidity contracts for running a private prediction league on-chain: entries held in a league vault, picks locked at kick-off, results resolved through an oracle adapter, and the pot paid out by final ranking. Built with [Foundry](https://book.getfoundry.sh/), targeting Base (Sepolia testnet first).

These contracts are a testnet prototype. The live app runs without them; they are the path to a transparent, non-custodial prize pool.

| Contract | Role |
| --- | --- |
| `LeagueFactory.sol` | Creates leagues (entry amount, max players, join deadline, payout split) and deploys the shared scoring engine |
| `LeagueVault.sol` | Holds each league's entries until payout |
| `PredictionCommitment.sol` | Stores each player's score pick and freezes it at the match's lock time (commit–reveal to hide picks until kick-off is planned, see `docs/`) |
| `ScoreEngine.sol` | Scores each pick against the official result |
| `OracleAdapter.sol` | Receives official match results |
| `PayoutDistributor.sol` | Splits the pot by final ranking |
| `mocks/MockUSDC.sol` | Test token for local and testnet use |

Each contract has a matching test in `test/`.

## Setup

```shell
cd contracts
forge install foundry-rs/forge-std OpenZeppelin/openzeppelin-contracts
forge build
forge test
```

`lib/` is not committed; `forge install` recreates it.

## Deploy to Base Sepolia

```shell
export PRIVATE_KEY=...            # testnet deployer key only
export BASE_SEPOLIA_RPC_URL=...
forge script script/Deploy.s.sol --rpc-url base_sepolia --broadcast
```

`Deploy.s.sol` deploys MockUSDC, the factory and a first demo league, then writes the addresses to `app/lib/contracts.json`. `RegisterMatches.s.sol` registers fixtures and their pick lock times on `PredictionCommitment`.
