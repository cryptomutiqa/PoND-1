# 基于分散度证明协议的区块链项目
### 项目综述
           本项目是希望用一种新的共识协议-网络分散度证明取代已有的POW和POS协议，解决现有系统下的一些缺陷问题，实现更环保、更公平的加密数字货币底层协议。在此基础上利用跨链通信技术构造一系列不同分工和不同特性的区块链，并在协议层面给予开发者应有的物质激励，最终使本项目成为一个开放的可扩展系统，在区块链，特别是加密数字货币领域为人们提供一个更安全、更公平和更灵活的平台。<br><br>

## 网络分散度证明协议（Proof of Network Distersity）
*(专利号CN 2018102289423)*
### 设计目的
           我最初的设计目标是利用网络传输延迟特点创造一个无法模拟的能力证明方式，实现低能耗的POW式共识。<br><br>
           因为能耗问题，工作量证明(Proof Of Work)模式正越来越被人们所诟病。然而抛开能耗问题， POW模式仍然是所有解决方案里面最高效、最稳定、最体现本质的。工作量证明以其简单直接的“能者多得”逻辑完美的解决了开放式分布系统的共识问题，其它可行的全分布式的共识协议都沿用了这个核心逻辑，比如活动证明(Proof Of Activity)、燃烧证明(Proof Of Burn)、存储证明(Proof Of Storage)、消逝时间证明(Proof Of Elapsed Time)等等，其中权益证明(Proof Of Stake)共识因为不依赖任何外部资源，而且完全抛弃了耗能的“算力”竞赛而被最广泛的接受。因为POS模式将核心逻辑从“能者多得”的基础上发展为“富者多得”，使得区块链的记账权竞争只需要在一个个静止的内部状态之间进行，不需要消耗电力。但是POS共识也存在自己的缺陷，比如“nothing at stake”导致的多重投票问题、“stake grinding”攻击问题等等，其中一个无法避免的缺陷，就源于它的核心逻辑“富者多得”：当付出相同的劳动时间后，富人会获得更多，所以富人倾向于花更多的时间工作而穷人则相反，导致富人更富，穷人更穷，这个过程会不断加速，最后成为少数人的“贵族游戏”。尽管有的POS协议实现了完全公平的利息制度，取代了挖矿活动，但这样的协议“给所有人奖励，相当于所有人都没有奖励”，降低了所有用户参与维护网络的积极性，容易受到攻击。<br><br>
            本文的初衷，只是想找出另一种不耗电力的能力证明协议替换权益证明，基于一个简单的事实：因为网络延迟的缘故，要想最快的获取分散在网络中的信息，必须要同样分散在网络中的多个节点共同收集才能实现，这是无法通过提高单机的性能来达到的，而这也是一种“能力”的体现。然而在设计过程中，我发现很多过程都无法、也不需要摆脱权益属性，最后证明让用户自由的选择用权益还是用能力来争夺记账权，用利益分配法则来引导用户参与，可以最大程度的保证公平性和安全性。<br><br>
           最后的设计相对于POS和POW，具有以下优点：不存在算力竞赛，节约能耗；不存在无利害关系(nothing at stake)导致的多重投票和粉碎攻击(stake grinding)缺陷；在基于权益的基础上优化财富分配逻辑，以矿工代替所有非积极节点参与网络维护工作；确保了财富获取和工作量相匹配，以推动数字货币的良性发展。<br><br>
*（注：因为本设计只针对完全去中心化的共识协议，故对PBFT、DPOS、Ripple等协议暂时不做讨论。）*<br><br>

### 场景和角色
整个网络可以想象为蜜蜂采蜜的场景，节点按照分工不同有以下三种角色
1.	花节点<br>
网络中每笔交易的广播源节点即为一个花节点，负责响应第一个向它发起“采蜜”请求的工蜂。花节点的权益视为“花蜜”，采集花蜜的多少影响到矿工的竞争力。花节点还拥有主链的投票权。
2.	蜂巢节点<br>
蜂巢节点是矿工，他们拥有自己的工蜂为他们收集花蜜，使用花蜜参加出块权竞争，此外还要验证交易、区块等。任何用户都可以作为蜂巢节点。
3.	工蜂节点<br>
附属于各个蜂巢，负责侦测附近的花节点，同时尽快发出采蜜请求。一个请求被花节点响应的过程视为一次“采蜜”。<br><br>

网络结构如下图所示：<br>
![Alt text](/res/charactors.jpg)<br>
图1：节点及网络结构示意图<br>

