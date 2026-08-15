# Bitmap 会不会成为“后补贴时代的矿工第二区块奖励”？

**研究日期：2026-08-15**  
**核心问题：** 新区块对应的 `.bitmap` 是否可能由出块矿工优先取得，并在区块补贴持续减半后，成为矿工挖矿收益乃至 Bitcoin 安全预算的一部分？

## 一、先给结论

### 1. 技术事实：这个设想在技术上成立，但要改写成更准确的一句话

> **不是“矿工挖出区块后自动获得 Bitmap”，而是“控制区块模板的人，可以提前准备或在候选区块内放入自己的 commit/reveal，并把自己的 `{高度}.bitmap` reveal 排在竞争者之前，因此拥有近乎决定性的优先申领权”。**

Bitmap 官方文档明确采用：第一个把现存区块高度写成 `{block-height}.bitmap` 的有效铭文，取得该 District；白皮书同时明确其为 `first-is-first`、供应量随 Bitcoin 区块数扩张的社会共识规则。[16][18][21]

所以用户听到的两个说法中：

- **“谁 gas 高谁拿到”——不精确。** Bitcoin 没有以太坊式 gas；竞争者支付的是交易费率。较高费率只是提高普通交易被矿池选中、被较早排列的概率，不是协议保证。
- **“挖出区块的矿工可以优先打包自己交易，不论手续费”——方向正确，但主体通常是区块模板控制者。** Solo miner 可以自定模板；传统 Stratum V1 下通常是矿池运营方决定交易集合和次序。[3][4][5]

  Stratum V2 才把更多模板选择权交还具体矿工。[19]

### 2. 关键限制：不能在找到有效区块哈希之后，再把 Bitmap 交易塞进去

工作量证明提交的是包含 Merkle Root 的区块头。交易集合或顺序一改，Merkle Root 就变，原来找到的有效哈希随即失效，必须重新挖。因此正确流程不是：

> 找到块 → 临时生成 Bitmap → 塞入已找到的块。

而是：

> 预先准备 commit/reveal 或在候选区块模板中加入 commit+reveal → 按这个模板持续哈希 → 若该模板出块成功，自己的 Bitmap 申领随区块一起确认。

Ordinals 铭文采用 commit/reveal 两阶段：commit 创建承诺输出，reveal 花费该输出并公开铭文内容。[2] Bitcoin 的区块模板允许纳入有依赖关系的交易，BIP 22 的 `depends` 字段明确表示某笔交易之前必须出现的父交易。[3]

### 3. 概率判断

我把事件拆成四层，而不是给一个模糊的总概率：

| 事件 | 主观概率 | 判断 |
|---|---:|---|
| 矿池/矿工技术上批量优先申领新区块 Bitmap | **90%+ 可实现** | 无需 Bitcoin 协议修改，主要是模板、钱包和索引工程问题 |
| 至少一家矿池把 Bitmap 作为附加收益或纪念资产试点 | **15%–30%（未来 5–10 年）** | 门槛低，但当前经济价值太小、会计和分配麻烦 |
| 新 Bitmap 形成持续、可交易的“矿工第二奖励”市场 | **5%–15%（未来 10–20 年）** | 需要 Bitmap 社会共识、产品使用和流动性同时扩张 |
| Bitmap 成为足以“撑起 Bitcoin 安全大旗”的主要安全预算 | **低于 1%（可见的数十年）** | 所需单块价值比当前高四至五个数量级，而且收益不属于共识层保证 |

**结论不是“不可能”，而是：技术可能性很高，经济兑现概率低；一旦兑现，资产价格弹性极大。** 这正是一个值得作为“右尾期权”研究和持有的命题，但不能当作基本盘现金流。

---

## 二、我们此前对 Bitmap 的认知，和这次新命题如何衔接

此前工作的核心结论是：

1. Bitmap 的底层证明力来自 Bitcoin PoW，不是 Bitmap 自己创造安全性。
2. 如果只讲 3D 土地、Voxel 房屋，刚需较弱。
3. Bitmap 更有希望的定位，是 Bitcoin 原生的**可信根、公共坐标、身份/记忆/组织连续性入口**。
4. `7187.bitmap` 与 Organa 的实践，就是把“地块”变成可解析、可签名、可验证、可运行的组织坐标，而不是等待纯土地叙事上涨。

这次提出的“矿工第二奖励”命题，第一次把 Bitmap 的价值与它最底层的生产者——矿工——连接起来。它不是对旧逻辑的替代，而是增加了一个潜在飞轮：

