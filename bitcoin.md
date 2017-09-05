# Bitcoin

trusted-third-party model is inadequate
how do we achieve a trustless model?

## Transactions
- a coin comes into existence (somehow) with an owner Alice
- a chain of transactions records the movement of the coin between
  users: Alice passes the coin to Bob on by signing a transaction
  containing Bob's public key with her private key.

This has an obvious problem: there is nothing stopping Alice from
signing transfers to Carol and Doug too. We could get around this by
mandating that all transactions come from or go to Mandy the Mint, but
now we're back to a central trusted third-party. We can do better!

## Timestamp server

- hash a block of items (here, transactions)
- publish hash widely

This proves that the transaction must have happened before the point
in time the hash was published. This establishes a chronology, and we
can start talking meaningfully about at least a partial order of
events.
