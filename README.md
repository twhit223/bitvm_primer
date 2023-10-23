# BitVM Primer
By Tyler Whittle ([@Dr_DAO_](https://twitter.com/Dr_DAO_))


## Introduction

Welcome to the BitVM primer! If you read the abstract below and get it on the first go, kudos to you. Please reach out to me so I can offer you a job at the [Taproot Wizards](https://taprootwizards.com/). If you’re like the 99% of us who are left simultaneously excited and confused, read on! 

**_Abstract - BitVM: Compute Anything on Bitcoin_**

_BitVM is a computing paradigm to express Turing-complete Bitcoin contracts. This requires no changes to the network’s consensus rules. Rather than executing computations on Bitcoin, they are merely verified, similar to optimistic rollups. A prover makes a claim that a given function evaluates for some particular inputs to some specific output. If that claim is false, then the verifier can perform a succinct fraud proof and punish the prover. Using this mechanism, any computable function can be verified on Bitcoin._

_Committing to a large program in a Taproot address requires significant amounts of off-chain computation and communication, however the resulting on-chain footprint is minimal. As long as both parties collaborate, they can perform arbitrarily complex, stateful off-chain computation, without leaving any trace in the chain. On-chain execution is required only in case of a dispute._

Though it might be hard to distill from the abstract, BitVM marks a paradigm shift in what we thought was possible on Bitcoin. Put simply, the BitVM allows arbitrary computation to be executed on Bitcoin. 

Okay, “arbitrary computation” is a mouthful and doesn’t really help a non-technical reader. So let me give you an example: Chess. 


## Chess, Programs, & Smart Contract Languages

Chess is an example of a well-defined program with set constraints and a win condition. You have certain pieces that can move in certain ways around an 8x8 board. You know the game is over when a king is captured or neither player is able to win. You can consider chess a program with some arbitrary computation (e.g. there is not a chess.move_knight(F4) command built into Bitcoin). 

Now let’s say Vicky and Paul want to play a game of chess against each other. What’s more, they want to make a big bet on it: 1 BTC to the winner! Given this game has such large stakes, they want a way to verify who won. 

Before blockchains, Vicky & Paul’s best bet was to find a trusted third party (let’s call him Terrance) who would observe the game, declare a winner, and custody/pay out the 1 BTC. But what if Terrance wasn’t so trustworthy? Maybe Terrance decides to run away and keep the 1 BTC for himself. Maybe Paul bribed Terrance to declare him the winner no matter what. Neither of these situations (or the myriad of others that could potentially arise) are ideal. 

Enter blockchains! A primary advantage of blockchains is that they shift the need to trust another human to the need to trust cryptography and code. 

So why don’t we see an on-chain chess game on Bitcoin like we [do on Ethereum](https://betterprogramming.pub/on-chain-chess-smart-contract-breakdown-7d01cdaaeb54)[^1]? 

The answer lies in Bitcoin’s underlying language: [Script](https://en.bitcoin.it/wiki/Script). 

## Bitcoin Script

As an analogy, think of Bitcoin/Script like your high-school calculator and Ethereum/Solidity like your iPhone (or Android if you’re like me). Your iPhone can run any program an app developer can cook up. Your calculator, on the other hand, is relegated to some numbers and a few mathematical functions. Maybe there are a few extra buttons for some calculus on there, but no one is mistaking your TI-83 for an iPhone. 

Ethereum’s smart contract language, Solidity, is considered “[Turing complete](https://en.wikipedia.org/wiki/Turing_completeness#:~:text=In%20colloquial%20usage%2C%20the%20terms,purpose%20computer%20or%20computer%20language.)”. While this isn’t strictly true, it’s used in this context to mean Solidity can run pretty much any program imaginable. Chess, a Defi protocol, a zero-knowledge proof verifier - these can all be implemented directly on Ethereum. 

Script, however, is not considered Turing complete. This means it wasn’t possible to run all those cool programs you see on other chains on Bitcoin (until now!). 

 \
If you clicked on the [Script link](https://en.bitcoin.it/wiki/Script) earlier, you’ll have seen a list of these things called opcodes. These are essentially the buttons of the Bitcoin calculator. You’ll see opcodes for simple things like addition (OP_ADD) and cryptographic operations like hashing (OP_SHA256). 

There are ~100 opcodes on Bitcoin, and they are specifically designed to limit the complexity of the computation that can be done. For example, as of writing there is not an opcode to multiply two numbers, nor is there an opcode to add to strings together. Satoshi removed these and many other opcodes very early in Bitcoin’s life to ensure that Script was not Turing complete.


## Bitcoin Nodes & Avoiding Arbitrarily Long Compute 

So why did Satoshi make Bitcoin so restrictive? The answer lies in the economics of Bitcoin. Bitcoin’s security relies on decentralization. It relies on users like you and me to run nodes and VERIFY that the transactions submitted to the network are valid. A full node runs the computation of every transaction in every block on the network. And unlike Ethereum, the fee a user must pay to get their transaction included is only loosely related to the amount of computation required to execute the transaction.[^2] 

Now think what might happen if someone got a transaction included in a block that took 2 hours for a node to run. That would effectively be a DDoS on the Bitcoin network! Nodes are expected to verify a block in a timely manner because miners can’t add a new block until the previous one is verified. By restricting the language of Bitcoin, Satoshi ensured the amount of computation required for a node to verify a block would never spiral out of control. 

Enter BitVM… 

What [Robin](https://twitter.com/robin_linus?lang=en) and the clever team at [ZeroSync](https://zerosync.org/) have figured out is a way to allow those arbitrary programs to be executed on Bitcoin without making every full node run every line in the program. 

BitVM achieves this through some crazy wizardry. 

First it simulates [Boolean logic gates](https://en.wikipedia.org/wiki/Logic_gate) (the building block of computers) within Script. Then, it uses something called hashlocks and the structure of taproot addresses to verify the arbitrary computation. Finally, through an elegant challenge protocol, the Bitcoin network can adjudicate which party (Paul or Vicky in our example) is correct. We’ll be diving deep into how that all works in future installments. Alternatively, [Shinobi’s article](https://bitcoinmagazine.com/technical/the-big-deal-with-bitvm-arbitrary-computation-now-possible-on-bitcoin-without-a-fork) gives a high-level overview for the curious reader. For now, let’s pop back up a level and talk about what this means. 


## What does BitVM enable?

With all that background knowledge out of the way, we’re brought back to BitVM. BitVM has created a way to verify arbitrary computation on Bitcoin. 

Before BitVM, Paul and Vicky couldn’t have played their chess game and had the result verified by Bitcoin. Now they can! 

BitVM will allow them to deposit 0.5 BTC each into a 2/2 multisig address that they both control. If they both agree that Vicky won, they both sign a transaction that sends the 1 BTC to Vicky. Easy peasy! If they disagree, however, there is a way to verify every chess move that happened **on Bitcoin** and **force** Paul to send Vicky the 1 BTC. This is what makes BitVM so special.

While [chess is a cool example](https://github.com/mcbagz/LogicGates/blob/main/Example.ipynb), the design space on BitVM is limitless. It will allow users to verify that almost any program ran correctly all directly on Bitcoin! We’re just a few weeks in, and we’re already seeing strong groups [hacking away on toy programs](https://github.com/supertestnet/tapleaf-circuits) to test out this new system.

As for me, I’m most excited about the potential for BitVM to: a). enable trust-minimized bridges, and b.) verify zero-knowledge proofs. These are two key components of a zero knowledge (zk) rollup. If you aren’t familiar with zk rollups, I recommend [this article](https://mirror.xyz/twhittle.eth/JU2OOxTJO6LLKjd99Dan9lA-z09iOAxrInXn5JHx9wA) where I cover why Bitcoin will ultimately need zk rollups. 

_tl;dr_ zk-rollups are the holy grail of scaling Bitcoin. If BitVM turns Bitcoin from a calculator into an iPhone, then zk rollups will transform that iPhone into a supercomputer. 

Up until now, zk rollups haven’t been built on Bitcoin because there was no way to trustlessly get BTC from the main chain to the rollup, nor to verify zk proofs. With BitVM, we just may be able to do both!

If BitVM can supercharge Bitcoin to enable zk rollups, a new era of fully on-chain Bitcoin applications is about to explode. Defi, DAOs, gaming, etc. will all be coming to Bitcoin. THIS, dear readers, is the promise of BitVM. It should have you stoked to see what comes next!


_This concludes the BitVM Primer. I’m considering making this Part I in a series that walks the non-technical reader through the BitVM (full outline below). If that gets you excited, please let me know in the comments or on [Twitter (@Dr_DAO_)](x.com/Dr_DAO_)_. _I’m always looking for new ways to help the community better understand Bitcoin._

_Example BitVM Walkthrough_



* _Part I: What is BitVM and why should I care? (this article)_
* _Part II: Computers, Programs, and Circuits_
* _Part II: Committing to a Program on Bitcoin_
* _Part IV: The Challenge Protocol_
* _Part V: What challenges does BitVM face?_


Huge shoutouts to [Dazza](https://twitter.com/dazza9x), [English](https://twitter.com/EnglishNFTea), [Isabel](https://twitter.com/isabelfoxenduke), [Jan](https://twitter.com/nonfungible_jan), , & [Super Testnet](https://twitter.com/super_testnet) for their thoughtful comments on this article.

## Notes

[^1]:
    _ It would be simple to extend this chess implementation on Ethereum to pay out a predetermined amount of ETH to the winner. _

[^2]:
     The reason why Ethereum can allow for arbitrary programs is that the gas fee you set for a transaction represents a _limit_. Want to run through 1 million iterations of a “for” loop? No problem! The nodes will get started on that right away. But if you hit your gas limit at iteration 756,203 - the network will just stop executing your program. You still paid all that gas, but you never got a result.
    On Bitcoin, the fee paid to the network is for the _guarantee_ that your transaction will be executed on all nodes. Thus, bitcoin has no way to self-regulate based on how much work you are asking nodes to do. For example, the signature hashing algorithm used on Bitcoin is very inefficient. One surly Bitcoiner decided to submit a transaction that [required 5,569 signatures to be validated](https://mempool.space/tx/bb41a757f405890fb0f5856228e23b715702d714d59bf2b1feb70d8b2b4e3e08), and it took miners over a minute to validate! Segwit was created in part to solve this problem. 
