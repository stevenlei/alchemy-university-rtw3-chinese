# 1. 如何使用 Alchemy 开发 NFT 智能合约（ERC721）？

> 本文是中文翻译，原文地址：
> [https://docs.alchemy.com/docs/1-how-to-develop-an-nft-smart-contract-erc721-with-alchemy](https://docs.alchemy.com/docs/1-how-to-develop-an-nft-smart-contract-erc721-with-alchemy)

**用 Solidity 开发智能合约并部署到区块链上**一开始听起来可能很艰巨：Solidity、安全、Gas 优化、开发环境和 Gas 费用，这些只是你在区块链上部署代码所需要经历的其中一部分。

不过幸运的是，在过去的几个月里已经有很多新的工具推出，让开发者们在开发智能合约时变得更加容易。

像 **OpenZeppelin Wizard** 这样的工具，为开发者提供了可视化工具，通过选取相关功能，即可在短时间内创建可组合和**安全的智能合约**，与 **Alchemy** 等 Web3 开发者工具一起使用，使在区块链上编写和部署代码的体验变得前所未有的简单、快速和可靠。

在本教程中，你将学习**如何使用 Alchemy、OpenZeppelin、Remix 和 Ethereum Goerli 开发和部署 ERC721（NFT）**智能合约。

更确切地说，你将学会：
- 如何使用 OpenZeppelin 和 Remix 编写和修改智能合约？
- 使用 **[https://goerlifaucet.com/](https://goerlifaucet.com/)** 获得免费的 Goerli 测试网 ETH
- 在 Ethereum Goerli 测试网上进行部署，以节省 Gas 费用。
- 使用 Filebase 在 IPFS 上托管 NFT 的元数据。
- 铸造一个 NFT 并在 OpenSea 测试网上看到它。

你也可以跟随视频教程：

(Youtube Video Here)

让我们从创建智能合约开始。

##  使用 OpenZeppelin Wizard（合约建构向导），开发 ERC721 智能合约

如前所述，在本教程中，你将**使用 OpenZeppelin Wizard（合约建构向导）来创建智能合约**，主要有两个原因：

- 它很安全。
- 它符合智能合约的业界标准。

当谈到编写智能合约时，**安全性**是关键。有大量的智能合约被利用漏洞的例子：由于安全性欠佳，恶意行为者盗取了数亿美元。

_你不会希望有人会从你部署到区块链的合约中，偷走所有珍贵的加密货币或 NFT。对吗？_

**OpenZeppelin 可以达到这个目的**，他们是智能合约标准（ERC20、ERC721 等）的最大维护者之一，允许开发者使用经过彻底审计的代码来开发可靠的合约。

要开发我们的 ERC721 NFT 智能合约，你需要做的第一件事是 [**访问 Open Zeppelin Wizard（合约建构向导）页面**](https://docs.openzeppelin.com/contracts/4.x/wizard)。

进入该页面后，你会看到以下编辑器：

![985](https://files.readme.io/9e820fe-erc721.png "erc721.png" width=auto height=auto)

点击左上角的 ERC721 按钮，选择要使用的 ERC 标准类型和你想写的合约类型：

![997](https://files.readme.io/828ee6a-Erc721_2.png "Erc721 2.png" width=auto height=auto)

现在你已经选择了合约标准，在左边的菜单上，你应该看到一些选项。

让我们改一下名称 (Name) 和代称 (Symbol)。点击文本框中的 “MyToken”，给它起个名字，对代称做同样的处理，并将 Base URI 字段留空（名称将被 OpenSea 和 Rarible 用作集合的名称）。

![516](https://files.readme.io/7c6e435-Alh_set.png "Alh set.png" width=auto height=auto)

## 选择 NFT（ERC721）的功能

现在需要选择你想集成到智能合约中的功能，在_设置 (Settings)_之后，你会看到_功能 (Features)_，在那里你可以选择不同的模块功能，以包括在你的智能合约当中。

![258](https://files.readme.io/acaa48a-Miint.png "Miint.png" width=auto height=auto)

在我们的范例中，你要选择以下功能：
- **Mintable** 将创建一个只能由特权账户调用的 Mint （铸造）函数
- **Autoincrement IDs** 将自动为你的 NFT 分配一个递增的 ID
- **Enumerable** 启用智能合约的链上令牌枚举 (on-chain Tokens enumeration) 和 “totalSupply” （总发行量）等功能，这在默认的 ERC721 集成中是没有的
- **URI Storage** 以将元数据 (metadata) 和图像关联到每一个 NFT 中

![256](https://files.readme.io/d4a7d1d-Screenshot_2022-05-01_at_17.20.48.png "Screenshot 2022-05-01 at 17.20.48.png" width=auto height=auto)

为了达到本教程的目的，也因为不需要为这个 NFT 创建任何代币经济 (Tokenomic)，所以不需要勾选以下模块：

- **Burnable** - 销毁代币
- **Pausable** - 暂停代币转移、销售等。
- **Votes** - 允许访问类似治理的功能，如代表和投票。

如果你想了解更多关于这些模块的信息，[查看 OpenZeppelin  关于 ERC721 标准的官方文档。](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721)

现在已经选择了想要的功能，OpenZeppelin Wizard 将生成智能合约代码，它应该看起来像这样：

	// SPDX-License-Identifier: MIT
	pragma solidity ^0.8.4;
	
	import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
	import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
	import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
	import "@openzeppelin/contracts/access/Ownable.sol";
	
	contract Alchemy is ERC721, ERC721Enumerable, ERC721URIStorage, Ownable {
	    constructor() ERC721("Alchemy", "ALC") {}
	
	    function safeMint(address to, uint256 tokenId, string memory uri)
	        public
	        onlyOwner
	    {
	        _safeMint(to, tokenId);
	        _setTokenURI(tokenId, uri);
	    }
	
	    // The following functions are overrides required by Solidity.
	
	    function _beforeTokenTransfer(address from, address to, uint256 tokenId)
	        internal
	        override(ERC721, ERC721Enumerable)
	    {
	        super._beforeTokenTransfer(from, to, tokenId);
	    }
	
	    function _burn(uint256 tokenId) internal override(ERC721, ERC721URIStorage) {
	        super._burn(tokenId);
	    }
	
	    function tokenURI(uint256 tokenId)
	        public
	        view
	        override(ERC721, ERC721URIStorage)
	        returns (string memory)
	    {
	        return super.tokenURI(tokenId);
	    }
	
	    function supportsInterface(bytes4 interfaceId)
	        public
	        view
	        override(ERC721, ERC721Enumerable)
	        returns (bool)
	    {
	        return super.supportsInterface(interfaceId);
	    }
	}

> 译著：Solidity 的版本可能不一样，但是只要是 0.8.\* 版本，就没有什么问题。

现在是时候复制我们的代码到 Remix IDE 上，开始修改并部署到区块链上。

## 用 Remix IDE 修改和部署你的 ERC721 智能合约

现在你拥有了属于你的 ERC721 智能合约，让我们修改并在 Goerli 测试网上部署它。为此，我们将使用 **Remix IDE**，这是一个免费的、基于浏览器的集成开发环境，专门为使用 Solidity 开发智能合约而设计。

首先，你可能已经注意到，在 OpenZeppelin Wizard 编辑器的顶部，有一个 “Open in Remix” 的按钮：

![960](https://files.readme.io/108c79e-Remix.png "Remix.png" width=auto height=auto)

点击它将在浏览器的新分页中打开 Remix IDE。

### 使用 Remix 修改 NFT 智能合约

从合约的顶部开始，有一个 “SPDX-License-Identifier”，它指定了你的代码使用何种许可类型发布 — 在 Web3 应用程序中，保持代码开源是很好的做法，因为这将确保可信度。

	// SPDX-License-Identifier: MIT

然后是 **pragma** — 你想用来编译智能合约代码的编译器版本。这个小小的 **^** 符号是告诉编译器，0.8.0 到 0.8.9 之间的每个版本都适合编译我们的代码。

	pragma solidity ^0.8.4;

然后我们要导入一堆库，初始化智能合约：

	import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
	import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
	import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
	import "@openzeppelin/contracts/access/Ownable.sol";
	import "@openzeppelin/contracts/utils/Counters.sol";

然后我们要初始化这个智能合约，继承我们从 OpenZeppelin 资源库导入的所有标准：

	contract Alchemy is ERC721, ERC721Enumerable, ERC721URIStorage, Ownable {...}

你可能注意到，`safeMint` 函数有一个 Modifier “**onlyOwner**” — 这将允许只有智能合约的拥有者（部署智能合约的钱包地址）铸造 NFT。你可能希望任何人都能铸造 NFT，要做到这一点，你需要从 `safeMint` 函数中删除 `onlyOwner`：

	function safeMint(address to, string memory uri) public {
	    uint256 tokenId = _tokenIdCounter.current();
	    _tokenIdCounter.increment();
	    _safeMint(to, tokenId);
	    _setTokenURI(tokenId, uri);
	}

你也可以把 Ownable 从合约声明部分的导入库中删除：

	import "@openzeppelin/contracts/access/Ownable.sol";

现在任何人都可以铸造我们的 NFT，但是你需要限制被铸造的数量。为了做到这一点，让我们定义一下最大供应量。

例如说，希望能够铸造出最多 10,000 个NFT。为此，让我们创建一个类型为 `uint256` 的变量，称之为 `MAX_SUPPLY`，并将其赋值为 `10,000`：

	Counters.Counter private _tokenIdCounter;
	    uint256 MAX_SUPPLY = 100000;
	
	    constructor() ERC721("Alchemy", "ALCH") {}

接下来，让我们修改 `safeMint` 函数，在第 18 行添加一个 `require` 语句：

	require(_tokenIdCounter.current() <= MAX_SUPPLY, "I'm sorry we reached the cap");

如果你想更好的理解 Solidity 中的 `require` 是什么，[请点击这里阅读官方文档的介绍](https://docs.soliditylang.org/en/v0.4.24/control-structures.html#error-handling-assert-require-revert-and-exceptions)。

现在你已经限制了 NFT 的最大供应量，是时候编译智能合约并部署到 Goerli 测试网了。要做到这一点，你需要**在 [Alchemy.com](https://alchemy.com/?a=roadtoweb3weekone) 上创建一个免费账户**，将其添加为 Metamask 的节点提供者，并[获得一些免费的 Goerli ETH](https://goerlifaucet.com/)。

> 译著：这里将介绍怎样使用 Alchemy 作为你在钱包中的 RPC（私人节点），Metamask 本身都包含了 Goerli 测试网，所以跳过这个部分都可以继续。不过了解怎样使用 Alchemy 的节点服务还是有很多益处的，它可以在公共节点繁忙的时候依然保证交易速度，所以建议跟随。

## 创建一个免费的 Alchemy 账户

首先，让我们到 [alchemy.com](https://alchemy.com/?a=roadtoweb3weekone) 点击 “登录“ 并创建一个新账户：

![2494](https://files.readme.io/56d3600-web3_eco.png "web3 eco.png" width=auto height=auto)

选择 Ethereum：

![2660](https://files.readme.io/7719099-ether_eco.png "ether eco.png" width=auto height=auto)

给你的应用程序和团队起个名字，选择 Goerli 测试网并点击创建应用程序：

![2520](https://files.readme.io/45f0d4b-Screen_Shot_2022-09-22_at_10.00.53_AM.png "Screen Shot 2022-09-22 at 10.00.53 AM.png" width=auto height=auto)

一旦完成这些步骤，就会跳转到仪表板。点击刚才建立的应用程序，**点击右上角的 “VIEW KEY” 按钮**，并复制 HTTP URL（后面会用到）。

![2491](https://files.readme.io/d2c8933-Screen_Shot_2022-09-22_at_10.13.54_AM.png "Screen Shot 2022-09-22 at 10.13.54 AM.png" width=auto height=auto)

接下来，你需要将 Alchemy 添加到 Metamask 作为 Goerli 测试网的 RPC 供应商。

## 添加 Alchemy Goerli 测试网到你的 Metamask 钱包

Metamask 安装完毕后，点击下拉菜单中的 “网络” -\> “添加网络”。

![796](https://files.readme.io/af70d3f-Screen_Shot_2022-09-22_at_1.28.09_PM.png "Screen Shot 2022-09-22 at 1.28.09 PM.png" width=auto height=auto)

跳转到以下页面，填写 Goerli 网络和 RPC URL 信息：

![2518](https://files.readme.io/6ca3599-Screen_Shot_2022-09-22_at_1.39.28_PM.png "Screen Shot 2022-09-22 at 1.39.28 PM.png" width=auto height=auto)

填写以下信息：
- **Network name:** Alchemy Goerli
- **New RPC URL:** 贴上刚才在 Alchemy 复制的 HTTP URL
- **Chain ID:** 5
- **Currency Symbol:** GoerliETH
- **Block Explorer:** [https://goerli.etherscan.io](https://goerli.etherscan.io/)

恭喜，你成功地通过 Alchemy 将 Goerli 测试网添加到了 Metamask 了！🎉

现在是时候**在 Goerli 测试网上部署我们的智能合约了**。但首先，你需要获得一些 Goerli 上的测试 ETH。

### 获得免费的Goerli测试ETH

获取 Goerli 测试 ETH 是很简单的，只需访问 [goerlifaucet.com](https://goerlifaucet.com/)，将你的钱包地址复制到文本栏，然后点击 “Send Me ETH” 按钮：

![1600](https://files.readme.io/f3d2940-goe.png "goe.png" width=auto height=auto)

大约 10-20 秒后，你会看到 Goerli ETH 出现在 Metamask 钱包中。

> 译著：最近 Goerli 测试网比较繁忙，Gas 比较高，有时候需要多试几次才能领取成功。

你可以在不登录的情况下每 24 小时获得最多 0.1 个 ETH，或者通过登陆 Alchemy 账户获得 0.5 个。

现在你有了测试 ETH，**是时候在区块链上编译和部署我们的 NFT 智能合约了。

## 在 Goerli 测试网上编译和部署 NFT 智能合约

回到 Remix，点击页面左侧的编译器菜单，点击蓝色的 “Compile” 按钮：

![357](https://files.readme.io/c0c058f-solid_compiler.png "solid_compiler.png" width=auto height=auto)

在 Environment 中选择 “Injected Web3”，然后点击 “Deploy and Run script” 按钮：

**请确保 Metamask 钱包已经连接到 Goerli 测试网**，从 Contract 的下拉选单中选择你的合约（找到你的智能合约的名称），并点击 “Deploy”：

![366](https://files.readme.io/e249393-dep.png "dep.png" width=auto height=auto)

Metamask 会弹出交易窗口，点击确定和支付 Gas 费用。

如果一切按预期进行，大约 10 秒钟后你应该看到合约已经成功部署：

![628](https://files.readme.io/6630f89-deployed_contracts.png "deployed_contracts.png" width=auto height=auto)

现在，智能合约已经部署在 Goerli 测试网上，是时候铸造我们的 NFT 了，但首先，你需要在 IPFS 上创建和上传元数据 (metadata)，让我们了解一下什么是 “元数据”。

## 什么是 NFT 元数据 (Metadata)？

![2075](https://files.readme.io/8a5f1ab-octopus.png "octopus.png" width=auto height=auto)

为了让 OpenSea 获得 ERC721 NFT 的信息（图片地址、名称、属性等），**合约需要返回一个元数据的 URI**。OpenSea、Rarible 和其他主流的市场**将通过 ERC721 URIStorage 标准中的 tokenURI 方法获得相关的元数据**。

ERC721 中的 `tokenURI` 函数应该返回一个 HTTP 或 IPFS 的地址，例如 `ipfs://bafkreig4rdq3nvyg2yra5x363gdo4xtbcfjlhshw63we7vtlldyyvwagbq`。当被查询时，这个地址应该返回一个包含你的 NFT 元数据的 JSON 数据。

> 译著：IPFS 是去中心化储存的意思。一般来说 NFT 的元数据都会储存到 IPFS 中，以确保可用性。

你可以在 [OpenSea 官方文档中](https://docs.opensea.io/docs/metadata-standards)阅读更多关于【元数据标准】的内容。

## NFT 元数据文件的格式

根据 OpenSea 文档的介绍，NFT 元数据应该存储在一个 `.json` 文件中，结构如下：

	{ 
	  "description": "YOUR DESCRIPTION",
	  "external_url": "YOUR URL",
	  "image": "IMAGE URL",
	  "name": "TITLE", 
	  "attributes": [
	    {
	      "trait_type": "Base", 
	      "value": "Starfish"
	    }, 
	    {
	      "trait_type": "Eyes", 
	      "value": "Big"
	    }, 
	    {
	      "trait_type": "Mouth", 
	      "value": "Surprised"
	    }, 
	    {
	      "trait_type": "Level", 
	      "value": 5
	    }, 
	    {
	      "trait_type": "Stamina", 
	      "value": 1.4
	    }, 
	    {
	      "trait_type": "Personality", 
	      "value": "Sad"
	    }, 
	    {
	      "display_type": "boost_number", 
	      "trait_type": "Aqua Power", 
	      "value": 40
	    }, 
	    {
	      "display_type": "boost_percentage", 
	      "trait_type": "Stamina Increase", 
	      "value": 10
	    }, 
	    {
	      "display_type": "number", 
	      "trait_type": "Generation", 
	      "value": 2
	    }
	  ]
	}

以下是对每项属性所存储内容的简要解释：

| 属性                    | 解释                                                                                                                                                                                                   |
| :-------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **image**             | 这是该项目的图像 URL。可以是几乎任何类型的图片（包括 SVG，它将被 OpenSea 缓存为 PNG），可以是 IPFS URL 或路径。我们建议使用 350 x 350 的图片。                                                                                                         |
| **image\_data**       | 如果你想即时生成图像（不推荐），在这里定义原始的 SVG 图像数据。只有在你不包括 image 参数时才使用这个。                                                                                                                                            |
| **external\_url:**    | 这是出现在 OpenSea 上图片下面的 URL，将允许用户离开 OpenSea，在你的网站上查看该项目。                                                                                                                                                |
| **description**       | 可读描述，支持 Markdown。                                                                                                                                                                                    |
| **name**              | 项目的名称                                                                                                                                                                                                |
| **attributes**        | 项目的属性，它们将显示在项目的 OpenSea 页面上。(见下文)                                                                                                                                                                    |
| **background\_color** | 项目在 OpenSea 上的背景颜色，必须是六个字符、十六进制的格式（没有前置的 # 符号）。                                                                                                                                                      |
| **animation\_url**    | 一个指向项目的多媒体附件的 URL。支持 GLTF、GLB、WEBM、MP4、M4V、OGV 和 OGG 等文件扩展名，以及纯音频扩展名 MP3、WAV 和 OGA。animation\_url 也支持 HTML 页面，允许我们使用 JavaScript canvas、WebGL 等建立丰富的体验和互动的 NFT。现在支持 HTML 页面内的脚本和相对路径。然而，不支持对浏览器扩展的访问。 |
| **youtube\_url**      | 一个指向 YouTube 视频的 URL                                                                                                                                                                                 |

现在我们对 NFT 元数据将包含哪些内容有了基本的了解，让我们学习如何创建它并将其存储在 IPFS 上。

## 在 IPFS 上创建和上传元数据

首先，访问 [filebase.com](https://filebase.com) 并创建一个新账户。

> 译著：filebase 是其中一个 IPFS 的服务商

登录之后，点击左侧菜单上的 Buckets 按钮，创建一个新的 Bucket：

![3388](https://files.readme.io/73f3bad-filebase.png "filebase.png" width=auto height=auto)

访问 Buckets，点击 **Upload** 按钮，并上传你想用于 NFT 的图片，[我将使用这里的内容](https://ipfs.filebase.io/ipfs/bafybeihyvhgbcov2nmvbnveunoodokme5eb42uekrqowxdennt2qyeculm)。 

上传后，点击它并复制 IPFS 网关的 URL（会更新到下面 JSON 代码中的 `image` 当中）：

![2894](https://files.readme.io/5bd8524-ipfs.png "ipfs.png" width=auto height=auto)

使用任何文本编辑器，**粘贴以下 JSON 代码：**

	{ 
	  "description": "This NFT proves I've created and deployed my first ERC20 smart contract on Goerli with Alchemy Road to Web3",
	  "external_url": "Alchemy.com/?a=roadtoweb3weekone",
	  "image": "更改为你的 NFT 图片的 IPFS URL",
	  "name": "A cool NFT", 
	  "attributes": [
	    {
	      "trait_type": "Base", 
	      "value": "Starfish"
	    }, 
	    {
	      "trait_type": "Eyes", 
	      "value": "Big"
	    }, 
	    {
	      "trait_type": "Mouth", 
	      "value": "Surprised"
	    }, 
	    {
	      "trait_type": "Level", 
	      "value": 5
	    }, 
	    {
	      "trait_type": "Stamina", 
	      "value": 1.4
	    }, 
	    {
	      "trait_type": "Personality", 
	      "value": "Sad"
	    }, 
	    {
	      "display_type": "boost_number", 
	      "trait_type": "Aqua Power", 
	      "value": 40
	    }, 
	    {
	      "display_type": "boost_percentage", 
	      "trait_type": "Stamina Increase", 
	      "value": 10
	    }, 
	    {
	      "display_type": "number", 
	      "trait_type": "Generation", 
	      "value": 2
	    }
	  ]
	}

> 译著：注意更改上述内容中的 `image` 的地址

并将该文件保存为 `metadata.json`。回到 Filebase，把 `metadata.json` 文件上传到我们上传图片的同一个 Bucket 里：

![3446](https://files.readme.io/6ecc402-metadata.png "metadata.png" width=auto height=auto)

最后，点击 CID 并复制它，我们将在下一部分需要它来建立 NFT 铸造时的 `tokenURI`：

![2820](https://files.readme.io/8767656-cid.png "cid.png" width=auto height=auto)

## 在 Goerli 测试网铸造你的 NFT

回到 Remix，在 Deploy & Run Transactions 菜单中，在 “Deployed contracts” 下 — 点击我们刚刚部署的合约，它将打开一个包含在智能合约里的所有方法的列表：

![578](https://files.readme.io/0a614b5-rin_nft.png "rin_nft.png" width=auto height=auto)

**橙色的按钮**是写入区块链的方法，而**蓝色的按钮**是从区块链上读取的方法。

点击 `safeMint` 方法旁边向下的图标，**粘贴以下字符串到 uri 文本框中（需要替换成实际的元数据 CID）**：

`ipfs://你的元数据的CID`

点击 transact 将会弹出一个 Metamask 窗口，提示你支付 Gas 费用。
点击确定继续，铸造你的第一个 NFT！

等待几秒钟，等待交易完成。为了确保铸造成功，在 balanceOf 文本框中输入你的钱包地址并运行，它应该显示你有 1 个 NFT（译著：这是向 NFT 合约查询指定地址 — 这里是你的地址，拥有多少个 NFT。因为铸造了一次，所以应该持有 1 个）。

在 `tokenURI` 文本框中做同样的操作，输入 `0` 作为参数（译著：默认第一个 NFT 的 ID 号码是 `0`），它应该能够显示你的 NFT 的 `tokenURI`。

非常好！你刚刚铸造了你的第一个 NFT！ 🎉

现在是时候**访问 OpenSea 测试网**检查一下元数据是否正确。

## 在 OpenSea （测试网）中查看你的 NFT

访问 [testnets.opensea.io](https://testnets.opensea.io/) （译著：这是 OpenSea 测试网，可以读取在测试网中部署的 NFT）并**使用 Metamask 钱包登录**。然后点击你的个人资料图片，你应该在那里能够看到你新铸造的 NFT。如果图片还不可见，点击 “Refresh metadata” 按钮：

![2610](https://files.readme.io/3256828-testnet.png "testnet.png" width=auto height=auto)

有时 OpenSea 很难识别测试网的元数据 — 可能需要 6 个小时才能看到。经过一些时间之后，你的 NFT 应该是可见的，如下所示：

![2840](https://files.readme.io/b36cf81-erc_testnet.png "erc_testnet.png" width=auto height=auto)

**恭喜你，你已经成功创建、修改并部署了你的第一个智能合约。铸造了你的第一个 NFT，并在 IPFS 上发布了你的图像！** 🔥

## 挑战部分

**下一步？**  尝试修改智能合约，允许用户只能最多铸造一定数量的NFT。就让我们限制每个用户（钱包地址）只能铸造 5 个 NFT，否则有人可能会开始铸造出成千上万个 NFT！

要做到这一点，请研究一下 Solidity 的 `mapping` 类型，[这里有一份指南指导你](https://docs.soliditylang.org/en/v0.4.21/types.html)。

想要这个教程的视频版本？订阅[Alchemy 的 YouTube 频道](https://www.youtube.com/channel/UCtvTdPZWUwW4whk9CLlCBug) 并加入我们的[Discord社区](https://discord.gg/3AyCvMJrAr)，成千上万的开发者会帮助你！

我们一直在寻求意见改进这个学习之旅，请与我们分享您的意见 [https://alchemyapi.typeform.com/roadtofeedback](https://alchemyapi.typeform.com/roadtofeedback)