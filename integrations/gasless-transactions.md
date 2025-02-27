# Account Abstraction on EVMs

{% hint style="info" %}
The following article is relevant primarily for EVM compatible networks.&#x20;
{% endhint %}

## Overview

Ethereum and other Ethereum virtual machine (EVM) compatible networks require transactions to be originated from an externally owned account (EOA) and for that account to pay the gas fees for the transaction in the native cryptocurrency of the chain (for example, Ether in the case of Ethereum).  Blockchain users pay gas in order to purchase scarce block space and fund the chain, incentivizing validators to participate in securing the network.&#x20;

For projects operating primarily over tokens or NFTs, however, requiring a native cryptocurrency balance to fund transactions introduces onboarding friction.  Additionally, ensuring end-user wallets are sufficiently funded to execute transactions creates ongoing operational overhead. For these reasons, the notion of “gasless” or "sponsored" transactions has been an area of ongoing debate for a number of years.  Unfortunately, the Ethereum community has failed to settle on a single standard nor enshrined this capability in the layer 1 protocol, leaving developers to contend with various approaches and weigh trade-offs depending on their specific use case. &#x20;

Various approaches to achieving sponsored transactions are outlined below.

## Account Abstraction (ERC-4337)

One of the newest approaches to enabling sponsored transactions on EVM chains is to use paymasters as specified in [ERC-4337](https://www.erc4337.io/), Account Abstraction (AA).  AA separates (or abstracts) authentication against smart contract wallets from the requirements of the underlying protocol (specifically ECDSA/secp256k1 signatures).  Additionally, it specifies standards for executing sponsored transactions using smart contracts called paymasters.&#x20;

Transactions in AA are encapsulated in a higher level format called user operations and submitted to an off chain network of bundlers which batch and send them to the chain through a singleton entry point contract.  Every user operation must be signed before it can be submitted to the chain. Increasingly, Dfns customers are relying on our advanced MPC solution to ensure secure and highly available signing of user operations.&#x20;

The Dfns engineering team has built proofs of concept (POCs) to demonstrate how to integrate our MPC signing with various partners offering AA platforms.&#x20;

### Biconomy

Biconomy is a web3 infrastructure provider offering a full stack solution for AA.  You can read about their solution [here](https://www.biconomy.io/smart-accounts).   Dfns has built a sample integration which processes a gasless transaction on the Mumbai testnet.  You can find a Readme and the full code [here](https://github.com/dfns/dfns-sdk-ts/tree/m/examples/libs/viem/biconomy-aa-gasless) as well as in the Biconomy documentation [here](https://docs.biconomy.io/account/signers/dfns).&#x20;

### Safe

In addition to being the industry-leading Smart Account, Safe also supports ERC-4337 via Modules and the FallbackHandler with a flow documented [here](https://docs.safe.global/home/4337-safe). The [Safe4337Module](https://github.com/safe-global/safe-modules/blob/main/modules/4337/contracts/Safe4337Module.sol) extends the Safe Smart Account to enable ERC-4337 compliant transactions, received via the proxy contract. This functionality is exposed via the Relay Kit in the Safe{Core} SDK - check [this guide](https://docs.safe.global/home/4337-guides/safe-sdk) on how to integrate it. To use Dfns with Safe AA, simply use our [GenerateSignature API](https://docs.dfns.co/d/api-docs/wallets/generate-signature-from-wallet) to sign User Operations sent through your bundler to the Safe proxy.

### ZeroDev

ZeroDev is a 4337 provider offering Bundler and Paymaster services as described in [their docs](https://docs.zerodev.app/).  Dfns has built a sample integration demonstrating a sponsored transaction using our viem wrapper which is available [here](https://github.com/dfns/dfns-sdk-ts/tree/m/examples/libs/viem/zerodev-aa-gasless).   Follow [their quickstart](https://docs.zerodev.app/sdk/getting-started/quickstart) to begin an integration.&#x20;

## Meta-Transactions (ERC-2771)

Meta-transactions are an earlier attempt to enable sponsored transactions as specified in [ERC-2771](https://eips.ethereum.org/EIPS/eip-2771).  In this approach, end user transactions are forwarded off-chain to relayers that fund gas cost in exchange for a fee. The primary caveat is the target contract must support the standard by implementing the [ERC2771Context](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/metatx/ERC2771Context.sol) interface which replaces the built in msg.sender variable with a \_MsgSender() function.  This implementation extracts the original sender from the call data to prevent the relayer address from being used in its place.&#x20;

If you control the target smart contact and can support this interface or if you have validated your target contract already supports 2771, there are a number of relayer providers you can work with in order to sponsor transactions on behalf of your users and provide a gasless user experience while signing transactions with Dfns. The following are a few examples.&#x20;

### Open Gas Station Network (GSN)

GSN is an open-source public good funded by the Ethereum Foundation and built by the authors of ERC-2771 to help promote the standard.  They offer detailed [documentation](https://docs.opengsn.org/) on configuring a relayer and have published a sample ["capture the flag" workshop](https://github.com/opengsn/workshop) to demonstrate the changes required to make a smart contract compatible with 2771.   They even have a [detailed tutorial on setting up a relay server](https://docs.opengsn.org/relay-server/tutorial.html#introduction).  You can reach out to the GSN team in [discord](https://discord.gg/NXXTCbh58s) with any questions.

### Biconomy

Biconomy is one of the longest standing players in the relayer market.  They offer a [Gasless SDK for EOA wallets](https://docs-gasless.biconomy.io/) to enable sponsored transactions.   They have [detailed documentation](https://docs-gasless.biconomy.io/products/enable-gasless-transactions/choose-an-approach-to-enable-gasless/eip-2771) on implementing the 2771 standard as well as funding deposits via their [Gas Tank](https://docs-gasless.biconomy.io/guides/gas-tank-deposits).   They also cover the steps required to [enable a smart contract for 2771 support](https://docs-gasless.biconomy.io/tutorials/native-meta-transactions/enable-native-meta-transactions).  Feel free to [reach out to the Biconomy team](https://docs-gasless.biconomy.io/comm/contact-us) with any questions specific to their offering.&#x20;

### Gelato Network

Gelato describes themselves as "web3’s decentralized backend" and provides a number of services to enhance smart contracts, including 2771 compatible relayers.  They expose a centralized funding service called [1 Balance](https://docs.gelato.network/developer-services/1balance).  Once an account is funded, it can be used to sponsor transactions on behalf of users by implementing [sponsoredCallERC2771](https://docs.gelato.network/developer-services/relay/erc-2771-recommended/sponsoredcallerc2771) as detailed in their documentation.  Please contact Gelato directly with questions pertaining to their specific configuration.&#x20;

## Automated "Gas Tanks"

The final approach to enabling gasless transactions is simply to transfer the required amount of the  native cryptocurrency into each end users' wallet, relieving them from the responsibility of acquiring it themselves.  In most cases, implementations of this approach specify a "gas tank" wallet which automatically sends cryptocurrency to other wallets when they fall below a given threshold.

The primary problem with this approach is the cost of sending each individual "refuel" transaction on chain.  Looking at the economics on the Ethereum L1, it does not scale well as demonstrated in the calculations below:

<img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="original">&#x20;

## Next Steps

If you're interested in implementing sponsored transactions or have a novel approach to the problem, don't hesitate to reach out to us as this is one of our favorite topics of discussion.  As always, feel free to send us feedback at [docs@dfns.co](mailto:docs@dfns.co).&#x20;







