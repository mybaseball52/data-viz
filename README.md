# Aquarium Project

> 
> #### 以下提供PlayerBook.sol作為範例，主要列出：
> * 重點資料結構
> * 重點函式（先列出函式介面即可）
> 
## Ethereum相關開發（智能合約）
---
合約內容部份僅列出核心函式（以PlayerBook為範例）

### 合約主從架構
![123](https://i.imgur.com/wkdC3MF.png)

### 合約內容（四個合約）

- ### 共同部分(lib)

    - ==IERC20== ==暫時無用到==
        
    - Ownable
        - onlyOwner: 只有特定的地址才可以執行 
        ``` javascript=1
        modifier onlyOwner() {
            require(msg.sender == owner, "Only Owner Can Do This");
            _;
        }
        ```
    - Pausable
        - whenNotPause: 當合約沒有暫停時執行
        ``` javascript=1
        modifier whenNotPaused() {
            require(!paused);
            _;
        }
        ```
        - whenPaused: 當合約已經暫停時執行
        ``` javascript=1
        modifier whenPaused() {
            require(paused);
            _;
        }
        ```
        - pause, unpause: 主動調整合約狀態的函式，必要時owner得以控制其開關
        ``` javascript=1
        function pause() public onlyOwner whenNotPaused {}
        ```
        ``` javascript=1
        function unpause() public onlyOwner whenPaused {}
        ```
- ### PlayerBook.sol

    _重點結構_
    ``` javascript=1
    /** @dev Player model */
    struct Player {
        /* pricing data */
        uint joinPrice;
        uint accumulatedSellPrice;
        uint maximumSellPrice;
        uint recomanndBonus;
        uint onlySellPrice;
        /* fish data of the player */
        uint fishbowlLevel;
        uint fishbowlSize;
        uint fishAmount;
        /* About generation */
        address[] addrOfFirstGenetations;
        address[] kins;
        /* status of player */
        PlayerStatus status;
    }

    /** @dev mark the current status of a player */
    enum PlayerStatus { NOT_JOINED, NORMAL, EXCEEDED }
    ```
    -  joinGame: 玩家初入遊戲遊戲

        -  initPriceData: 初入遊戲時的價錢初始設定，包含 ***基礎入場價格***、***累積銷售金額***、***可銷售總額***
        ``` javascript=1
        function initFishData(uint _initFishAmount) internal {
            address _addr = msg.sender;
            playerBook[_addr].fishbowlLevel = Fishbowl.getFishbowlLevel(_initFishAmount);
            playerBook[_addr].fishbowlSize = Fishbowl.getFishbowlSize(_initFishAmount);
            playerBook[_addr].fishAmount = _initFishAmount;
        }
        ```
        -  initFishData: 初入遊戲時的魚隻資訊初始設定，包含 ***魚缸等級***、***魚缸大小***、***初始魚數***
            > 魚缸等級與魚缸大小會由Fishbowl呼叫
        ``` javascript=1
        function initPriceData(uint _initFishAmount) internal {
            address _addr = msg.sender;
            playerBook[_addr].joinPrice = (_initFishAmount * getCurrentPrice() * 2));
            playerBook[_addr].accumulatedSellPrice = 0;
            playerBook[_addr].maximumSellPrice = playerBook[_addr].joinPrice * playerBook[_addr].fishbowlSize;
        }
        ```
    - addFishAmount: 增加玩家魚數
       
        *玩家增加魚數之條件*
        1. 玩家於時價購買魚
            > TransactionSystem.sol在觸發買魚時需要呼叫
        2. 玩家取消掛單時
            > TransactionSystem.sol 在觸發取消掛單時需呼叫
        3. 系統繁殖
            > TransactionSystem.sol 在觸發繁殖時需呼叫
        ``` javascript=1
        function addFishAmount (uint _fishAmount) private PlayerIsLegal () {}
        ```
    - minusFishAmount: 減少玩家魚數
        
        *玩家減少魚數之條件*
        1. 掛單
            > TransactionSystem 在觸發玩家掛單時需呼叫
        ```javascript=1
        function minusFishAmount (uint _fishAmount) private PlayerIsLegal () {}
        ```
        
    - updateFishbowlSize: 升級魚缸
        ==升級魚缸條件==
        1. 買滿自身等級以上的魚隻
            > Fishbowl.sol 在觸發升級成功時需呼叫
        2. 推薦直一
            > Commission.sol 在呼叫友人推薦時需呼叫
        ```javascript=1
        function updateFishbowlSize (uint _bowlsize) private PlayerIsLegal () {}
        ```
- ### TransactionSystem.sol
        此部分主要由峻淋實作、Kobe協作
    - 
- ### Fishbowl.sol
        此部分主要由冠毅實作
    -
    
- ### Commission.sol
        此部分主要由冠毅實作
    :::
    - 