```text
区块产生
→ 新 Bitmap 申领权出现
→ 模板控制者优先取得
→ 矿工/矿池开始持有、拍卖或分配
→ Bitmap 被理解为每个区块的数字产权凭证
→ 价格和流动性提高
→ 优先申领权更有经济价值
→ 矿工更主动参与
```

但是它也存在反向循环：如果没有真实使用和买家，矿工得到的只是一个每天继续增发 144 个左右、无人承诺回购的社会共识资产。

---

## 三、真实的申领规则：手续费竞争和矿工优势分别是什么

### 1. Bitmap 归属取决于“第一个有效铭文”，不是谁挖出了原始 Bitcoin 区块

Bitmap 不是 Bitcoin 共识中的 coinbase 附加字段。矿工挖出区块 N，并不会自动拥有 `N.bitmap`。必须另行创建 Ordinals 铭文；Bitmap 索引器再依据 `first-is-first` 规则识别最早有效申领。[16][18][21]

Ordinals 对铭文编号的顺序，首先按 reveal 交易在区块中的出现顺序，再按同一交易中 envelope 的出现顺序。[2] 因而两个人竞争同一个 `{N}.bitmap` 时，决定性因素是**哪一个有效 reveal 在链上排序更早**。

### 2. 普通 bot 为什么靠高费率抢

普通 bot 不控制区块模板，只能：

- 提前创建可滚动的 commit；
- 看到新区块后立即广播 reveal；
- 用高费率、直连矿池、交易加速器或私有通道提高入块速度；
- 争取让自己的 reveal 在竞争交易之前。

因此“高费率拿 Bitmap”只是开放 mempool 环境下的经验性结果，而不是 Bitmap 或 Bitcoin 的硬规则。Bitcoin 还存在带外费用/加速服务，支付可能不完整地体现在链上交易费中，这进一步说明单看 fee rate 不能等同于最终排序权。[20]

### 3. 模板控制者为什么拥有结构性优势

模板控制者可以：

- 直接纳入自己有效但极低费率、甚至不经公共 mempool 的交易；
- 把自己的 reveal 排在其他 Bitmap 竞争 reveal 之前；
- 排除或延后竞争者；
- 在同一候选区块中按依赖顺序放入 commit 和 reveal；
- 或者更稳妥地，在上一个区块就准备好下一个高度的 commit。

BIP 22 明确矿工可以定制和组装完整区块结构；Bitcoin Developer Guide 也说明 solo mining 软件根据模板构造候选区块。[3][4]

传统矿池下，ASIC 算力提供者通常并不拥有这项选择权；它取决于矿池协议、模板控制和双方收益合同。[5]

Stratum V2 的 Job Declaration 才为具体矿工自选交易集提供标准化路径。[19]

### 4. 链上样本证明：当前 bot 已经在做“滚动式预承诺”

本地对高度 `961600–961714` 的链上扫描中，找到 82 个对应新区块高度的 `.bitmap`：

- 77 个在对应高度的同一个区块内 reveal；
- 5 个延迟到下一个区块 reveal；
- 多个连续申领使用同一地址和滚动 UTXO 链。

例如 `961685.bitmap` 在高度 961685 确认，reveal 手续费仅 168 sats；其输入来自上一区块中确认的一笔交易，而那笔交易本身 reveal 了 `961684.bitmap`，同时生成下一次 commit 输出。[9][10][22]

这说明：

- 现在的 bot 并不是每次新区块出现后从零开始；
- 它在前一轮 reveal 时，已经为下一高度准备了新的 Taproot commit；
- 新高度出现后只需迅速广播下一笔 reveal；
- **矿池若参与，可以在这套滚动链之上再增加模板排序权，因此优势更大。**

---

## 四、它能不能成为后补贴时代的安全预算？先算数量级

### 1. 当前差距极大

截至本次取数，Bitcoin 高度约 962,508，区块补贴为 3.125 BTC；BTC 约 62,993 美元，因此单块补贴约 **196,853 美元**。[7][8]

我们今天重新抓取的 Bitmap 成交显示：

- Satflow 近 24 小时中位成交约 **7,200 sats，约 4.54 美元**；
- OKX Web3 近 24 小时中位成交约 **0.000058 BTC，约 3.65 美元**；
- 近期主成交中心仍基本处于用户观察到的 **3–5 美元**。[14][15]

按每天约 144 个新区块计算：

- 若每个新 Bitmap 值 5 美元：年新增价值约 **26.28 万美元**；
- 当前 Bitcoin 全网每天仅补贴约 **2,835 万美元**；
- 5 美元 Bitmap 对当前补贴的贡献约 **0.0025%**，几乎可以忽略。

