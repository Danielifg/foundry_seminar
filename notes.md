Foundry Best Practices

- If the contract is big and you want to split it over multiple files, 
group them logically like  MyContract.owner.t.sol, MyContract.deposits.t.sol, etc

- Never make assertions in the setUp function, instead use a dedicated test like test_SetUpState()
- Forge expects the order of assertEq arguments to be assertEq(actual, expected)


Fork Testing RPC caching

- By pinning to a block number, forge caches RPC responses so only the first run is slower, and subsequent runs are significantly faster


Fuzzing 
optimizer_runs = 1000000

- https://github.com/foundry-rs/foundry-toolchain
- Note that if you are fuzzing in your fork tests, the RPC cache strategy above will not work unless you set a fuzz seed. 
- At least one fuzz test per function in the contract
- Remember to write invariant tests! For the assertion string, use a verbose english description of the invariant: assertEq(x + y, z, "Invariant violated: the sum of x and y must always equal z").


Test
- Another use case for this is tracking data that you would not track in production to help test invariants. 
- For example, you might store a list of all token holders to simplify validation of the invariant "sum of all balances must equal total supply". These are often known as "ghost variables".

- Unfortunately there is currently no good way to unit test private methods since they cannot be accessed by any other contracts. Options include:

Converting private functions to internal.
Copy/pasting the logic into your test contract and writing a script that runs in CI check to ensure both functions are identical.


- DeFi
D1 - Check your assumptions about what other contracts do and return.
D2 - Don't mix internal accounting with actual balances.
D3 - Don't use spot price from an AMM as an oracle.
D4 - Do not trade on AMMs without receiving a price target off-chain or via an oracle.
D5 - Use sanity checks to prevent oracle/price manipulation.
D6 - Watch out for rebasing tokens. If they are unsupported, ensure that property is documented.
D7 - Watch out for ERC-777 tokens. Even a token you trust could preform reentrancy if it's an ERC-777.
D8 - Watch out for fee-on-transfer tokens. If they are unsupported, ensure that property is documented.
D9 - Watch out for tokens that use too many or too few decimals. Ensure the max and min supported values are documented.
D10 - Be careful of relying on the raw token balance of a contract to determine earnings. Contracts which provide a way to recover assets sent directly to them can mess up share price functions that rely on the raw Ether or token balances of an address.
D11 - If your contract is a target for token approvals, do not make arbitrary calls from user input.