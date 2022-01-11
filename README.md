# 智能合约漏洞
记录一些漏洞分析

#Wolfgame
在vultron检测不出来的原因：
vultron要求abi.type===“function” && abi.constant==false，若是constant为true，那么它不会改变状态变量，因此它可能不是一个交易，vultron不会将这个函数作为候选序列