### 2. 什么时候开始“有感”？

在 BTC 价格不变的静态假设下：

| 约略减半阶段 | 单块补贴 | 按 62,993 美元/BTC 的单块价值 |
|---|---:|---:|
| 当前 | 3.125 BTC | 196,853 美元 |
| 2028 后 | 1.5625 BTC | 98,427 美元 |
| 2032 后 | 0.78125 BTC | 49,213 美元 |
| 2040 后 | 0.1953125 BTC | 12,303 美元 |
| 2048 后 | 0.048828125 BTC | 3,076 美元 |

因此：

- 新 Bitmap 稳定值 **50 美元/块**：可成为小型矿池愿意顺手自动申领的边际收入，但不是安全预算核心。
- **500 美元/块**：年新增价值约 2,628 万美元，开始成为有统计意义的附加业务。
- **5,000 美元/块**：年新增价值约 2.628 亿美元，在远期低补贴时代可能成为部分矿工的重要补充。
- **50,000 美元/块以上**：才进入能明显影响全网模板竞争、矿池商业模式和安全预算讨论的区域。

问题是：若近百万个 Bitmap 的平均价值达到 5,000 美元，粗略总价值就是约 **48 亿美元**；达到 50,000 美元，则接近 **480 亿美元**。这不是数学上不可能，但必须有全球级产品、文化共识和持续需求支撑。

### 3. Bitmap 不能单独“替代区块补贴”的根本原因

1. **不是 Bitcoin 共识收入。** 节点不会检查矿工是否取得或出售 Bitmap。
2. **没有强制买家。** 交易费是用户为了结算而支付；Bitmap 价值取决于二级市场是否愿意接盘。
3. **收入非同质、难分配。** 一个 Bitmap 是独立 NFT/铭文，不像 BTC 手续费可立即按 PPS/FPPS 结算。
4. **矿池和算力提供者可能利益冲突。** 模板由矿池控制时，Bitmap 到底属于池、实际找到 share 的矿工，还是全体参与者，必须另立合同。
5. **高度持续扩张。** Bitmap 每年约新增 52,560 个；必须让新增使用需求长期超过新增供给。
6. **极端高价值会带来重组激励。** 如果某个新区块 Bitmap 或其他区块内容的外部价值远超正常奖励，矿工可能更愿意争夺、重挖或通过私有交易获取该价值，反而增加 fee sniping/短重组风险，而不一定纯粹增强稳定性。

Bitcoin 最终仍需要由链上交易费、结算需求、带外支付以及可能出现的其他矿工可提取价值共同构成安全预算；到约 2140 年，协议补贴归零，矿工收入将主要依靠交易费。[6]

---

## 五、极端乐观情景：Bitmap 真正“撑起一杆旗”会是什么样

### 阶段 A：矿池工具化

- 一个 Bitmap SDK 可直接集成到 Bitcoin Core/Stratum V2 模板构造器。
- 矿池自动为下一高度建立 commit，并在自己出块时优先 reveal。
- 每个新 Bitmap 自动进入矿池 treasury，或铸造成可拍卖的“block deed receipt”。
- 矿池公开规则：Bitmap 收益按算力、找到区块的设备、池 treasury 三方分配。

### 阶段 B：产权与生产者叙事成立

市场开始接受：

> 出块者创造了 Bitcoin 的新时间坐标，因此对该坐标对应的 Bitmap 拥有天然首申领权。

这会把 Bitmap 从“bot 抢的便宜铭文”，升级为“每个 Bitcoin 区块的生产者凭证”。即使这不是 Bitcoin 原生共识，它也可能成为强社会共识。

### 阶段 C：真实用途扩张

- Bitmap 成为 AI Agent、组织、服务和数字资产的长期公共坐标；
- 高价值区块用于根身份、证明、记忆、应用入口和自治组织；
- 非持有者也能直接使用 Bitmap 锚定的产品；
- 矿池不只是出售地块，而是提供初始化、解析、托管、建设和连续性服务。

### 阶段 D：价格发现与矿业结合

- 普通区块 Bitmap 有稳定拍卖价；
- 特殊高度、难度调整、减半、历史事件区块产生显著溢价；
- 矿池财报出现“BTC subsidy + fees + digital block property”三类收入；
- 矿工为了争取高价值特殊区块，把 Bitmap、rare sat、铭文排序权和带外交易一起纳入模板收益优化。