### 共识过程
>1.	**每笔交易发布之前，花节点发出一个广播信号，附近的工蜂收到广播信号后向花节点发出“采蜜”请求**
>2.	**收到第一个请求后，花节点将主链末尾块地址b和工蜂所属蜂巢地址m写进交易结构，广播本次交易，一次成功的“采蜜”完成**<br>
*（注1：认定主链遵循“最重”原则，参考GHOST协议；工蜂的请求中会有一个块地址作为参考，但采用与否完全在花节点）*<br>
*（注2：交易数据结构需要增加b和m两个32字节的字段，如果其它位置没有存放账户权益，还需要加上balance字段。）*
>3.	**此交易被某个矿工确认打包到新的区块，并广播到网络**
>4.	**蜂巢节点接收到新块的广播后，验证每个交易的同时，检查交易中的m和b。当m指向蜂巢自己时，标记此条交易为x，当b指向当前链末尾区块时，标记为y**
>5.	**蜂巢节点取得所有花节点的权益数，并平均分配到它们各自在块中的每条交易。**
>6.	**累计同时有xy标记的交易分得的权益数，记为X，直到出块为止，最多累计100个区块**<br>
*（注：x标记的含义为记账权投票结果，而这里为什么还需要检查y标记，见“[作弊和攻击](#作弊和攻击)”小节第1条）*
>7.	**统计y标记的交易分得的权益数，记为Y，只统计当前块**<br>
*（注：y标记的含义为主链投票结果，实际上Y值还需做一些调整，见“[作弊和攻击](#作弊和攻击)”小节第2条）*
>8.	**蜂巢周期进行一个基于常量（时间戳、个人签名等）的数学运算，期望结果达到某个目标，满足出块要求，记为：proofFunction() \< target\*d\*X\*Y，其中target 是目标，d是难度调节参数。期间如果收到多个竞争块，可以并行处理。为了不削弱主链，只要主链重量不超过当前链+8，不要停止在当前链的竞争，这样也符合自身利益（有可能变成主链）**<br>
*（注：计算周期会有浮动，见“[作弊和攻击](#作弊和攻击)”小节第1条）*
>9.	**当达到出块要求后，蜂巢将这段时间收到的交易打包并广播至全网。同时打包的还有所有xy标记的交易id(为了节约空间，可以在100个区块范围内编码)、各个节点收益和其它计算参数，以供验证。挖矿所得的收益由矿工和所有xy标记的花节点按比例分配**<br>
*（注1：分配方案见 “[收益分配](#收益分配)”小节）*<br>
*（注2：关于验证参数和收益分配数据所占用的额外空间，至多相当于给每条交易增加2字节的id、4字节的收益，完全在可接受范围之内；而验证区块或确认交易所要做的额外工作则至多是遍历之前100个区块，只要算法得当，也不会造成过多的工作量）*<br>

* 为了便于理解，以上步骤可以进行如下简要描述<br>
           **“蜂巢节点不断的在全网进行采蜜活动，采集的结果记录在区块中，采集的花蜜量与赢得记账权的几率成正比；花节点每次广播都以权益对主链的分支进行投票，投票的结果也记录在区块中，分支赢得的权益与它被认定为主链的几率成正比。”**<br><br>
* 更加简化的描述<br>
           **“用户每次交易都用权益对主链和记账节点分别进行投票，赢得权益最多的分支或节点获胜的几率最高。”**<br>
*（注1：为了控制投票频率，需要计算每个账户上一次交易到当前的区块间隔，以一定粒度，比如以10个区块为单位作为调节系数，从0开始，每过一个单位时间，系数增大一定比例，间隔一段较长时间，比如6000个区块，大约100个小时之后才能将系数恢复到最大值；同样每一个UTXO也增加调节系数，以转账频率调节权益大小，方法和参数都与前者相同。）*<br>
*（注2：为了提高效率，可以增加纯投票类型的交易，用户也可以根据自己交易的频繁程度选择当前是否参加投票）*<br><br>

下面分两张图分别解释出块权投票（x投票）和主链投票（y投票）的工作原理：<br>

![Alt text](/res/vote_on_x.jpg)<br>
图2：出块权投票及生效过程<br>
           如上图所示，在已产生的若干区块中统计选票，获得选票最多的蜂巢将有最高的几率赢得出块权<br>
           
![Alt text](/res/vote_on_y.jpg)<br>
图3：主链投票及生效过程<br>
           如上图所示，每当产生分支时，交易会对分支进行投票，决定了下次出块时各个分支所获得的选票（权益）多少，也就决定了出块难度参数Y的大小。因此获得越多选票的分支，产生下一个区块的速度也会更快，而更快的广播出块会在再下一次的投票中获得比上次更多的选票，因此上述过程将加速拉开两个分支的差距，迅速决定出主链。<br><br>
           
           按照以上共识流程，矿工要想在竞争中获得更大的优势，就必须在物理网络上拥有更多而且更分散的工蜂，以响应随机出现在网络中的花节点信号。所以此竞争机制无法在单机上进行模拟，避免了无限提高机器性能的装备竞赛，保证了挖矿的公平性。<br>
           而把主链的投票与记账权投票分离的意义在于，让权益独立的来决定分支。因为矿工争夺的是用户对记账权的投票，主链的投票对其收入没有影响，没有争夺的必要，这样能保证即使在出现用户过于集中的情况，也可以效的防止对分支的攻击。另外协议对钱包程序也有一种激励机制（见“[钱包应用](#钱包应用)”小节），鼓励多让用户选择自由竞争的状态，进一步确保主链的稳健。<br>
           同时蜂巢加工蜂的结构可以在普通app和网页开发中进行嵌入，我希望以此能够帮助一些开发者走出只有靠显示广告获得收入的困境，或者能够在使app或网页拥有更佳的用户体验上有所贡献。<br><br>

### 争夺花节点
           为了拥有更强的出块能力，蜂巢会尽可能的争取到更多的花节点，虽然我设计的初衷是大家都用更多工蜂来获得花蜜，例如在app中嵌入工蜂程序，为了有更多的工蜂，就必须不断提高app的品质来赢得使用者。但是不可避免的，会有一些用户相互达成协议，以团队形式合作挖矿（比方说钱包或者矿池）。既然如此，我们干脆在交易结构中提供一个分类选项，你可以选择A.以广播的形式接受工蜂采蜜；或者B.指定一个矿工，直接让他赢得选票；甚至C.指向自己，单独挖矿。这样做看似影响了公平性，但实际上站在矿工的角度考虑，如果他们能够通过调整自己的分配方案，互相竞争吸引用户，就会出现一些大小不一的团体，与工蜂加蜂巢一起竞争出块权，这其实也是有利于网络健康发展的。这些团体或者个人，以近似于POS的模式参与竞争，可以有效的降低某些拥有巨大工蜂群的蜂巢控制整个网络的风险。<br>
#### 钱包应用
           这里我们需要讨论一类特殊的app——钱包应用，因为钱包应用可以引导用户甚至直接决定交易的类型和投票的目标，基于自身利益的考虑，大部分钱包应用都会选择B类方式为自己挖矿，或者优先响应自己的蜂巢，这样的话普通蜂巢就基本没有机会参加竞争了。因此我们需要有一个机制来鼓励钱包应用多选用A类交易，同时提供给钱包应用一个正当的获得收入的途径，比如：A类交易增加钱包收益字段，把一部分收益分配给钱包应用的运营商，而B类和C类则没有这种奖励。这样做还带来一个额外的好处，可以鼓励开发团队开发优秀的钱包应用和优秀的侧链，为开放式平台提供物质上激励的基础（见“[多链](#多链)”小节）。<br>
#### POS变种
           由此还可以导出一个此共识的变种方式，即只存在C类矿工。这种情况下每个人都凭借自身的权益单独挖矿，变成了纯粹的POS模式。这样的变种体系也能保证公平性，而且解决了普通POS模式下的几个问题（见“[设计目的](#设计目的)”小节）。虽然如此，由于POS模式无法避开的财富分配问题，我还是倾向于使用原先的模型来构造区块链，即使单从权益分配的角度来说，A类和B类矿工也扮演了重要角色：由于有AB两类矿工的存在，低权益的用户即使工作积极性不高，也会有矿工替他们完成竞争工作，大致上，用户只需要在100个小时之内发起过一次交易就能保证所持权益不缺席投票活动了，这样就能基本避免前面所说的财富分配不均的问题。<br><br>

### 收益分配
           挖矿所得的收益如果分配策略不同，用户在利益的驱使下会造成网络结构和个体行为的变化。这里的分配是指A类挖矿所得的分配，B类的分配完全由矿工来完成，C类则不需要分配。有以下这些问题需要考虑：<br>
1.	为了避免蜂巢将自己拆分后获得更多的利益，蜂巢应该按照总量的固定比例分成
2.	权益的分配权重会影响到用户的选择，如果完全按权益分配，绝对公平的分配机制可能不足以吸引更多的普通用户参与其中，但是如果权益权重过低，则会失去太多高权益的用户。
3.	降低权益的比重后，为了避免用户能够无限拆分自己的账户以获得更多收益，需要将手续费作为参考依据，这样还可以为不同需求的用户提供更多的可选策略，起到调节的作用<br>

           所以A类挖矿分配方案最好受权益和手续费两个参数共同影响，比如：矿工分得总收益的20%，剩下分成两部分，1/3按照手续费分配，2/3按照权益分配（这里的权益是指累计到X参数的权益，每个花节点可以重复计算），花节点每次获得收入的25%(即总数的20%)分给钱包账户。<br>
           而B类挖矿的分配方案由矿工根据和用户的约定在挖矿程序中自行设定，虽然相对自由，但是也有一定的限制：为了保证钱包应用的收益（见“[钱包应用](#钱包应用)”小节）始终大于钱包自己挖矿的收益，B类挖矿的矿工所得必须限制在A类挖矿中钱包收益的比例之下，这里即20%以下。<br>
           用户在不同投票方案下的默认手续费可以根据前一段时间A、B两种用户平均每次参与出块的人数n进行调整。人数越少，说明此类矿工越多，则降低交易费，吸引更多的用户，以匹配矿工的数量。实际上即使没有上述调整，经济规律也会让人们自然做出类似的平衡，只不过慢一点。另外，对A类挖矿，还可以根据n同时调节蜂巢节点分成比例：人数少，则降低分成比例，吸引更多的用户选择A方式，同时还能降低蜂巢节点的数量，加速调节网络结构。<br><br>

### 作弊和攻击
1.	过滤交易，矿工只选取对其有利的交易打包<br>
           因为每次出块时包含的交易可以影响之后的竞争环境，所以矿工有可能通过挑选打包的交易试图获得更有利的位置<br>
           首先每个块包含的交易只占统计总量的一小部分，改变一个块的内容并不能对统计结果造成很大的影响。要更好的解决此类问题，需要统计本次打包的所有交易的花节点的权益之和作为参数，用来调整下一次出块的计算周期，权益越高，计算周期越短，否则越长，比如让计算周期在0.9秒-1.1秒之间浮动，这样会直接影响下一次出块的速度。如此一来，打包尽可能多的交易才是更好的选择，可以减少矿工作恶的动机。该参数每隔一段时间应该根据平均值做一次调整。<br>
           由此可能会有一个疑问：累计权益时为什么要参考y标记，不能直接按x标记来计算呢？不参考y标记就不存在本条的问题，而且这样也不好理解：统计记账节点的投票结果时把分支的投票结果也带进来，从逻辑上讲没有任何意义。但这样做有个唯一的好处，就是任何人选错分支都得不到收益，这样会促使用户在选择分支时更加谨慎，而不是随便选，或者不选。即使这样做会导致一部分统计不准确，考虑到实际应用中产生孤块的几率并不高，相对于前面所说的好处来说，统计上带来的偏差也可以忽略不计了。<br><br>
2.	同样是过滤交易，但目的是影响分支的发展速度<br>
           因为每次出块包含的交易数量也决定了当前链下次出块速度（影响到Y参数），攻击者可以有意减少某条分支的交易数量，影响主链的认定<br>
           解决方法：我们在共识过程第4步检查交易内的地址b时，除了标记指向当前链末尾的交易为y以外，还标记指向当前链倒数第2、3（数量可调整）块的交易为y1，计算Y值时，y、y1两种标记的交易同时统计。这样即使某个攻击者削弱了某个块的出块速度，只要他后面的矿工是诚实的，就会把攻击者有意漏掉的交易补上，这些交易会指向当前链倒数第2+个区块。如果这些交易可以有效的提供给难度参数Y所需要的权益值，那这条支链发展的速度也会由后面的矿工弥补上来。<br>
           这里同样也可能会有一个疑问：可不可以在计算X参数时也统计xy1标记的交易，这样即便是有人在打包区块时有意的对交易做了手脚，只要后面几个块的矿工把交易补上了，对统计结果也不会有影响？回答是不可以。因为这样做的话，在产生分支时，节点可以避重就轻，投票给分支之前的区块，以确保自己正确。这样会妨碍分支快速分出胜负，导致无法及时确定出主链。<br><br>
3.	51%攻击<br>
           如果某个用户集中了50%以上的资源，系统就会完全处于他的控制之下，这点和其他算法是相同的。这里我们分析一下掌握了50%以下资源的用户能发起什么程度的攻击。<br>
           我们暂时使用前面的设定，出块周期为60秒，6000个块间隔为权益恢复期，则平均每个块会有总量1/6000的权益，我们取100个块作为统计区间，那么平均每次出块就会有(1/6000)\*100=1/60的权益参加竞争。要想控制出块权，只需要每一个块中放入大于1/120的权益。假定一个用户掌握了50%的权益而且全都积攒超过了100个小时，那么他可以保证在(50%)/(1/120)=60个块之内能成功进行双花攻击。因此我们如果有一个很重要的交易，等待1小时的时间再进行确认，才能保证绝对安全。<br>
           100个块的统计区间是一个非常保守的设计，因为我现在还没有对数据查找算法进行深入研究和试验，为了防止在验证区块和交易时造成瓶颈，暂时设定为100个区块。实际上根据目前数据库的查找速度水平，只要进行适当的设计，我估计还有很大的提升空间。假设能提升到1000个块作为统计区间，系统的安全性就能提高10倍，以此类推。<br><br>
4.	模拟工蜂，即试图通过模拟创建大量的工蜂节点在p2p网络中接入许多虚拟节点，从而提高成功采蜜的几率<br>
           为了应对此情形，我们可以在创建p2p连接的时候加以控制，比如每个客户端都只与和自己响应速度最快的前M个节点建立连接即可。<br><br>

### 出块速度
           由于完全的去中心化，和比特币一样，我们在出块速度上也将受到限制。但是，和比特币不同的是，在此共识模式下，出块能力实际上是和网络响应速度密切相关的，出块几率大的蜂巢收到新块广播的时间也更短，而出块能力低的蜂巢即使延迟一些收到广播也没多大关系。此外，我还把主链选择协议从POW换成了GHOST，可以在产生孤块后更有效的抵御攻击。因此我们的共识周期一定会比比特币的更短一些（每分钟一块？现在还缺乏实验数据）。<br>
           而要实现类似于DPOS的只在少数节点之间轮流出块的高流通率区块链，只需要在此共识的另一变种上做一些改动。当只存在B类矿工时，我们的共识体系本身就是一种投票机制，在此基础上做一些修改，比如简化验证流程、取消竞争机制和收益分配、取消主链投票等即可。<br><br>

## 项目大体规划
### 多链
           由于此种共识方式下合并挖矿的开销非常低，多验证一条链只需要多开一个线程而已，而且由于没有寄生关系，不会存在算力攻击的问题。所以我们在运行侧链方面比POW方式更具优势。<br>
           区块链的去中心化程度决定了它的健壮性，但是去中心化程度同时限制了全网同步的速度，也就限制了出块速度，使得健壮性和交易通量成了区块链发展中两个互相矛盾的特性。另一方面，区块链的设计复杂度越高，它能实现的功能就越多，但是代码复杂的同时会带来更多的不安全因素。如果创建一个万能的区块链很困难，那么按不同的需求将数据放到不同的链上去处理应该是一个好主意。用户可以把自己的多数资金放在完全去中心化、更安全但是速度比较慢的主链上，然后在需要进行小笔交易或者其它业务的时候将一部分资金转移到某条侧链上去进行。侧链可以设计为牺牲一部分安全性但是提高了性能的高速链（见“[出块速度](#出块速度)”小节），也可以设计为拥有智能合约功能的更加复杂的业务链等等，楔入到其他区块链也是比较好的选择。<br>
           同时，由于协议层面为开发者提供了基本的利益分配作为激励（见“[钱包应用](#钱包应用)”小节），希望由此会有更多的开发团队投入到侧链的开发中来，为整个系统健康成长提供新鲜血液。<br>
### 前期规划
           我目前的计划是起码有两个链，一个基本链用于提供最安全的虚拟货币资产保护和交易功能，一个业务链实现无第三方仲裁的兑换交易（详情请见《[去中心化交易所](https://github.com/yj1190590/backupDEX)》）。然后视团队情况和技术细节而定，可能优先开发高通量的小额交易链和能运行智能合约的业务链。以及完善一套接入标准，以方便和其他项目的对接，如前言中所说的那样，项目目标是要构造一个功能多样的可扩展的区块链族，能够方便的与不同开发者的不同项目之间实现价值传输是很有必要的。<br><br>
*(由于去中心交易所的专利还没有申请好，所以暂时还不便公开)*
