# 3.如何制作元数据在链上的 NFT - 使用 Hardhat 和 JavaScript

> 本文是中文翻译，原文地址：
> [https://docs.alchemy.com/docs/how-to-make-nfts-with-on-chain-metadata-hardhat-and-javascript](https://docs.alchemy.com/docs/how-to-make-nfts-with-on-chain-metadata-hardhat-and-javascript)

在创建 NFT 时，**将元数据存储在中心化对象存储服务**或像 **IPFS** 这样的去中心化数据储存解决方案都是很好的做法，从而避免直接在链上存储大量数据（如图像和 JSON 对象）因而产生的昂贵 Gas 费用。

**但这种做法有一个问题：**

不在区块链上存储你的元数据，智能合约就不能直接与它互动（译著：例如在不需要花费 Gas 更新元数据的情况下，在 NFT 上动态显示智能合约的数据），因为区块链**不能与 “外部世界” 沟通**。

如果我们想直接从智能合约中更新元数据，就需要在链上存储它，但是 Gas 费用怎么办？

好在，像 Polygon 这样的二层链在这里发挥了作用，**极大地降低了 Gas 成本**，并引入了许多优势，使开发人员能够扩展其应用程序的功能。

在本教程中，你将学习如何**创建一个基础的区块链游戏**，开发一个完全动态的 NFT，其链上元数据根据你与它的互动而变化，并在 **Polygon Mumbai** 上部署它，以降低 Gas 费用。

**更确切地说，你会学到：**

- 如何在链上存储 NFT 元数据
- 什么是 Polygon，为什么降低 Gas 费用很重要。
- 如何在 Polygon Mumbai 上部署
- 如何处理和在链上存储 SVG 图像和 JSON 对象
- 如何根据你与 NFT 的互动来修改你的元数据

**你也可以参考视频教程：**

(YouTube Video Here)

在深入了解代码和开发这个动态 NFT 智能合约之前，我们需要先了解几件事：

- **什么是 Polygon**
- 为什么我们会使用它

另外，请在[这里](https://alchemy.com/?a=RTW3-Week-3)注册一个 Alchemy 账户，我们稍后会需要它来进行挑战。

让我们开始吧！

## Polygon PoS - 较低的 Gas 费用和较快的交易速度

Polygon 是一个**去中心化的 EVM 兼容的扩展平台**，使开发者能够在不牺牲安全性的情况下，以较低的交易费用构建可扩展、良好的用户体验的去中心化应用（DApps）。

它属于一组被描述为**二层链（L2）**的链，这意味着它们建立在以太坊之上，以解决以太坊的一些问题 —— 同时依靠它来运作。

众所周知，以太坊既不快也不便宜，在它的链上部署智能合约可能会很快就变得非常昂贵，这就是 **Polygon** 或 **Optimism** 等二层解决方案发挥作用的地方。

例如，Polygon 有两个主要优势：

- **更快的交易处理速度** (65,000 tx/秒 vs ~14)
- 每笔交易的 Gas 成本比以太坊**低约 10,000 倍**。

而第二正是我们将连同元数据的 NFT 智能合约部署到 Polygon 上的原因。如果我们在以太坊上储存元数据，我们可以预料得到每笔交易需要数百美元的 Gas 费用，**但在 Polygon 上，它的成本不会超过几分钱**。

如果你想深入了解 Polygon 和其他二层链是如何降低交易成本和加快交易速度的，我建议你去看看[这份指南](https://www.one37pm.com/nft/what-are-layer-2-solutions-and-why-are-they-important)。

现在我们简单了解了二层解决方案所带来的好处，以及为什么在这种情况下使用 Polygon 是一个改变游戏规则的方法 —— 现在就设置我们的钱包与 Polygon Mumbai 的连接，并获得一些免费的 Matic，我们稍后需要用它来支付 Gas 费用。

## 添加 Polygon Mumbai 到你的 Metamask 钱包

首先，让我们**将 Polygon Mumbai 添加到 Metamask 钱包里**。

使用浏览器打开 [mumbai.polygonscan.com](https://mumbai.polygonscan.com/)并滚动到页面底部，你会看到**“添加 Polygon 网络” 的按钮**，点击它并确认你想把它添加到 Metamask 中。

![1286](https://files.readme.io/f3d1895-mumbpoly.png)

就是这么简单，你现在就可以把 Polygon Mumbai 加到你的 Metamask 钱包里了！🎉

现在 Metamask 已经连接到 Polygon Mumbai 网络，我们需要**一些测试 MATIC** 来支付 Gas 费用。

## 获取免费的 Matic 来部署你的 NFT 智能合约

获得测试 MATIC 是非常简单的，只需使用浏览器访问以下其中一个水龙头：

- [mumbaifaucet.com](https://mumbaifaucet.com/)
- [faucet.polygon.technology](https://faucet.polygon.technology/)

将钱包地址复制到文本栏，并点击 **“Send Me MATIC”**。

![2028](https://files.readme.io/3247953-SendMatic.png)

稍等一会后，你就会看到 MATIC 出现在 Metamask 钱包里。

在不登录的情况下，你最多可以获得 1 个 MATIC，如果有 [Alchemy 账户](https://alchemy.com/?a=RTW3-Week-3)，则可以获得 5 个。

**太好了**！钱包已经准备好了，是时候创建我们的项目，开发动态的 NFT 智能合约，并开始与之互动了！

## 如何制作元数据储存在链上的 NFT - 项目设置

打开终端，创建一个名为 “ChainBattled” 的文件夹，通过运行以下命令**安装 Hardhat**。

    yarn add hardhat

> 译著：也可以运行 `npm install hardhat`

然后初始化 Hardhat 以创建项目模板：

    npx hardhat

选择 “Create a basic sample project” 并确认后续的所有选项。

![1270](https://files.readme.io/fb720fa-sampleProj.png)

现在我们需要安装 **OpenZeppelin** 包，以获得 [ERC721 智能合约](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721) 标准，我们将用它作为模板来构建我们的 NFT 智能合约。

安装 OpenZeppelin 智能合约库：

    yarn add @openzeppelin/contracts

> 译著：也可以运行 `npm install @openzeppelin/contracts`

很好！我们现在已经安装好制作元数据储存在链上的 NFT 所需要的一切 🔥

让我们清理和修改一下项目模板并创建动态 NFT 智能合约。

不过首先，我们需要**修改 hardhat.config.js 文件，**与 Polygon Mumbai 和 Polygonscan（译著：Polygon 的区块链浏览器） 连接 —— 我们以后会需要它来验证代码（译著：让智能合约的代码在 Polygonscan 上开源）。

## 修改 hardhat.config.js 文件

让我们在 VSCode 或你最喜欢的代码编辑器中打开项目，**删除 contracts 文件夹中的 Greeter.sol 文件**，**以及 scripts 文件夹中的 test-deploy.js 文件**。

下一步是将 Hardhat 连接到 Polygon Mumbai。打开包含在项目根目录的 **hardhat.config.js** 文件，在 module.exports 对象内，复制以下代码：

    module.exports = {
      solidity: "0.8.10",
      networks: {
        mumbai: {
          url: process.env.TESTNET_RPC,
          accounts: [process.env.PRIVATE_KEY]
        },
      },
    };

当我们部署智能合约时，我们还会通过 mumbai.polygonscan.com 来验证和开源合约，所以我们需要向 Hardhat 提供一个 etherscan，或者，在这案例中，**Polygonscan 的 API 密钥**。

我们稍后会获取 Polygonscan 的 API 密钥，目前，只需在 hardhat.config.js 文件中添加以下代码：

    etherscan: {
      apiKey: process.env.POLYGONSCAN_API_KEY
    }

现在，**你的 hardhat.config.js 文件看起来应该是这样的：**

    require("dotenv").config();
    require("@nomicfoundation/hardhat-toolbox");

    module.exports = {
      solidity: "0.8.10",
      networks: {
        mumbai: {
          url: process.env.TESTNET_RPC,
          accounts: [process.env.PRIVATE_KEY]
        },
      },
      etherscan: {
        apiKey: process.env.POLYGONSCAN_API_KEY
      }
    };

配置文件已经准备就绪，让我们开始开发智能合约吧！

## 开发智能合约：元数据储存在链上的 NFT

在 contracts 文件夹中，创建一个新文件，并将其称为 “ChainBattles.sol”。

一如既往，我们需要指定 **SPDX-Licence-Identifier** 和 **pragma**，并从**OpenZeppelin** 导入几个库，我们将使用这些库作为我们智能合约的基础。

    // SPDX-License-Identifier: MIT

    pragma solidity ^0.8.0;

    import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
    import "@openzeppelin/contracts/utils/Counters.sol";
    import "@openzeppelin/contracts/utils/Strings.sol";
    import "@openzeppelin/contracts/utils/Base64.sol";

在本例中，我们导入了：

- **ERC721URIStorage 合约**，将作为我们 ERC721 智能合约的基础。
- **counters.sol 库**，将负责处理和存储 tokenID。
- **string.sol 库**，实现 `toString() ` 函数，将数据转换为字符串 —— 字符序列。
- **Base64** 库，正如我们之前看到的，将帮助我们处理 base64 数据，如我们的链上 SVG。

接下来，我们来初始化合同。

## 初始化智能合约

首先，我们需要创建一个新的合约，**继承我们从 OpenZeppelin 导入的 ERC721URIStorage** 扩展，可以通过使用 “is” 关键字来做到这一点。

    contract ChainBattles is ERC721URIStorage {}

在合约里面，**初始化字符串和计数器库：**

    contract ChainBattles is ERC721URIStorage {
        using Strings for uint256;
        using Counters for Counters.Counter;
    }

本例中，`using Strings for uint256` 意味着我们将 Strings 库中的所有方法关联到 uint256 类型。了解更多关于[将库关联到类型](https://forum.openzeppelin.com/t/what-does-this-mean-using-strings-for-uint256-in-erc721-contracts/7964)。

这同样适用于 `using Counters for Counters.Counter` 这一行，[你可以在 OpenZeppelin 论坛上阅读更多的信息](https://forum.openzeppelin.com/t/what-does-this-mean-using-strings-for-uint256-in-erc721-contracts/7964)。

初始化了这些库后，定义一个新的 tokenIds 函数，我们会用它来存储 NFT IDs。

    contract ChainBattles is ERC721URIStorage {
        using Strings for uint256;
        using Counters for Counters.Counter;
        Counters.Counter private _tokenIds;
    }

我们需要声明的最后一个全局变量是 tokenIdToLevels [mapping](https://www.tutorialspoint.com/solidity/solidity_mappings.htm)，将用它来存储与 tokenId 关联的 NFT 级别。

    mapping(uint256 => uint256) public tokenIdToLevels;

该 mapping 将**连接一个 uint256，即 NFTId，到另一个 uint256**，即 NFT 的级别。

接下来，我们需要定义我们的智能合约的构造函数。

    constructor() ERC721 ("Chain Battles", "CBTLS"){
    }

到现在为止，你的代码应该像这样：

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
    import "@openzeppelin/contracts/utils/Counters.sol";
    import "@openzeppelin/contracts/utils/Strings.sol";
    import "@openzeppelin/contracts/utils/Base64.sol";

    contract ChainBattles is ERC721URIStorage  {
        using Strings for uint256;
        using Counters for Counters.Counter;
        Counters.Counter private _tokenIds;

        mapping(uint256 => uint256) public tokenIdToLevels;

        constructor() ERC721 ("Chain Battles", "CBTLS"){
        }
    }

现在我们有了 NFT 智能合约的基础，即将需要实现 4 个不同的功能：

- **generateCharacter** —— 生成并更新我们的 NFT 的 SVG 图像。
- **getLevels** —— 获取 NFT 的当前级别
- **getTokenURI** —— 获取一个 NFT 的 TokenURI。
- **mint** —— 当然，铸造函数
- **train** —— 训练一个 NFT 并提高其级别

让我们从第一个函数开始，该函数会接受一个 SVG，将其转换为 Base64 格式的数据，并将其储存在链上  —— 但首先，我们应该简单介绍一下为什么可以在链上储存 SVG 图像，以及它们与 Base64 数据的关系。

## 什么是 SVG 以及为什么它们很重要

SVG 文件是可扩展矢量图形文件的简称，是一种标准的图形文件类型，用于在互联网上呈现二维图像。与其他流行的图像文件格式不同，SVG 格式将图像存储为矢量，这是一种由点、线、曲线和基于数学公式的形状组成的图形类型。

SVG 文件是用 XML 编写的，这是一种用于存储和传输数字信息的标记语言。SVG 文件中的 XML 代码定义了构成图像的所有形状、颜色和文本。

    <svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet" viewBox="0 0 350 350">
       <style>.base { fill: white; font-family: serif; font-size: 14px; }</style>
       <rect width="100%" height="100%" fill="black" />
       <text x="50%" y="40%" class="base" dominant-baseline="middle" text-anchor="middle">Warrior</text>
       <text x="50%" y="50%" class="base" dominant-baseline="middle" text-anchor="middle">Levels: getLevels(tokenId)</text>
     </svg>

SVG 最酷的地方在于，它们可以被：

- **容易修改并使用代码生成**
- **很容易转换为 Base64 数据**

现在，你可能想知道为什么我们要把 SVG 文件转换成 Base64 格式的数据，答案非常简单：

你可以在浏览器中显示 Base64 的图片，而不需要托管商。

让我们以这张图片为例：

![1818](https://files.readme.io/3df2833-Screenshot_2022-05-17_at_03.34.25.png)

复制以下代码然后在你的浏览器 URL 栏中粘贴，将显示相同的图像：

    data:image/svg+xml;base64,IDxzdmcgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiBwcmVzZXJ2ZUFzcGVjdFJhdGlvPSJ4TWluWU1pbiBtZWV0IiB2aWV3Qm94PSIwIDAgMzUwIDM1MCI+CiAgICAgICAgPHN0eWxlPi5iYXNlIHsgZmlsbDogd2hpdGU7IGZvbnQtZmFtaWx5OiBzZXJpZjsgZm9udC1zaXplOiAxNHB4OyB9PC9zdHlsZT4KICAgICAgICA8cmVjdCB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBmaWxsPSJibGFjayIgLz4KICAgICAgICA8dGV4dCB4PSI1MCUiIHk9IjQwJSIgY2xhc3M9ImJhc2UiIGRvbWluYW50LWJhc2VsaW5lPSJtaWRkbGUiIHRleHQtYW5jaG9yPSJtaWRkbGUiPldhcnJpb3I8L3RleHQ+CiAgICAgICAgPHRleHQgeD0iNTAlIiB5PSI1MCUiIGNsYXNzPSJiYXNlIiBkb21pbmFudC1iYXNlbGluZT0ibWlkZGxlIiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5MZXZlbHM6IGdldExldmVscyh0b2tlbklkKTwvdGV4dD4KICAgICAgICA8L3N2Zz4=

你可以注意到你粘贴的代码不是一个普通的 URL，它实际上是由两部分组成的：

- **数据指令** - 告诉浏览器如何处理数据 `data:image/svg+xml;base64,`
- **Base64 数据** - 包含实际的数据

这很有用，因为即使 Solidity 不能处理图像，它也能处理字符串，而 SVG 并不是什么别的东西，只是标签和字符串的序列，我们可以很容易地在运行时检索，另外，将所有东西转换为 base64，将允许我们在链上储存图像，而不需要额外的对象存储。

现在我们解释了为什么 SVG 很重要，让我们学习如何生成自己的链上 SVG 并将其转换为 Base64 格式的数据。

## 创建 generateCharacter 函数来创建 SVG 图像

我们需要一个函数来\*\*在链上生成 NFT 图像，通过 SVG 代码来显示 NFT 的级别。

在 Solidity 中这样做会有点麻烦，所以我们先复制下面的代码，然后将解释它的不同部分：

    function generateCharacter(uint256 tokenId) public returns(string memory){

        bytes memory svg = abi.encodePacked(
            '<svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet" viewBox="0 0 350 350">',
            '<style>.base { fill: white; font-family: serif; font-size: 14px; }</style>',
            '<rect width="100%" height="100%" fill="black" />',
            '<text x="50%" y="40%" class="base" dominant-baseline="middle" text-anchor="middle">',"Warrior",'</text>',
            '<text x="50%" y="50%" class="base" dominant-baseline="middle" text-anchor="middle">', "Levels: ",getLevels(tokenId),'</text>',
            '</svg>'
        );
        return string(
            abi.encodePacked(
                "data:image/svg+xml;base64,",
                Base64.encode(svg)
            )
        );
    }

第一件事你应该注意到的是 **bytes** 类型，这是一个动态大小的数组，最多 32 字节，你可以存储字符串和整数。

另一方面，如果你想更深入地了解 Bytes，我真的建议你去阅读 [Jean Cvllr](https://jeancvllr.medium.com/?source=post_page-----9d88fdb22676--------------------------------) 的这份指南。

在本例中，我们用它来存储代表我们 NFT 图像的 SVG 代码，由于 **abi.encodePacked()** 函数将一个或多个变量转化为字节数组，并将它们编码为 abi。

你可以在 Solidity 文档中阅读更多关于 [abi 全局 Solidity 对象和 encode 函数的信息。](https://docs.soliditylang.org/en/latest/abi-spec.html#non-standard-packed-mode)

正如你所注意到的，SVG 代码取自 `getLevels()` 函数的返回值，并使用它来填充 “Levels: ” 属性 —— 我们将在稍后实现这个函数，但请注意，**你可以使用函数和变量来动态地改变你的 SVG**。

正如我们之前所看到的，为了在浏览器上实现图像的可视化，我们需要有它的 base64 版本，而不是字节版本 —— 此外，我们还需要在前面加上 `data:image/svg+xml;base64,` 字符串，以便向浏览器说明 Base64 字符串是一个 SVG 图像，以便让浏览器知道如何打开它。

为此，在上面的代码中，我们使用 `abi.encodePacked()` 函数，将 SVG 的编码版本用 `Base64.encode()` 转为 Base64 的格式，并预留了浏览器规范字符串。

现在我们已经实现了生成图像的函数，接下来需要实现一个函数来\*\*获取我们的 NFT 的等级。

### 创建 getLevels 函数来获取 NFT 的等级

为了获得 NFT 的等级，我们需要使用智能合约中声明的 **tokenIdToLevels mapping**，将我们想要获得级别的 tokenId 传入该函数。

    function getLevels(uint256 tokenId) public view returns (string memory) {
        uint256 levels = tokenIdToLevels[tokenId];
        return levels.toString();
    }

正如你所看到的，这是非常直接的，唯一需要注意的是 **toString()** 函数，它来自 [OpenZeppelin Strings 库](https://docs.openzeppelin.com/contracts/3.x/api/utils#Strings)，并将我们的等级，即 uint256，转换为一个字符串 —— 然后将被 `generateCharacter` 函数使用，正如我们之前看到的。

接下来，我们需要创建 **getTokenURI** 函数来生成和获取 NFT 的 TokenURI。

> 译著：tokenURI 是 ERC721 用于获取 NFT 元数据的函数

### 创建 getTokenURI 函数来生成 tokenURI

`getTokenURI` 函数将需要一个参数，即 `tokenId` 来生成图像，并建立 NFT 的名称。

像往常一样，让我们先看看代码，并通过它的不同部分：

    function getTokenURI(uint256 tokenId) public returns (string memory){
        bytes memory dataURI = abi.encodePacked(
            '{',
                '"name": "Chain Battles #', tokenId.toString(), '",',
                '"description": "Battles on chain",',
                '"image": "', generateCharacter(tokenId), '"',
            '}'
        );
        return string(
            abi.encodePacked(
                "data:application/json;base64,",
                Base64.encode(dataURI)
            )
        );
    }

首先要注意的是，我们再次使用 **abi.encodePacked** 函数，不过这次是\*\*创建一个 JSON 对象。

如果你不知道 [什么是 JSON 对象](https://www.w3schools.com/js/js_json_objects.asp)，让我们简单地说，是一系列的键和值对，一个属性的名称，和属性的值。

在本例中，“name” 的值是 “Chain Battles” 加上 “#” 和 tokenId toString()，另一方面，“image” 属性的值是**从 generateCharacter() 函数返回的值**。

最后，我们返回一个包含字节数组的字符串，代表带有 JSON 数据指示的 DataURI 的 Base64 格式的编码 —— **类似于我们对 SVG 图片的处理**。

现在我们已经创建了 **getTokenURI**，接下来需要创建一个函数来实际铸造 NFT 并初始化我们的变量 —— 让我们看看如何达到！

### 创建 Mint 函数，将元数据储存在链上的 NFT

本例中，铸造函数将有 3 个目标：

- **创建一个新的 NFT**
- **初始化等级**
- **设置 token URI**

复制以下代码：

    function mint() public {
        _tokenIds.increment();
        uint256 newItemId = _tokenIds.current();
        _safeMint(msg.sender, newItemId);
        tokenIdToLevels[newItemId] = 0;
        _setTokenURI(newItemId, getTokenURI(newItemId));
    }

像往常一样，首先递增一下 `_tokenIds` 变量的值，并将其当前值存储在一个新的 uint256 变量上，即 `newItemId`。

接下来，调用 [OpenZeppelin ERC721](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721) 库中的 `_safeMint()` 函数，传递 [msg.sender](https://medium.com/coinmonks/solidity-who-the-heck-is-msg-sender-de68d3e98454) 变量，以及当前的 id。

然后在 `tokenIdToLevels` mapping 中创建一个新项目，并将其值分配为 `0`，这意味着每一个 NFT 的等级都从这个等级开始。

最后，**通过 newItemId 和 getTokenURI() 的返回值来设置 token URI，**。

这将使 NFT 的元数据，包括图像，完全存储在链上 🔥。

这也意味着我们将能够\*\*直接从智能合约中更新元数据，让我们看看如何创建一个函数来 “训练” 我们的 NFT，提升它们的等级。

### 创建 “训练” 功能，提升你 NFT 的等级

如之前所说，现在 NFT 的元数据完全储存在链上，我们将能够直接从智能合约中与它互动。

假设我们想在 “训练” 后提高 NFT 的等级，要实现这个功能，需要创建一个训练函数，它将：

- 确保受训的 NFT 存在，并且你是它的所有者。
- 将你的 NFT 的等级提高 1
- 更新 token URI 以反映训练情况

```
function train(uint256 tokenId) public {
  require(_exists(tokenId), "Please use an existing token");
  require(ownerOf(tokenId) == msg.sender, "You must own this token to train it");
  uint256 currentLevel = tokenIdToLevels[tokenId];
  tokenIdToLevels[tokenId] = currentLevel + 1;
  _setTokenURI(tokenId, getTokenURI(tokenId));
}
```

你会注意到，我们使用 `require()` 函数检查两件事：

- 通过 ERC721 标准中的 [\_exists()](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_exists-uint256-) 函数检查 token 是否存在。
- NFT 的所有者是否 **msg.sender**（调用该函数的钱包）。

一旦这两个检查都通过了，我们就从 mapping 中获得 NFT 的当前等级，并将其递增 1。

最后，通过 `tokenId` 和 `getTokenURI()` 的返回值调用 `_setTokenURI` 函数。

现在调用 “训练” 函数将提高 NFT 的等级，这将自动反映在图像中。

**恭喜你**！你刚刚完成了将元数据储存在链上的 NFT 智能合约 🏆

下一步是在 **Polygon Mumbai** 上部署智能合约，并通过 [Polygonscan](https://mumbai.polygonscan.com/) 与它互动。要做到这一点，我们需要获得 [Alchemy](https://alchemy.com/?a=RTW3-Week-3) 和 Polygonscan 的 API 密钥。

### 部署智能合约

首先，让我们在项目的根目录下创建一个新的 `.env` 文件，并添加以下变量：

    TESTNET_RPC=""
    PRIVATE_KEY=""
    POLYGONSCAN_API_KEY=""

然后，访问 [alchemy.com](https://alchemy.com/?a=RTW3-Week-3)，创建一个新的 Polygon Mumbai 应用程序。

点击新创建的应用程序，复制 API 的 `HTTP URL`，并将 API 作为 `TESTNET_RPC` 值粘贴在我们上面创建的 `.env` 文件中。

> 译著：
>
> 建议新建一个钱包作为开发专用的钱包，原因单纯是风险管理。
>
> Hardhat 需要使用你的钱包私钥来把我们的合约签署然后部署到链上，这就跟 MetaMask 需要我们的钱包私钥来为交易签署一样，没有经过签署的交易是提交不到上链的。
>
> 签署这个动作在本地进行，HardHat 和 Alchemy 都不会知道你的私钥。
>
> 请确保包含私钥的文件或代码（这里示范的 .env）都不要上传到网络上，在网络上的任何位置都有暴露的风险。

打开你的 Metamask 钱包，点击三点 (...) 菜单 \> 账户详情 \> 并复制你的钱包私钥作为`.env` 文件中 `PRIVATE_KEY` 的值。

最后，访问 [polygonscan.com](https://polygonscan.com/)，并创建一个新账户。

![731](https://files.readme.io/4950cd8-polyscan.png)

登录后，进入 **Profile Menu**，点击 API Keys。

![191](https://files.readme.io/1ae7acc-api_pol.png)

然后点击 “添加”，给你的应用程序起个名字。

![1035](https://files.readme.io/24d5821-add.png)

现在将 Api-Key Token 复制粘到 `.env` 文件中作为 `POLYGONSCAN_API_KEY` 的值。

部署智能合约之前的最后一步，我们需要**创建部署脚本**。

## 创建部署脚本

部署脚本，正如你[在上一课](https://docs.alchemy.com/alchemy/road-to-web3/weekly-learning-challenges/2.-how-to-build-buy-me-a-coffee-defi-dapp)学到的那样，顾名思义，是用来告诉 Hardhat 如何将智能合约部署到指定的区块链上。

我们的部署脚本，在这种需求下是非常直接的：

    const main = async () => {
      try {
        const nftContractFactory = await hre.ethers.getContractFactory(
          "ChainBattles"
        );
        const nftContract = await nftContractFactory.deploy();
        await nftContract.deployed();

        console.log("Contract deployed to:", nftContract.address);
        process.exit(0);
      } catch (error) {
        console.log(error);
        process.exit(1);
      }
    };

    main();

调用 `getContractFactory`，传递智能合约的名称，**这个函数来自 Hardhat ethers 库**。然后调用 `deploy()` 函数，等待它部署成功，并且记录下部署后的合约地址。

一切都被包裹在一个 `try {} catch {}` 区块中，以捕捉任何可能发生的错误，并将其打印出来便于调试。

现在我们的部署脚本已经准备好了，是时候在 Polygon Mumbai 上编译和部署我们的动态 NFT 智能合约了。

## 编译和部署智能合约

要编译智能合约，只需使用终端，在项目的根目录运行以下命令：

    npx hardhat compile

如果一切按预期进行，你会看到你的**智能合约在项目文件夹内被编译**。

现在，让我们把智能合约部署在 Polygon Mumbai 链上运行。

    npx hardhat run scripts/deploy.js --network mumbai

稍等片刻，你应该看到智能合约的地址出现在你的终端上。

![838](https://files.readme.io/856d094-smart_contract.png)

太强了！你刚刚在 Polygon Mumbai 上部署了第一个智能合约！接下来，我们需要验证（译著：即提交合约源码）智能合约的代码，通过 Polygonscan 与之互动。

## 通过 Polygonscan 查看你的智能合约

复制刚刚部署的智能合约的地址，进入 [mumbai.polygonscan.com](https://mumbai.polygonscan.com/)，并**在搜索栏中粘贴智能合约的地址**。

进入你的智能合约页面后，点击 “Contract” 页签。

你会看到，合约代码是不可读的。

![1383](https://files.readme.io/e86495b-concode.png)

**这是因为我们还没有验证我们合约的代码**

为了验证智能合约，我们需要回到项目，并在终端运行以下代码：

    npx hardhat verify --network mumbai YOUR_SMARTCONTRACT_ADDRESS

![834](https://files.readme.io/64fcc79-verify_code.png)

有时你可能会收到 ”failed to send contract verification request“ 的错误信息  ——  只要再试几次就应该能通过。

这将使用我们在 `hardhat.config.js` 文件中添加的 Polygonscan API 密钥来验证智能合约代码。现在我们将能够通过 Polygonscan 与之互动（译著：通过 Polygonscan 网站调用合约函数），让我们试试吧。

## 通过 Polygonscan 与你的智能合约互动

现在，智能合约已通过验证，mumbai.polygonscan.com 将在其附近显示一个绿色的小勾。

![355](https://files.readme.io/80d594c-consource.png)

要与它互动并铸造第一个 NFT，在 “Contract” 页签下**点击 “Write Contract” 按钮**，并且点击 “Connect to Web3”。

![1379](https://files.readme.io/556ed26-connect_web3.png)

然后寻找 “mint“ 功能并点击 ”Write”。

![560](https://files.readme.io/a8d336c-write.png)

这将弹出一个 Metamask 窗口，要求你支付 Gas 费用，点击确定按钮。

恭喜！你刚刚铸造了你的第一个动态 NFT —— 让我们到 OpenSea Testnet 上看看它的情况。。

## 在 OpenSea 上查看你的动态 NFT

复制智能合约地址，进入 [testnet.opensea.com](https://testnets.opensea.io/)，并将其粘贴到搜索栏。

![1275](https://files.readme.io/c2bfe83-chain_jotter.png)

如果一切按预期进行，你现在应该看到你的 NFT 显示在 OpenSea 上，有它的动态图像、标题和描述。

不过到现在为止并没有什么新鲜的，因为我们已经在[第一课](https://docs.alchemy.com/alchemy/road-to-web3/weekly-learning-challenges/1.-how-to-develop-an-nft-smart-contract-erc721-with-alchemy)中建立了一个 NFT 集合。等一下，这里最酷的是，我们现在可以实时更新图像。

让我们回到 Polygonscan

## 通过 “训练” NFT 动态更新 NFT 图像

访问 [mumbai.polygonscan.com](https://mumbai.polygonscan.com/)，在 “Contract” 页签下找到 **Write Contract**，找到 “train” 函数。

输入你 NFT 的 ID: `1`，因为我们只铸造了一个，**点击 “Write”：**。

![952](https://files.readme.io/5caa7b7-nft_id.png)

然后回到 [testnets.opensea.com](https://testnets.opensea.io/)，刷新页面。

![1246](https://files.readme.io/f6bea78-chain_bottles.png)

正如你所看到的，你的 NFT 图像刚刚更新了，反映了新的等级！

**恭喜你，你的 NFT 刚刚升了级！做得好！** 🎉

现在是时候通过本周的挑战，将它提升到一个新的水平了！

## 本周的挑战

目前我们只储存了 NFT 的等级，为什么不试试储存更多东西呢？

**将当前的 `tokenIdToLevels[]` mapping 替换为一个 struct，该结构储存：**

- **等级**
- **速度**
- **强度**
- **生命**

你可以在本指南中阅读更多关于 [Struct](https://www.tutorialspoint.com/solidity/solidity_structs.htm) 的内容。

当你创建了这个结构后，就在 `mint()` 函数中初始化它们的数值，要做到这一点，你可能想研究一下如何在 [Solidity 上生成随机数字](https://blog.finxter.com/how-to-generate-random-numbers-in-solidity/)。

完成了这个项目后，请提交你的智能合约地址。