### 极端乐观估值框架

以约 96.25 万供应、当前 4 美元粗略地板计，地板总值仅约 **385 万美元**。若未来 Bitmap 成为 Bitcoin 原生公共坐标：

- 1,000 万美元总值：约 **2.6 倍**；
- 1 亿美元：约 **26 倍**；
- 10 亿美元：约 **260 倍**；
- 50 亿美元：约 **1,300 倍**。

这只是“地板 × 总供应”的粗略敏感性，不是可实现的清算市值；大量早期特殊 Bitmap 不会按地板出售，市场深度也远不足以支持全部供应按同一价格退出。

---

## 六、如果不是 Bitmap，什么更直接捕获同一命题？

### 1. Rare/epic/uncommon sats：技术上更纯粹的“矿工额外产物”

Ordinal Theory 定义：每个区块的第一个 sat 是 uncommon；每个难度调整周期的第一个 sat 是 rare；每个减半周期的第一个 sat 是 epic。[12]

它们比 Bitmap 更直接，因为：

- 新 sat 就在 coinbase 中诞生，天然由出块方产生；
- 无需另外抢注 `.bitmap`；
- 稀缺度由区块结构直接决定，而不是社会共识命名规则。

但缺点是：

- 普通 uncommon sat 同样每块新增一个；
- 从矿池 coinbase、收益分配和 UTXO 中精确隔离并交付，需要专门的 sat 管理；
- 市场流动性和估值透明度可能比 Bitmap 更差。

**如果押注“未来矿工不只挖 BTC，也挖区块原生收藏品”，rare/epic sats 是比 Bitmap 更纯的技术表达；Bitmap则是更容易理解、使用和建设的产品表达。**

### 2. BTC：最稳健地捕获安全预算增长

无论未来收入来自交易费、Ordinals、Runes、Bitmap、带外支付还是新应用，最终都要依赖 Bitcoin 的结算和 PoW 安全。BTC 不一定回报倍数最大，但成功概率、流动性和抗单一叙事失败能力最高。

### 3. 矿业股/矿池/ASIC 基础设施

它们直接捕获矿工收入多元化，但同时承担：电价、设备折旧、融资、运营、监管、股权稀释和周期性风险。若 Bitmap 只是矿工收入中很小的一项，矿业股不会因为 Bitmap 单独获得巨大重估。

### 4. Ordinals 顶级文化资产

顶级 Ordinals 收藏品捕获的是 Bitcoin 数字文物和文化溢价，供应固定、品牌更集中；但它们与“每个新区块都产生一种矿工权益”的关系较弱。

### 5. Runes/BRC-20

它们更可能通过频繁交易直接制造手续费，因此对矿工安全预算的作用更现实；但代币数量、叙事替换和流动性轮动很快，单一资产难以永久捕获整个协议带来的费用价值。

---

## 七、站在 2026-08-15，什么潜在回报率最大？

需要区分三个答案：

### A. 最高成功概率：BTC

如果目标是十年以上不依赖单一团队或单一社会共识的复利，BTC 仍是第一选择。

### B. 最高极端回报弹性：低价 Bitmap，但必须是“风险资本仓”

Bitmap 当前成交中心约 3–5 美元、粗略地板总值只有数百万美元；只要进入一亿美元级社会共识，理论倍数就很大。它具备：

- 与 Bitcoin 区块一一对应；
- 每十分钟扩张一次；
- 可被矿工模板权天然捕获；
- 可承载公共坐标、可信根、AI/组织入口；
- 当前价格对上述任何成功几乎没有充分计价。

因此，**在“可归零的高风险资产”中，我认为 Bitmap 是潜在回报率最大的 Bitcoin-native 方向之一。** 但这不等于应该把大部分资产换成 Bitmap。

### C. 最纯的“矿工新产物”押注：rare/epic sats

如果只押矿工未来挖的不只是 BTC，而是区块原生稀缺物，那么 rare/epic sats 的逻辑更硬；但入场价格、真伪验证、托管和流动性都更苛刻。

## 八、我的实际配置建议

若新增资金专门用于这个命题，而不是整个家庭资产：

| 桶 | 比例 | 作用 |
|---|---:|---|
| BTC | **60%–75%** | 安全预算与 Bitcoin 总生态的基本盘 |
| Bitmap | **15%–25%** | 最高凸性的右尾期权 |
| Rare/特殊 sats 或顶级 Ordinals | **5%–10%** | 区块原生稀缺和文化资产 |
| 现金/机动 | **5%–10%** | 等待矿池试点、协议采用或流动性事件 |

