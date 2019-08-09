<p align="center">
	<img src="/static/smashing.png" height="300px"/>
</p>

# Smashing Smart Contracts: Advanced Vulnerability Detection and Exploitation

In this workshop we'll practice hacking & exploitation of Solidity/EVM smart contracts. During the workshop we'll look at  real-world contracts, play challenges from [CaptureTheEther](https://capturetheether.com) and [OpenZeppelin Ethernaut](https://ethernaut.openzeppelin.com) and tackle some reverse engineering challenges. You'll find the source code for most challenges in [here](https://github.com/ConsenSys/mythx-playground).

## Preparation

Here's how to get set up for the workshop. It should be super easy except if you're using Windows (you need Node.js and a Python 3 environment, preferably set up a separate [Virtualenv](https://virtualenv.pypa.io/en/latest/).

If you run into insurmountable problems ask the instructors for help. There's also a dedicated [Discord channel](https://discord.gg/kGDd8FP) that we created exclusively for you, the valued workshop participant.

### Installing Ganache

[Ganache](https://www.trufflesuite.com/ganache) is a local Ethereum development server. Install it with npm:

```console
$ npm i -g ganache-cli
```

### Installing Mythril (Vulnerability Detection)

Install [Mythril](https://github.com/ConsenSys/mythril) from Pypi:

```bash
$ pip3 install mythril
```

If you can't get this to work you can use the Docker image instead (see [docs](https://mythril-classic.readthedocs.io/en/master/installation.html), note however that the below tools won't work then.

### Installing Scrooge McEtherface (Exploit Automation)

Scrooge doesn't have a Pypi package. Clone the Github repo to install the required packages into a Python 3 environment:

```
$ git clone https://github.com/b-mueller/scrooge-mcetherface
$ cd scrooge-mcetherface
$ pip3 install -r requirements.txt
```

### Installing Theo (Frontrunning)

Theo has a PyPI package:
```console
$ pip install --user theo --upgrade
```

Or you can just clone the repo and install it:
```console
$ git clone https://github.com/cleanunicorn/theo
$ cd theo
$ pip install -e .
```

## Workshop Content and Exercises

### Level 1: Accidentally Killin'

In the first example we'll focus on killing smart contracts as well as stealing Ether from them. Here are a few hints:

```
$ myth analyze <codefile.sol>
```

For analysis of on-chain contracts use the `--rpc` and `-a` options.

```
$ myth analyze --rpc localhost:8545 -a <address>
$ myth analyze --rpc infura-ropsten -a <address>
```

To run Scrooge McEtherface, set up Scrooge's `config.ini`, then run:

```
$ ./scrooge <address>
```

**Target Contracts:**

- [SimpleWalletLibrary](https://github.com/ConsenSys/mythx-playground/blob/master/level1/SimpleWalletLibrary.sol)
- [Ethernaut Fallout](./code/Fallout.sol) - [Challenge](https://ethernaut.openzeppelin.com/level/0x220beee334f1c1f8078352d88bcc4e6165b792f6)
- [Ethernaut Fallback](./code/Fallback.sol) - [Challenge](https://ethernaut.openzeppelin.com/level/0x234094aac85628444a82dae0396c680974260be7)

### Level 2: Integer Arithmetics

In the second example we'll again steal tokens and Ether, but this time with an integer arithmetics flavor.

- [Ethernaut Token](https://ethernaut.openzeppelin.com/level/0x6545df87f57d21cb096a0bfcc53a70464d062512)
- [Tokensale on CaptureTheEther](https://capturetheether.com/challenges/math/token-sale/)

### Level 3: Poor Random Numbers

Another simple challenge that has to do with reading storage:

- [Guess the Random Number on CaptureTheEther](https://capturetheether.com/challenges/lotteries/guess-the-random-number/)

## Level 4: Frontrunning

This time, we'll set up a honeypot for others to tap into.

To run Theo, start it with available arguments:
```console
$ theo
The account's private key (input hidden)
> 
Contract to interact with
> 0x1df62f291b2e969fb0849d99d9ce41e2f137006e
Scanning for exploits in contract: 0x1dF62f291b2E969fB0849d99D9Ce41e2F137006e
Received an empty response from eth_getCode. Check the contract address and verify that you are on the correct chain.
No exploits found. You're going to need to load some exploits.

Tools available in the console:
- `exploits` is an array of loaded exploits found by Mythril or read from a file
- `w3` an initialized instance of web3py for the provided HTTP RPC endpoint
- `dump()` writing a json representation of an object to a local file

Check the readme for more info:
https://github.com/cleanunicorn/theo

Theo version v0.8.1.

>>> 
```

### Getting Testnet ETH

First, you need a web3 capable browser and some testnet ETH. You probably also have both, but if not, get [Metamask](https://metamask.io) and grab some ETH from the Ropsten faucets:

- https://faucet.metamask.io/
- https://faucet.ropsten.be/

### Running a Geth Light Client

Most of the examples work with [Infura](https://www.infura.io), but if you want to use Scrooge McEtherface and do things in a block-chainy way you should run your own node. Syncing a full node can take a couple of days but you can set up a light client instead. Install [go-ethereum](https://github.com/ethereum/go-ethereum/wiki/Installing-Geth) and run:

```
geth --testnet --syncmode light --rpc
```

This should sync with the Ropsten network pretty quickly. You can now point Metamask and Scrooge to `Localhost 8545`.

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

## Troubleshooting

### openssl/aes.h: No such file or directory

If you get this error, you need the libssl source libraries:

```
    scrypt-1.2.1/libcperciva/crypto/crypto_aes.c:6:10: fatal error: openssl/aes.h: No such file or directory
     #include <openssl/aes.h>
              ^~~~~~~~~~~~~~~
    compilation terminated.
    error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
    
    ----------------------------------------
Command "/usr/bin/python3 -u -c "import setuptools, tokenize;__file__='/tmp/pip-build-5rl4ep94/scrypt/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" install --record /tmp/pip-mnbzx9qe-record/install-record.txt --single-version-externally-managed --compile" failed with error code 1 in /tmp/pip-build-5rl4ep94/scrypt/
```

On Ubuntu you can install them with:
```console
$ sudo apt install libssl-dev
```

## What to Do Next

A great way to continue learning is playing challenges like [Capture the Ether](https://capturetheether.com), [Security Innovation CTF](https://blockchain-ctf.securityinnovation.com) and [Ethernaut](https://ethernaut.zeppelin.solutions).

## Credit

Created by [ConsenSys Diligence](https://consensys.net/diligence/) and the [Mythril](https://mythril.ai) team. Special thanks to Mick Ayzenberg ([Security Innovation](https://www.securityinnovation.com)), [Trail of Bits](https://www.trailofbits.com), Steve Marx of [CaptureTheEther](https://capturetheether.com) and ConsenSys fame and [Zeppelin Solutions](https://zeppelin.solutions) for vulnerable contract samples and challenges. Also, kudos to the [Truffle](https://truffleframework.com) and [Guardrails](https://www.guardrails.io) teams for working with us on Mythril Platform integration.

