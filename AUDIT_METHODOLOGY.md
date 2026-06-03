# Architectural Analysis Methodology: Ether.fi

This document describes the step-by-step process for conducting a security audit of Ether.fi smart contracts.

## Step 1: Identifying Attack Vectors and Entry Points
We began by analyzing `LiquidityPool.sol` to understand how the system accepts ETH and issues LRT tokens.
* **Action:** Search for key deposit functions (`deposit`, `_deposit`).
* **Result:** Use of the `whenNotPaused` modifier was detected, indicating the presence of a centralized kill switch (Circuit Breaker).

## Step 2: Withdrawal Flow Analysis
Next, the withdrawal process was examined, which turned out to be complex and multi-step.
* **Action:** Analysis of the `withdraw` function in `LiquidityPool.sol`.
* **Result:** It was discovered that withdrawals are only allowed for "trusted" contracts (e.g., `PriorityWithdrawalQueue`), preventing direct attacks on the pool's liquidity.

## Step 3: Researching Critical Control Nodes
We moved to `PriorityWithdrawalQueue.sol` to understand how the queuing mechanism works.
* **Action:** Analyze the contract for the presence of "priority" logic.
* **Result:** It was discovered that the queue uses a whitelist system and time delays (`MIN_DELAY`), which protects the protocol from Flash Loan attacks.

## Step 4: Access Rights Audit (RBAC)
The final step is to verify who manages privileged operations.
* **Action:** Analyze the `onlyRequestManager` modifier and the associated `RoleRegistry.sol` contract.
* **Result:** It was determined that the protocol uses the professional `EnumerableRoles` library from Solady and the `Ownable2Step` pattern.
* **Conclusion:** Security of the entire system is delegated to the role registry, making it a single point of failure.

## Toolkit
* **Static analysis:** Using `grep` and `sed` to navigate the codebase.
* **Dependency analysis:** Checking inheritance chains (OpenZeppelin, Solady).
