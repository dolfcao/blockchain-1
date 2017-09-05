# Bitcoin

## Precis

trusted-third-party model is inadequate
how do we achieve a trustless model?

This is the seminal blockchain paper, can't say much more than that.

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

## Proof of work

This is essentially a voting system. The problem is how to distribute
property rights: if we go with a naive "every participant gets a
vote", we create an incentive to create many fake participants (a
[sybil attack](https://en.wikipedia.org/wiki/Sybil_attack)). This
implies that we need some way of pegging votes to something real: what
BitCoin does is to peg it to computational power.

In detail: the proof of computational power is to find a value that
when hashed with the rest of the block in a specified way creates a
hash with a given number of leading zeroes.

Obviously, given the set of transactions and the nonce, you can check
the required property with a single hash operation and a loop.
Conversely, without insight into the hashing function, there
shouldn't be any faster way of finding a nonce (the special value)
that gives zeros than by simply trying a lot of them. (The paper
suggests incrementing, but it is obviously not mandated, and, indeed,
if you assume that's a common strategy, doing something other than the
normal approach will probabilistically get better results.)

The paper mentions a feedback mechanism by which the number of
required zeros changes depending on how fast blocks are being
generated. It isn't made explicit in the paper, but from
[this answer on quora](https://www.quora.com/How-many-zeros-should-I-require-for-proof-of-work-and-how-should-this-change-through-the-years)
it seems like in practice it's recalculated every 2016 blocks
(new_target = old_target * (time to generate) / (2 weeks)).

There might be some on-chain jostling as the difficulty change comes
through, but as it's all calculated from the set of blocks, eventually
honest nodes should converge (remember, the perceived time as seen by
the node doesn't matter, it's all derived from the data.)

## Network

The nitty-gritty: how do all these blocks make their way around the
network?

1. new transactions are broadcast (hopefully) to all nodes. (Unclear
   whether there's some kind of gossip mechanism here.)
2. each node collects transactions into a block. (This presumably
   means you have some ordering property for transactions within a
   block, as you'd need nodes who received transactions in different
   orders to come up with the same hash)
3. each honest node looks for a PoW for all the blocks it has seen so
   far. (Would be interesting to see if there's any potential speedup
   from looking for hashes of subsets of transactions. Am guessing
   that without flaws in the hashing algo there probably isn't.)
4. Find proof of work => broadcast block+PoW to other nodes (again, gossip? how
   does a node establish that they found it first?)
5. Other nodes check all transactions are valid and not already spent:
   if it would be the currently-longest chain, accept it! Accepting a
   chain in practice means to use it the basis for looking for a new
   proof of work.

If a node misses a block, it will become obvious at some point when
it's referred to by hash in a following block. The paper says it can
request one - it isn't clear exactly who it requests it from. Some
sort of gossip protocol?

## Incentive

Every block+PoW creates a coin owned by the node that found the PoW.
The paper makes the argument that a greedy attacker who owns > 50% of
the cpu power should find it more profitable to just mint new coins
than invalidate transactions that he made - given that there's no
bound on how much money a transaction can represent, this seems an
extremely weak argument. Probably a one-shot thing, but it's not like
that cpu power goes away.

## disk space

structuring the hash as a merkle tree means we don't need to retain
the full blockchain, just as far back as we want to verify directly.

## simplified payment verification

a node that dips in and out of the network needs a different way of
verifying a transaction. they can ask full nodes for their longest
chain until they're satisfied that their transaction is deeply-enough
buried. Individuals can make their own convenience-safety tradeoff
decisions.

## combining and splitting value

one transaction per coin is a bit dumb. instead, a transaction takes
an arbitrary number of inputs, and up to two outputs: one to assign to
the recipient, and an optional one to return change to the sender.
(This is a slightly weird one - not entirely clear to me what this
buys over another transaction sending money back? Atomicity, maybe?)

## Privacy

basically, don't let your identity get tied to the keypair. Not
particularly strong protection. Think there's further work on this.

## Calculations

Proofs! Looks right for the attack model they're chiefly concerned
with - basic exponential argument.
