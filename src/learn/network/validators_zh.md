# Validators

1.验证者节点在网络中的角色以及作用

Aleo Validators之间组成一个共识网络，并通过AleoBFT共识协议来决定区块的生成。Validator通过质押Aleo Credits来获得投票权（节点的投票权与质押的Aleo Credits的数量成正比），AleoBFT保证了：当一个新的区块被生成时，这个区块至少获得了超过2/3的投票，这意味着网络中诚实的Validators已经对这个区块达成一致意见，这有效保证了网络的安全，阻止了恶意节点的攻击。区块一旦形成，便认为是达到了最终确定性，区块以及包含的交易将不会被回滚。





2.验证者节点的经济激励

AleoBFT的机制保证了如果一个恶意的节点想要攻击网络，则至少需要获得超过1/3的投票权才能阻止新的区块的产生。这意味着，当网络中中质押的Aleo Credits越多时，共识网络会越安全。为了激励验证者节点质押自己的Aleo Credits，当每个区块被产生时，区块中会包含给验证者节点相应的BlockReward奖励。Validator节点获得BlockReward的奖励占比与它质押的Aleo Credits的占比相等。（TODO: Add Link)



3.如何成为验证者节点？

成为验证者节点需要质押至少10,000,000(一百万)的Aleo Credits。当质押的交易请求被共识网络接受之后，新的验证者节点可以立马参与到共识中，并获得BlockReward奖励，这得益于AleoBFT对Narwhal Bullshark所进行的改进。

当我们只有少量的Aleo Credits时，虽然我们不能成为独立的验证者节点，但是我们可以通过委托质押的方式来获得BlockReward奖励。



4.什么是委托质押？

委托质押是指用户可以通过Program（Aleo的智能合约）将Aleo Credits质押在某个Validator节点上，质押的AleoCredits所得到的投票权也会委托给相应的Validator节点。用户可以获得线性比例的BlockReward奖励，Validator可以通过Program的设置收取一定比例的Fee。XXX（TODO：ADD Link）钱包、浏览器为用户提供了委托质押的功能，用户可以在他们提供的UI界面上看到各个Validator的Fee比例，方便地完成质押。

用户也可以随时取消质押，取消质押后的360个区块之后，用户可以将取消质押返还的Aleo Credits提取到余额中。







