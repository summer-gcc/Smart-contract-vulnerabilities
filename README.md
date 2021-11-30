# 智能合约漏洞
记录一些漏洞分析

# Wolf Game300倍羊毛
Wolf Game的300倍羊毛

https://twitter.com/not__stoops/status/1462638499316699137


漏洞类别
可重入漏洞
Worf Game
一只羊一天可以生产一万枚羊毛token wool,wool可以卖出或者用来繁育下一代,羊的持有者每一次领取wool的时候都需要将20%送给狼,羊累计了2万枚token wool的时候就可以解除质押,此时狼将会有50%的几率将羊身上的wool全抢走
分析
进一步的利用使玩家可以选择在取消抵押他们的羊 NFT 时要求 600 万个 WOOL 代币，而不是预期的 20,000 个。
1. 代码的 claim 和 unstake 分支有一种可重入漏洞：它调用 safeTansferFrom 在删除其 staking 信息之前发送你的羊。这里有个问题:如果它是个合约, safeTransferFrom调用接收令牌的地址

2. 具体来说，它调用 onERC721Received(ERC-721的官方解释是"Non-Fungible Tokens"，英文简写为"NFT"，可以翻译为不可互换的Tokens，简单地说就是每个Token都是独一无二的且不能互换的。)。我们所要做的就是部署一个合约，其中一个 ERC721Received 回调到claim和unstake函数。

3. 因为最初的 safeTransferFrom 调用还没有完成，stake 删除仍然没有发生，而且合约仍然认为我们的羊在这些嵌套调用中被staking，并且每次都向我们发送我们的羊毛。

4. 这里唯一棘手的是，即使在每个嵌套调用中，Barn 合约也必须拥有令牌，以便 safeTransferFrom 成功。似乎他们试图通过覆盖他们自己的 onERC721Received 来防止我们直接将代币转回给他们

5. 然而，我们可以通过调用 transferFrom 而不是 safeTransferFrom 来解决这个问题，它根本不会调用他们的合约，从而绕过这个检查


6. 我们将示例合约中的嵌套调用次数上限设置为 20 倍，因为它可以加快测试运行速度，但您可以嵌套多少 unstakes的唯一限制是区块 gas 限制和其他被抵押的羊的数量（因为 totalSheepStaked 不能低于零）。
在#17 MAX_COUNT改成300以占据整个块并获得300x

需要在#174进行编译器插入的溢出检查才能成功,至少需要抵押300只其他人的羊才能窃取300x

7. 在实践中，因为许多人都在放羊，所以这只是区块 gas 限制。在 30M 时，这将使您获得大约 300 倍的乘数。
8.  Executor 合约还旨在防止 $WOOL 被盗（“Would have been robed”恢复），可以使用 flashbots 避免在这些尝试中支付 gas。
9. 此漏洞还使 totalSheepStaked 永久不准确。使用后，6 中提到的溢出检查将防止被放牧的羊的数量低于您的乘数。如果你用了 300，最后 300 人将永远无法解除他们的羊
补充
function safeTransferFrom(address _from, address _to, uint256 _tokenId, bytes data) external payable;
// 与transferFrom类似的资产转移函数。
// 它会额外检查_to地址和_tokenId的有效性，另外如果_to是合约地址，
// 还会触发它的onERC721Received回调函数。
function transferFrom(address _from, address _to, uint256 _tokenId) external payable;
// 将_from地址所拥有的_tokenId资产转移给_to地址。 
// 调用方必须是资产主人或是已被授权的地址，否则会抛出异常。

为了避免safetransferFrom触发游戏合约的onERC721Receiver函数, 使用transferFrom函数,这样就不会触发游戏合约的函数,而进行到下一个函数中.