对 Bitmap 内部，不建议只机械扫最便宜地板：

1. **普通低价地板**：用于获得广泛 beta，分散买，不追突然拉升。
2. **有时间/历史/结构意义的区块**：减半、难度调整、早期区块、重大 Bitcoin 事件、特殊交易结构。
3. **已建设坐标**：像 `7187.bitmap` 这样有公开解析、产品、签名和真实使用历史的地块。
4. **保留新矿工叙事观察仓**：一旦出现矿池公开申领、分配或拍卖 Bitmap，应视为一级催化剂，而不是普通社区新闻。

## 九、需要持续验证的五个领先指标

1. 是否出现矿池或 Stratum V2 模板组件自动申领 `.bitmap`。
2. 新区块 Bitmap 的申领地址是否从单一 bot 转向多个矿池标签地址。
3. 普通新区块 Bitmap 的首发拍卖价是否稳定高于申领和管理成本。
4. Bitmap 的非投机使用：AI 自动解析、服务调用、组织根、证明和应用数量是否增长。
5. 每年约 52,560 个新增 Bitmap 的需求能否被真实用户吸收，而不是库存持续堆积。

## 十、最终判断

> **你的脑暴不是空想，技术骨架是真的：控制区块模板的一方确实可以比公共 bot 更可靠地优先取得新区块对应的 Bitmap。**

但准确的投资结论是：

> **Bitmap 目前不是 Bitcoin 的第二区块奖励，而是一张“有机会被矿业体系认领为第二奖励”的超长期看涨期权。**

它最诱人的地方，正是当前几美元的价格与极端乐观情景之间存在巨大非线性；它最危险的地方，也是收益并非 Bitcoin 共识保证，必须靠社会共识、产品使用、市场流动性和矿工采纳共同兑现。

在今天这个时点，我会选择：

> **BTC 做主仓；Bitmap 做最重要的高凸性卫星仓；rare/epic sats 做更纯但更小的矿工原生稀缺仓。不要因为“理论上可能撑起安全预算”，把尚未形成现金流的 Bitmap 当成已经实现的矿工收益。**

## Sources

[2] https://docs.ordinals.com/inscriptions.html — Ordinal Theory Handbook: Inscriptions
[3] https://raw.githubusercontent.com/bitcoin/bips/master/bip-0022.mediawiki — BIP 22 getblocktemplate
[4] https://developer.bitcoin.org/devguide/mining.html — Bitcoin Developer Guide: Mining
[5] https://braiins.com/blog/past-and-future-of-bitcoin-mining-protocols-stratum-v2-overview — Braiins: Stratum V2 overview
[6] https://river.com/learn/what-will-happen-after-all-bitcoin-mined — River: After all bitcoin are mined
[7] https://api.blockchair.com/bitcoin/stats — Blockchair Bitcoin network stats
[8] https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd&include_market_cap=true&include_24hr_change=true — CoinGecko Bitcoin price API
[9] https://ordinals.com/r/inscription/c201fed451c4bc532eee3b7065eb5135ec81f69d2536f9b9b91e37c2155a33dfi0 — Ordinals inscription 961685.bitmap
[10] https://blockstream.info/api/tx/c201fed451c4bc532eee3b7065eb5135ec81f69d2536f9b9b91e37c2155a33df — Blockstream transaction 961685.bitmap
[12] https://docs.ordinals.com/overview.html — Ordinal Theory Handbook: Overview and rarity
[14] https://api.satflow.com/v1/activity/sales?collectionSlug=bitmap&timeRange=30d&external=false — Satflow Bitmap sales API
[15] https://web3.okx.com/priapi/v1/nft/stats/collection/sales?projectId=2462760&chain=0 — OKX Web3 Bitmap sales API
[16] https://gitbook.bitmap.land/bitmap-theory-whitepaper.md — Bitmap Theory Whitepaper
[18] https://gitbook.bitmap.land/bitmap-theory-whitepaper/theory.md — Bitmap Theory Whitepaper: Theory
[19] https://stratumprotocol.org/specification/06-job-declaration-protocol — Stratum V2 Job Declaration Protocol
[20] https://bitcoinops.org/en/topics/out-of-band-fees — Bitcoin Optech: Out-of-band fees
[21] https://gitbook.bitmap.land/readme.md — Bitmap 101
[22] https://blockstream.info/api/tx/2e87e46cc3c5d888216a06f4e54a347a55acdb95fb34ddf0f00506f0936ca805 — Blockstream prior transaction in rolling Bitmap chain
