*** SISC (ShirushiCoin) – Overview

SISC is an ERC-20 compatible token with role-based access control and a built-in mining schedule.
The contract extends standard ERC-20 behavior with administrative mint/burn, multi-transfer utilities, and a daily mining reward mechanism.

*** Key Features
- ERC-20 Core: name(), symbol(), decimals(), totalSupply(), transfer, approve, transferFrom, etc.
- Role-Based Access Control (RBAC):
 + DEFAULT_ADMIN_ROLE – manages privileged settings and roles.
 + PAUSER_ROLE – can pause() / unpause() token transfers.
 + FREEZER_ROLE – can freeze(address) / unfreeze(address) individual accounts.
 + MINER_ROLE – can call mine(year) to mint yearly rewards to a designated pool.

- Administrative Mint/Burn:
 + adminMint(address account, uint256 amount)
 + adminBurn(address account, uint256 amount)

- Mining Schedule:
 + Configurable start year MINING_START_YEAR
 + Reward calculation via getMiningReward(uint256 year)
 + mine(year) mints the reward to poolAccount (set via setPoolAccount)
 + Enforces a minimum interval between mine calls
 + Caps issuance so that totalSupply() never exceeds maxSupply

Operational Controls & Utilities:

pause() / unpause() to halt or resume transfers

freeze(address) / unfreeze(address) to restrict specific accounts

multiTransfer(address[] recipients, uint256[] amounts)

EIP-2612 permit support (gasless approvals)

Events for administration and mining (MineEvent, AccountFrozen, PoolAccountChanged, etc.)

Repository Structure
.
├─ contracts/
│  └─ SISC.sol                 # The main SISC (ShirushiCoin) Solidity contract
├─ src/                        # TypeScript helper scripts (optional, if included)
│  ├─ read.ts                  # Read-only queries (name/symbol/decimals/supply/balances, etc.)
│  ├─ transfer.ts              # Token transfers from a local signer
│  ├─ admin-mint.ts            # Run adminMint (requires DEFAULT_ADMIN_ROLE)
│  ├─ admin-burn.ts            # Run adminBurn (requires DEFAULT_ADMIN_ROLE)
│  ├─ grant-role.ts            # Grant roles (bytes32 or role name → keccak256)
│  ├─ revoke-role.ts           # Revoke roles
│  ├─ has-role.ts              # Check role membership
│  ├─ mine.ts                  # Execute mining for the current year (requires MINER_ROLE)
│  └─ diag-mine.ts             # Diagnostics for mining prechecks
├─ abi/
│  └─ sisc.json                # Compiled ABI (for scripts and verification)
├─ .env.example                # Environment variables template (RPC, PRIVATE_KEY, CONTRACT, etc.)
└─ README.md                   # This file


If you are only publishing Solidity, the src/ and abi/ folders are optional. They are useful when interacting with the contract via Node.js + ethers v6.

Roles & Typical Operations

Grant/Revoke a Role

grantRole(bytes32 role, address account)

revokeRole(bytes32 role, address account)

Role constants may be passed as raw bytes32 or computed via keccak256("ROLE_NAME").
Common roles: DEFAULT_ADMIN_ROLE, PAUSER_ROLE, FREEZER_ROLE, MINER_ROLE.

Pause/Unpause

pause() / unpause() (requires PAUSER_ROLE)

Freeze/Unfreeze Accounts

freeze(address) / unfreeze(address) (requires FREEZER_ROLE)

Mining

Check reward: getMiningReward(year)

Execute: mine(year) (requires MINER_ROLE, respects min interval & maxSupply)

Events (Selected)

MineEvent(address miner, address pool, uint256 amount)

AccountFrozen(address account, bool isFrozen)

PoolAccountChanged(address oldAccount, address newAccount)

Standard ERC-20 events: Transfer, Approval

Access control events: RoleGranted, RoleRevoked, RoleAdminChanged

Security Notes

Administrative functions and mining are protected by RBAC—assign roles carefully.

Consider using a multisig for DEFAULT_ADMIN_ROLE.

Pausing and freezing are powerful controls intended for emergency or compliance workflows.

Always test with a testnet and/or fork before mainnet deployments.

License

Specify your license here (e.g., MIT, Apache-2.0).
