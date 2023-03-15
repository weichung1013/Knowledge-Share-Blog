---
title: Monrepo 學習筆記 01
date: 2023-03-15 13:39:21
tags: monorepo,vue,note
---
# Monorepo 簡介

## Monolith vs Polyrepo vs Monorepo
![](https://i.imgur.com/RXbWoHU.png)

### Monolith
在專案較小、或是開發初期、協作人員不多的情況下，大多數的團隊都會採用Single-Repo Monolith的架構來進行開發。
例如下圖所示，WebGether網站的功能有新聞推送、商城以及聊天等三大功能。

![](https://i.imgur.com/DDuG0Mv.png)

檔案如下：
```
├── assets
├── components
│   ├── Button.js
│   ├── Modal.js
│   └── ...
├── node_modules
├── pages
│   ├── Chat
│   │   ├── Contact.js
│   │   ├── Message.js
│   │   └── ...
│   ├── Mall
│   │   ├── Cart.js
│   │   ├── Product.js
│   │   └── ...
│   ├── News
│   │   ├── Article.js
│   │   ├── Topic.js
│   │   └── ...
├── utils
├── package.json
├── jest.config.js
├── webpack.config.js
├── yarn.lock
└── README.md
```

這樣的好處是一庫到底、簡單易懂
但是當產品越來越複雜、團隊越來越大的時候就會開始出現問題了：

1. 打包和部署時間拉長
2. 不同功能相依性管理困難，難以擴充、缺乏彈性
3. 無團隊自主性
4. 不同團隊發布時間必須統一

假設WebGether三大功能原本都是用React開發的，隨著時間推進，React改版了。
開發新聞功能的團隊想改用新版React、商場團隊想要改用Vue作為框架、聊天功能的團隊發現慣用的某些package在新版React不受支援了，想維持使用舊版。
這時候唯一的解發就是將三大功能分家，切分為不同的專案。
   
### Polyrepo
Polyrepo(又稱 many-repo or multi-repo)最大的好處就是提供了團隊自主性(team autonomy)，
不同的Microservice直接拆分到不同repo完全獨立開來。
例如上面的例子，新聞團隊可以放心升級到新版、商場團隊可以打掉用Vue改寫、聊天團隊則可以繼續沿用化石級的package。

其檔案配置如下：

News:
```
├── assets
├── components
│   ├── Button.js
│   ├── Modal.js
│   └── ...
├── node_modules
├── pages
│   ├── Cart.js
│   ├── Product.js
│   └── ...
├── utils
├── package.json
├── jest.config.js
├── webpack.config.js
├── yarn.lock
└── README.md
```
Mall:
```
├── assets
├── components
│   ├── Button.js
│   ├── Modal.js
│   └── ...
├── node_modules
├── pages
│   ├── Article.js
│   ├── Topic.js
│   └── ...
├── utils
├── package.json
├── jest.config.js
├── webpack.config.js
├── yarn.lock
└── README.md
```
Chat:
```
├── assets
├── components
│   ├── Button.js
│   ├── Modal.js
│   └── ...
├── node_modules
├── pages
│   ├── Contact.js
│   ├── Message.js
│   └── ...
├── utils
├── package.json
├── jest.config.js
├── webpack.config.js
├── yarn.lock
└── README.md
```

![](https://i.imgur.com/b2ooAt3.png)

這樣就天下太平了嗎? 倒也沒有...
Polyrepo最大的好處就是團隊的獨立性，但是獨立性往往卻傷害了團隊協作...

1. 元件不易共享，例如某個團隊開發了很好用的新功能，其他團隊也想使用，以前Monolith的時候可以直接套用，但現在只能以npm package或git submodule的方式才能共用，增加了共用的困難度。
2. 資源更新時，個別repo難以被通知而及時更新。
3. 當共用資源有問題時，必須要每個repo個別去修bug、測試、發布，大幅增加了出錯的機率和複雜度。
4. 建構、測試、發布或rollback的複雜度都大增，可能只能用外部文件來管理，同樣增加了出錯的機率。

於是，Monorepo的概念就誕生了，希望可以同時兼顧團隊的獨立性和協作的能力。

### Monorepo
![](https://i.imgur.com/4teD3ca.png)

根據[Monorepo Tools](https://monorepo.tools/#local-computation-caching)，其定義為：
> A monorepo is a single repository containing multiple distinct projects, with well-defined relationships.<br/>

和Monolith相同的，Monorepo同樣將所有專案放在同一個repo內。
但是和Monolith不同的，其專案的關係是有清楚區分定義的。

其檔案配置大概如下：
```
├── shared
│   ├── assets
│   ├── components
│   │   ├── Button.js
│   │   ├── Modal.js
│   │   └── ...
│   ├── utils
│   └── ...
├── apps
│   ├── chat
│   │   ├── components
│   │   ├── pages
│   │   │   ├── Contact.js
│   │   │   ├── Message.js
│   │   ├── utils
│   │   └── ...
│   ├── chat-e2e
│   ├── mall
│   │   ├── components
│   │   ├── pages
│   │   │   ├── Cart.js
│   │   │   ├── Product.js
│   │   ├── utils
│   │   └── ...
│   ├── mall-e2e
│   ├── news
│   │   ├── components
│   │   ├── pages
│   │   │  ├── Article.js
│   │   │  ├── Topic.js
│   │   ├── utils
│   │   └── ...
│   └── news-e2e
├── node_modules
├── package.json
├── jest.config.js
├── webpack.config.js
├── yarn.lock
└── README.md
```

Monorepo 將個別功能放在同一 repo 的不同 package 底下，如前面的例子，在拆分 News、Mall 和 Chat 後，這三個功能雖然仍在同一個 repo 底下，但放置於不同的 pacakge 資料夾。
除了拆分功能外，Monorepo也將可共用的元件放在shared資料夾下，並共用相同的config檔案，這樣在建構、測試、發布以及rollback就不會有不相容的情況發生。

和Monolith不同的地方可用下圖表示：
![](https://i.imgur.com/nw4ft6P.png)

簡單來說，Monorepo就是模組化(modular)之後的Monolith。

這樣Monorepo的好處有：
1. 能共享資源，在同框架時更顯優勢。
2. 當資源更新時，個別 app 能夠及時被通知而更新。
3. 能共享配置，像是共享環境設定和 config 檔，不需每個 app 再自建一套。這在建構、測試與發佈流程上甚至是 rollback 是很方便的。

但是Monorepo仍然會遇到許多技術難題：
1. 和Monolith一樣，Monorepo一樣會有repo過於龐大的問題。
解法：[Mitigation strategies on Git](https://www.atlassian.com/git/tutorials/monorepos);
> Microsoft released GVFS, a scalable version of Git that works with enormous repos. Azure Pipelines, BitBucket support it already. GitHub support is coming soon.<br/>

2. 公用和非共用要切分清楚，共用的package和元件要放在共用資料夾內，不可隨意引用其他app的原件以免相依性亂掉。
3. 要注意package version hoist的狀況，以防node module的size爆掉...

![](https://i.imgur.com/O2e9sgR.png)

解法：許多Monorepo的工具都可以針對檔案的visibility做出限制，以免不同團隊將不屬於自己App的檔案設到package.json中。
4. 開發人員眾多時難以控管檔案權限，無法針對 package 來限制誰能瀏覽或編輯，同時也會反應到開 issue、回覆 PR 和通知過於紊亂的問題上。
解法：[Github Code Owners](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)，可以針對不同檔案給予不同人權限。例如：
```
apps/app-a/* @susan
apps/app-b/* @bob
```
這時候當 pull request 發給 App A時，只有Susan會收到通知，而當PR發給App B時，則只有Bob需要回覆。

## Monorepo的挑戰
Monorepo工具Nx的作者，Victor Savkin有整理一些採用Monorepo團隊會遇到的挑戰：
1. Trunk-based development
Monorepo和許多團隊慣用的branch分支開發並不是很合，所以團隊必須考慮切換為trunk-based的開發方式。
2. Not all services work with it
由於Monorepo的概念仍新，所以某些服務可能還沒有完全支援它。但是應該都有方法可以解決不相容的問題。
3. CI
Continuous integreation的流程可能要重新思考，因為你不再是建構整個單一app，而是必須只建構有改變的部分而已。
許多CI soluions都夠靈活可和Monorepo的工具整合，例如Azure、Circle和Jenkins(Github Actions應該也可以?)，但是你仍需花時間來研究。
4. Large-scale changes
但改動一個共用元件時，可能會影響到所有的apps，所以當進行這種大幅度改動時，必須考慮到backward-compatibility，例如可能必須準備兩個不同版本的parameter/methods/class/package，讓所有開發人員都有時間能從舊版移動到新版。

## 何時該使用Monorepo?
引用Sunner。桑莫整理的[表格](https://www.cythilya.tw/2023/01/28/monolith-vs-multi-repo-vs-mono-repo/)


| #        | Monolith                  | Polyrepo                  | Monorepo                               |
| -------- | --------                  | --------                  | --------                               |
| 特色     | 將所有的功能放<br/>在單一repo | 將個別功能放在<br/>不同 repo | 將個別功能放在<br/>同一 repo 的不同 package |
| 優點     | 簡單方便 | 1. 專案體積小<br/>、高效開發；<br/>2. 技術線獨立、<br/>相依管理簡單、<br/>高彈性 | 共享與獨立兼顧 |
| 缺點     | 相依管理困難、<br/>難以擴充、缺乏彈性 | 共享不易 | repo 龐大、<br/>開發需明確規範、<br/>~~檔案權限難以控管~~<br/>、Git 效能差 |
| 工具     | -                         | -                         | Nx, Lerna, Bazel等等 |
| 適用情境  | 產品開發初期或<br/>非大型規模專案 | 切分大型專案、<br/>相依和共享狀況<br/>少 | 切分大型專案、<br/>相依和共享狀況<br/>多 |

## 下期預告
1. 建立Vue3+Typescript的Monorepo專案
2. 可能會挑一個Monorepo工具來玩玩看(NX? Lerna? Bazel?)
## Reference
[該用 Monorepo 嗎？比較 Monolith vs Multi-Repo vs Monorepo](https://www.cythilya.tw/2023/01/28/monolith-vs-multi-repo-vs-mono-repo/);<br/>
[Monorepo Tools](https://monorepo.tools/#local-computation-caching);<br/>
[Misconceptions about Monorepos: Monorepo != Monolith](https://blog.nrwl.io/misconceptions-about-monorepos-monorepo-monolith-df1250d4b03c);<br/>
[Sharing code between web and native projects of React in a Monorepo](https://medium.com/@ksholla20/sharing-code-between-web-and-native-projects-of-react-in-a-monorepo-5b6e3b09d315);<br/>
###### tags: `monorepo`,`vue`,`note`