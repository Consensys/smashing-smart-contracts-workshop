# Smashing Smart Contracts: Advanced Vulnerability Detection and Exploitation

## Preparation

Here's how to get set up for the workshop. It should be super easy except if you're using Windows.

If you run into insurmountable problems ask the instructors for help. There's also a dedicated [Discord channel](https://discord.gg/kGDd8FP) that we created exclusively for you, the valued workshop participant.

### Getting Testnet ETH

First, you need a web3 capable browser and some testnet ETH. You probably also have both, but if not, get [Metamask](https://metamask.io) and grab some ETH from the Ropsten faucets:

- https://faucet.metamask.io/
- https://faucet.ropsten.be/

### Running a Geth Light Node

TODO

### Installing Mythril

_Mythril uses solc to compile Solidity files, so you'll need to [install that as well](https://solidity.readthedocs.io/en/latest/installing-solidity.html#binary-packages)_.

[Mythril](https://github.com/ConsenSys/mythril-classic) is a command-line tool for advanced users. It can do a *lot* of stuff, such as analyzing contracts on the blockchain, creating control flow graphs, searching the Ethereum state trie and auto-generating transaction sequences to trigger bugs (plus you can use it to cheat in CTFs).

You can install the latest version from Pypi or Dockerhub (instructions in the [Mythril Wiki](https://github.com/ConsenSys/mythril-classic/wiki/Installation-and-Setup).

**Installing from Pypi**

```
$ pip3 install mythril
$ myth version
Mythril version v0.21.12
```

**Installing from DockerHub**

```
$ docker pull mythril/myth
$ docker run mythril/myth version
Mythril version v0.21.12
```

### Installing Scrooge McEtherface

TODO

## Mythril Guide and Exercises

TODO

### Analyzing Contracts on the Blockchain

Now things will get serious! We'll take the attacker's side and exploit this exact contract. Depending on your level of skill, pick one of the following:

- Easy: [Token](https://ethernaut.zeppelin.solutions/level/0x6545df87f57d21cb096a0bfcc53a70464d062512)
- A bit more difficult: [TokenWhale](https://capturetheether.com/challenges/math/token-whale/).

## Advanced Configuration

Mythril can analyze smart contracts from many sources, including bytecode on the blockchain. In [exercise 4](https://github.com/ConsenSys/devcon4-playground/tree/master/exercise4), we'll identify another type of vulnerability that has often been exploited in the past. We'll then show how to detect vulnerable contracts on the mainnet and auto-generate an exploit that extracts the kills the vulnerable contract.

An important aspect is the number of transactions that Mythril (symbolically) executes. The default number is 1, but you can increase it for a more in-depth analysis (note that this increases analysis time significantly). By setting max transactions to 2, you will also detect bugs that are two transactions "deep".

For this example, let's set the max transaction count to 2:

```
$ myth analyze exercise4/contracts/Ownable.sol -t 2
```

### 

Mythril has a few extra tricks up its sleeve. In the [second exercise](https://github.com/ConsenSys/devcon4-playground/tree/master/exercise2), we'll look at a the `Tokensale` contract from [CaptureTheEther](https://capturetheether.com/challenges/math/token-sale/). The source code of that contract is in the `exercise2` directory. The question is, does it have any vulnerabilities? Let's do a quick Mythril Classic run to find out:

```
$ myth analyze exercise2/contracts/Tokensale.sol (Pypi)
```

Can you tell why "calldata" in Mythril's output has that particular value?

With the right calldata, you should now able to solve the [Tokensale challenge](https://capturetheether.com/challenges/math/token-sale/).

## Using Scrooge




## What to Do Next

A great way to continue learning is playing challenges like [Capture the Ether](https://capturetheether.com), [Security Innovation CTF](https://blockchain-ctf.securityinnovation.com) and [Ethernaut](https://ethernaut.zeppelin.solutions).

## Credit

Created by [ConsenSys Diligence](https://consensys.net/diligence/) and the [Mythril](https://mythril.ai) team. Special thanks to Mick Ayzenberg ([Security Innovation](https://www.securityinnovation.com)), [Trail of Bits](https://www.trailofbits.com), Steve Marx of [CaptureTheEther](https://capturetheether.com) and ConsenSys fame and [Zeppelin Solutions](https://zeppelin.solutions) for vulnerable contract samples and challenges. Also, kudos to the [Truffle](https://truffleframework.com) and [Guardrails](https://www.guardrails.io) teams for working with us on Mythril Platform integration.
