---
title: Post-Quantum Cryptocurrency
date: 2025-09-15
tags:
  - blog
  - research
  - technology
  - quantum-computing
  - crypto
---
# A Note from the Future
This post begins with my opinion that quantum computing will damage the integrity of blockchain. However, over the course of the post (and research), I learnt more about how blockchain works, and in turn swayed my opinion.
# Pre-quantum Cryptography is Flawed
Since the early 2000s humanity has predicted that quantum computers will eventually be able to solve cryptographic algorithms faster than all classical computers combined. With more and more advancements in recent years, the media, enterprises, and the public have all started to keep informed with how we will be impacted.
In short, most current pre-quantum cryptography use two large prime numbers to make one larger number. This system protects against classical computers as they are unable to find what two large prime numbers make up the larger number. However, quantum computers are able to solve this efficiently by guessing a bunch of inputs and finding the pattern in the outputs. This can be solved by developing even harder equations that both classical and quantum computers would be infeasible to solve - these algorithms are classified in PQC (Post Quantum Cryptograhy).
# Modern Cryptocurrency
Cryptocurrencies also use the same methods as normal cryptography to sign transactions. This means that there is a magnitude of articles, news feeds, and information regarding how cryptocurrencies will be impacted. As it stands now, cryptocurrencies use blockchain technology for following the trail of money. A bunch of new transactions are added onto the end of the chain and then signed with the whole chain using a magic signature. Meaning, if someone tried to change a transaction in the past, they would have to sign every chain from the point of transaction until the current one.

Anders Brownworth has made a great site to experiment with how blockchain works. I highly recommend checking it out. [Blockchain Demo](https://andersbrownworth.com/blockchain)

# Post-Quantum Cryptography
These signatures rely on the speed at which computers can discover a magic signature that fits the model. At its basic form, the blockchain is `x + y = z`, `x` is the blockchain and transactions, `y` is the magic number, and `z` is the hash. Given that `x` and `z` are static lengths, quantum computers would need a number of qubits equal to the length of `x` and `z`, and anywhere from 8 to 64 qubits for `y`. By entangling the qubits in `y` to convert them via the hashing function to `z`, a result could be made to find a matching magic signature in a single check, instead of traditional computers having to brute force the magic number. 
## Here's where I went wrong
> This is the main problem, since quantum computers can (in theory) break these magic signatures, they will be manipulate the entire transaction history of a blockchain in a short amount of time (1 check per block). Therefore, blockchain will no longer be a trusted source, as anyone will be able to change anyone's balance.

Doing further research, I reached the conclusion that this technique will *not* break blockchain, thanks to the existing trust and connectivity of the blockchain. This can be explained with a simple question: Why can't existing miners change the newest transactions?
Nodes storing the current blockchain do a bunch of trust checks to ensure there are no inconsistencies. After a Node gets transactions or a new signed block, it will store it in memory first. Only after other nodes come back with similar conclusions will the transactions/block be written as the current blockchain. If there are two trusted blockchains, the longer one (by a large margin e.g. 5 blocks) will be chosen and used.
In theory, a quantum computer could still manage to beat the existing blockchain and trick the nodes, but this is unlikely to occur with no one discovering it first, since everything on the blockchain is public, and businesses/people have their own custom tools for inspecting the blockchain, someone somewhere would identify the issue and merge the blockchains back to a trusted state. Although, some people would lose and gain crypto.

While this conclusion isn't as exciting as "Crypto is doomed!", it does raise a question for me to investigate further: Could quantum computers reverse hashes some day?

# See Also
## External Sources
### Quantum Computing
- [(Video) How Does a Quantum Computer Work? - Veritasium | Youtube](https://www.youtube.com/watch?v=g_IaVepNDT4)
- [Blockchain 101 - A Visual Demo - Anders Brownworth | Youtube](https://www.youtube.com/watch?v=_160oMzblY8)
- [How Does Bitcoin Work? | Learn me a bitcoin](https://learnmeabitcoin.com/beginners/how-does-bitcoin-work/)
- [Quantum Computing x Crypto Everything You Need To Know | Presto Labs](https://www.prestolabs.io/research/quantum-computing-x-crypto-everything-you-need-to-know)
- [Cryptocurrency vs. quantum computing | Coin Telegraph](https://cointelegraph.com/learn/articles/cryptocurrency-vs-quantum-computing-a-deep-dive-into-the-future-of-cryptocurrencies)