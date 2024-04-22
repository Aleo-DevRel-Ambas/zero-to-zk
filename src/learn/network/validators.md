#  Validators

### Role and Function of Validator Nodes in the Network

Validator nodes in the Aleo network form a consensus network and determine block generation through the Aleo Byzantine Fault Tolerance (AleoBFT) consensus protocol. Validators acquire voting power by staking *AleoCredits*, with the node's voting power directly proportional to the amount of *AleoCredits* staked. AleoBFT ensures that when a new block is generated, it receives approval from over 2/3 of the votes, indicating consensus among honest validators. This effectively ensures network security and prevents attacks from malicious nodes. Once a block is formed, it achieves **finalized**, meaning blocks and the transactions they contain will not be reverted.



### Economic Incentives for Validator Nodes

The mechanism of AleoBFT ensures that if a malicious node attempts to attack the network, it would need to acquire at least 1/3 of the voting power to prevent the production of new blocks.. This implies that the more *AleoCredits* staked in the network, the more secure the consensus network becomes. To incentivize validator nodes to stake their *AleoCredits*, each block produced includes a corresponding *BlockReward* for validator nodes. The proportion of *BlockReward* that validator nodes receive is consistent with the proportion of *AleoCredits* they have staked.



### Become a Validator Node

To become a validator node, one needs to stake a minimum of 10,000,000 (10 million) *AleoCredits*. Once the staking transaction is accepted by the consensus network, the new validator node can immediately participate in the consensus and receive *BlockReward* incentives, thanks to the improvements made by AleoBFT over Narwhal Bullshark.

When one possesses only a small amount of *AleoCredits*, although unable to become an independent validator node, they can participate in staking through delegation.



### Delegated Staking

Delegated staking allows users to stake *AleoCredits* on a specific validator node through a Program (Aleo's smart contract). The voting power gained from staking *AleoCredits* is also delegated to the respective validator node. Users receive *BlockReward* incentives in proportion to their stake, while validators may charge a certain percentage of fees set within the *Program*. Wallets and browsers such as XXX provide users with the functionality to delegate stake. Users can view fee percentages of various validators on their UI interface, facilitating the staking process.

Users can cancel their stake at any time. After cancellation, users can withdraw the *AleoCredits* refunded from the cancellation to their balance after 360 blocks.