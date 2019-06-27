# Advanced Smart Contract Security Verification and Exploitation

  * [Preparation](#preparation)
    + [Setting up MythX for Remix](#setting-up-mythx-for-remix)
    + [Installing Mythril](#installing-mythril)
  * [Hands-on Exercises](#hands-on-exercises)
  * [Hacking Contracts on the Mainnet](#hacking-contracts-on-the-mainnet)
  * [Advanced Configuation](#advanced-configuation)
    + [Exercise](#exercise)
    + [The Final Challenge](#the-final-challenge)
  * [What to Do Next](#what-to-do-next)
  * [Credit](#credit)

## Preparation

Here's how to get set up for the workshop. It should be super easy except if you're using Windows.

First, you need a web3 capable browser and some testnet ETH. You probably also have both, but if not, get [Metamask](https://metamask.io) and grab some ETH from the Ropsten faucets:

- https://faucet.metamask.io/
- https://faucet.ropsten.be/

The workshop exercises are hosted in an separate repo. Get a local copy by cloning the repo:

```
$ git clone https://github.com/ConsenSys/mythx-playground/
```

In this workshop you'll get to know both [MythX](https://mythx.io) as well as the latest build of [Mythril](https://github.com/ConsenSys/mythril-classic).

If you run into insurmountable problems ask the instructors for help. There's also a dedicated [Discord channel](https://discord.gg/kGDd8FP) that we created exclusively for you, the valued workshop participant.

### Setting up MythX for Remix

TODO

### Installing Mythril

_Mythril uses solc to compile Solidity files, so you'll need to [install that as well](https://solidity.readthedocs.io/en/latest/installing-solidity.html#binary-packages)_.

[Mythril](https://github.com/ConsenSys/mythril-classic) is a command-line tool for advanced users. It can do a *lot* of stuff, such as analyzing contracts on the blockchain, creating control flow graphs, searching the Ethereum state trie and auto-generating transaction sequences to trigger bugs (plus you can use it to cheat in CTFs).

You can install the latest version from Pypi or Dockerhub (instructions in the [Mythril Wiki](https://github.com/ConsenSys/mythril-classic/wiki/Installation-and-Setup).

**Installing from Pypi**

```
$ pip3 install mythril
$ myth --version
Mythril version v0.21.3
```

**Installing from DockerHub**

```
$ docker pull mythril/myth
$ docker run mythril/myth --version
Mythril version v0.21.3
```

wn_system.md)
- [Build a threat model for Crypto Froggies](threat-modeling/exercise_sample_system.md)

## Hands-on Exercises

For the remainder of the workshop we'll be looking at different ways of identifying and exploiting smart contract vulnerabilities.

## Hacking Contracts on the Mainnet

Note the "SWC" identifier at the end: That's a reference to the [Smart Contract Weakness Classification (EIP 1470)](https://smartcontractsecurity.github.io/SWC-registry/). You can look up details about each issue there.

Now things will get serious! We'll take the attacker's side and exploit this exact contract. Depending on your level of skill, pick one of the following:

- Easy: [Token](https://ethernaut.zeppelin.solutions/level/0x6545df87f57d21cb096a0bfcc53a70464d062512)
- A bit more difficult: [TokenWhale](https://capturetheether.com/challenges/math/token-whale/).

## Advanced Configuation

Mythril can analyze smart contracts from many sources, including bytecode on the blockchain. In [exercise 4](https://github.com/ConsenSys/devcon4-playground/tree/master/exercise4), we'll identify another type of vulnerability that has often been exploited in the past. We'll then show how to detect vulnerable contracts on the mainnet and auto-generate an exploit that extracts the kills the vulnerable contract.

An important aspect is the number of transactions that Mythril (symbolically) executes. The default number is 1, but you can increase it for a more in-depth analysis (note that this increases analysis time significantly). By setting max transactions to 2, you will also detect bugs that are two transactions "deep".

For this example, let's set the max transaction count to 2:

```
$ myth -x exercise4/contracts/Ownable.sol --max-transaction-count 2 --verbose-report (Pypi)

$ docker run -v $(pwd)/exercise4/contracts:/contracts mythril/myth -x /contracts/Ownable.sol --max-transaction-count 2 --verbose-report (Docker)
```

Again, the `--verbose-report` will give you the transaction sequence for triggering the bug - take a good look at the output!

Now the real fun starts: We'll deploy a real-world contract instance with a similar vulnerability. Whoever exploits it first is not only awesome, but also wins 0.1337 ETH for buying Pizza (or hodl). Tip: You can run Mythril Classic against an on-chain contract using the `-a` flag.

Tip: Use `myth -xia <address>` run Mythril against mainnet contracts using INFURA, and don't forget what you learned above :)

- [HACK THIS!](https://etherscan.io/address/0xe80a3d0a70a192dd4fbf9636235da70aa13e7b7d)

### Exercise

Mythril has a few extra tricks up its sleeve. In the [second exercise](https://github.com/ConsenSys/devcon4-playground/tree/master/exercise2), we'll look at a the `Tokensale` contract from [CaptureTheEther](https://capturetheether.com/challenges/math/token-sale/). The source code of that contract is in the `exercise2` directory. The question is, does it have any vulnerabilities? Let's do a quick Mythril Classic run to find out:

```
$ myth -x exercise2/contracts/Tokensale.sol (Pypi)

$ docker run -v $(pwd)/exercise2/contracts:/contracts mythril/myth -x /contracts/Tokensale.sol (Docker)
```

Looks like another integer-related bug! Let's now think about how to best exploit this issue. What we want is to get a lot of tokens without paying a single ETH. A nice trick is to express the exact opposite of what we want as an *invariant* using an `assert()` statement and letting Mythril do the work of finding the solution.

Create a copy of `Tokensale.sol` called `Tokensale-cheat.sol` and add an assertion to the function `buy` as follows:

```
    function buy(uint256 numTokens) public payable {

        require(msg.value == numTokens * PRICE_PER_TOKEN);

        assert(!(msg.value == 0 && numTokens > 0));
```

This essentially means "it should never happen that numTokens is greater than zero if msg.value (the amount of Ether sent) is zero". Obviously, as the attacker that's precisely what we *want* to happen. By adding the `--verbose-report` flag, Mythril will give you the transaction data needed to violate the assertion.

```
$ myth -mexceptions -x exercise2/contracts/Tokensale-cheat.sol --verbose-report (Pypi)

$ docker run -v $(pwd)/exercise2/contracts:/contracts mythril/myth -mexceptions -x /contracts/Tokensale-cheat.sol --verbose-report (Docker)
```

Can you tell why "calldata" in Mythril's output has that particular value?

With the right calldata, you should now able to solve the [Tokensale challenge](https://capturetheether.com/challenges/math/token-sale/).

### The Final Challenge

As a fully certified Mythril expert, you are now capable of solving a more difficult CTF challenge. This challenge is worth another 0.1337 MYTH. This time there won't be any hints ;)

## What to Do Next

A great way to continue learning is playing challenges like [Capture the Ether](https://capturetheether.com), [Security Innovation CTF](https://blockchain-ctf.securityinnovation.com) and [Ethernaut](https://ethernaut.zeppelin.solutions).

## Credit

Created by [ConsenSys Diligence](https://consensys.net/diligence/) and the [Mythril](https://mythril.ai) team. Special thanks to Mick Ayzenberg ([Security Innovation](https://www.securityinnovation.com)), [Trail of Bits](https://www.trailofbits.com), Steve Marx of [CaptureTheEther](https://capturetheether.com) and ConsenSys fame and [Zeppelin Solutions](https://zeppelin.solutions) for vulnerable contract samples and challenges. Also, kudos to the [Truffle](https://truffleframework.com) and [Guardrails](https://www.guardrails.io) teams for working with us on Mythril Platform integration.
