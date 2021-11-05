---
nav_order: 2
title: faq
---

# FAQ

This FAQ section has been submitted to the WarpDrive program. Anyone may contribute a Question and Answer which may be eligible for PP rewards. See <https://github.com/zenon-wiki/zenon-wiki/issues/1> for details.

# History

# Alphanet

### Q. Will smart contracts be part of Alphanet?
A. Yes. Initially only the embedded smart contract layer will be available at Alphanet launch.

### Q. When will cold wallet support be available?
A. The implementation for cold wallet support is possible and can be developed after Alphanet launch.

### Q. I am interested in running a node on the Alphanet. Are there requirements or specifications for what is required in terms of hardware capabilities?
A. Yes the node hardware requirements have already been published for the testnet. It doesn't mean Alphanet's will be the same, but you could imagine these as minimum requirements. You can easily achieve these requirements with a cloud vps. https://testnet.znn.space/#!requirements.md

# wZNN

### Q: What is the wZNN token address on BSC?
0x8a7ca49f42e5196c26bb00fa014352e5d065db4d

<https://bscscan.com/token/0x8a7ca49f42e5196c26bb00fa014352e5d065db4d>
This can be found at <https://bridge.zenon.network> in the menu at the upper right.
More info about wZNN can be found at <https://medium.com/@zenon.network/zenon-building-bridges-272538cde1a7>

### Q: What is the ZNN burn address for bridging ZNN to wZNN on BSC?
ZNNxBSCBridgeSwapBurnAddressZk7mw2

<https://explore.zenon.network/address/ZNNxBSCBridgeSwapBurnAddressZk7mw2>
This can be found at <https://bridge.zenon.network> in the menu at the upper right and at <https://medium.com/@zenon.network/zenon-building-bridges-272538cde1a7>

### Q. Why is there a bridge to Binance Smart Chain versus other chains?
A. BSC was initially chosen to be used for a one-way bridge because of EVM compatibility and low fees, facilitating ease of transfer for buyers and sellers.

### Q. Will Zenon bridge with other chains like Fantom, ETH or AVAX, similar to the current BSC Bridge?
A. All EVM chains are compatible with the current Zenon Bridge implementation and can be extended to work two-way after Alphanet launch.

### Q. What does it mean that the bridge is "one-way"?
A. This means that, for now, when you bridge ZNN over to BSC, the coins will remain wrapped (wZNN) on BSC and cannot be sent back to the legacy chain. After Alphanet launch, two-way bridging will be implemented and you can send wrapped coins back to the network for staking and delegation.

### Q. Can I stake wZNN that I have bought on Pancakeswap?
A. No, you can only stake ZNN on the legacy network, and either stake or delegate on Alphanet after it launches. wZNN can be bought, sold, or used for liquidity provision, but cannot be staked.

### Q. Can I store wZNN in Metamask?
A. Yes, you can send wZNN to any web3 compatible wallet in the same way you would send any other BSC token to it.

### Q. Can I earn rewards by participating in the liquidity pool for wZNN/BNB?
A. Yes, plasma points are being rewarded daily to liquidity providers based on their share of the LP. This will continue until the end of the incentivized public testnet, and upon Alphanet launch, the $PP tokens will convert into $QSR at a rate of 1000 $PP to 1 $QSR.

# Incentivized Testnet

## SwapDrop

### Q: Can I still participate in SwapDrop and receive PP rewards?
Yes, but only if you had ZNN on a legacy address at block 1,331,331.

For more information about the Swap Drop event see <https://medium.com/@zenon.network/swapdrop-event-october-1st-bd99964589aa>

### Q: Does participating in SwapDrop affect the ZNN in my legacy wallet?
No.

SwapDrop is an incentivized test for the real swap for Alphanet. As stated at: <https://medium.com/@zenon.network/swapdrop-event-october-1st-bd99964589aa>
> The legacy assets (ZNN and QSR, if applicable) will be swapped into testnet coins (tZNN and tQSR, if applicable) without affecting the legacy network balances.

# Legacy Network

### Q. Where are some good sources of information to familiarize myself with the project?
A. A community member compiled a lot of various info from other articles into one big article, "The Ape's Guide to the Galaxy". This is a great initial read to familiarize yourself with the project. Then you can dig into more details via the team's medium articles or asking specific questions of the community in the official Telegram channel. Links are provided below:
https://shazzamazzash.medium.com/zenon-network-an-apes-guide-to-the-galaxy-7aad7dacdfef
https://medium.com/@zenon.network

### Q. What's the difference between the legacy network and the Alphanet?
A. The legacy network is a placeholder chain that allowed the network to decentralize while the Network of Momentum was being built. Upon Alphanet launch the legacy network will still exist but mostly to facilitate holders moving their coins to Alphanet. At that point, the legacy network's coins will no longer be traded on exchanges.

### Q. I downloaded the Legacy wallet but it didn't make me create a recovery phrase, why?
A. The legacy wallet doesn't use recovery phases, if you back up a copy of your wallet.dat file it contains your private key however.

### Q. Once the Swap Cycle begins from Legacy to Syrius, would the staking continue on legacy wallet?
A. No, staking will commence on the Alphanet and cease on the legacy network. To continue staking, you will need to migrate your coins to the Alphanet via a swap mechanism (more details on this to be announced soon).

### Q. Is there an issue if I am delayed in swapping my coins over to Alphanet? Can I leave the coins in the legacy wallet without an issue.
A. You theoretically can leave your coins in the legacy wallet, but there is a decay mechanism built into the Alphanet swap which penalizes you for delaying swapping over to the new network. Eventually the legacy network will be unsupported by most network participants, so everyone is encouraged to swap over as soon as it becomes possible.
