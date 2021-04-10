## Chapter 8 記憶器元件與介面設計
### 8.1 CPU 與記憶器的界接
1. ROM : 主要用以儲存永久性的資料，RAM : 用來儲存暫時性的資料
2. 記憶器容量與語句寬度的擴充
> 問題:
> 使用單一個記憶器元件的容量往往不敷實際上的使用
> 使用單一個記憶器元件的**語句寬度小於CPU的資料匯流排寬度**
> 解決方法:容量擴展(capacity expansion)、語句寬度擴展(word width expansion)
(1)容量擴展:
* 目的:串接多個小容量的記憶器模組，成為一個具有相同語句寬度的容量記憶器模組
> ![](https://i.imgur.com/zPcBD0V.png)
> 成為一個256* 4 的ROM模組

(2)語句擴展:
* 目的:並接多個窄語句寬度的記憶器模組，成為一個具有相同容量的寬語句寬度的記憶器模組
> ![](https://i.imgur.com/A38TObB.png)
>成為一個 64 * 8的ROM模組

3. 記憶器位址解碼原理
> address decoder(位址解碼器):由CPU的記憶器空間產生記憶器元件的晶片致能信號(~CS)以控制該記憶器元件動作與否。

(1)部分位址解碼(partial decoding):只有部分位址信號參與位址解碼的動作。缺點為無法完全使用cpu的位址空間，造成擴充困難
![](https://i.imgur.com/3Hi5hsr.png)

(2)完全位址解碼(total decoding):**每一個元件或裝置接擁有唯一的位址區**，因此需要較多的解碼電路。
3 input，8 output
![](https://i.imgur.com/x8zgMuL.png)
![](https://i.imgur.com/jZNOIXa.png)

(3)區段位址解碼(block address decoding):CPU記憶器位址空間劃分成許多個以區段位址為單位的區段位址區，然後使用完全位址解碼方式，解出區段位址(用於大型系統中)
![](https://i.imgur.com/DC4yjUN.png)

3. 位址解碼電路設計
(1)固定位址解碼:直接使用解碼電路解出選定的位址，缺點為解碼電路設計完成後其位址即告確定若希望更改解碼位址時，必須重新設計位址解碼電路
* 使用NAND閘設計解碼電路
A19~A14均為0，A13~A1直接接於SRAM中
![](https://i.imgur.com/kqrgiLu.png)
* 使用解碼器元件
![](https://i.imgur.com/kauSsNS.png)
![](https://i.imgur.com/7BG5Tls.png)
* 開關選擇位址解碼電路 : 以一組DIP開關設定玉解碼的位址區，通常包刮一個比較器，比較位址輸入值與開關設定值。若相等，則比較器產生致能訊號，否則不產生知能的信號輸出。
![](https://i.imgur.com/junS4LZ.png)
>優點 : 具有彈性，其解碼位址可以由開關的設定更改
>
>缺點 : 需要較多的硬體電路，成本也較高
* PROM位址解碼
![](https://i.imgur.com/iTdzSp3.png)
![](https://i.imgur.com/3kcJBFZ.png)
* PAL位址解碼 : 兩層的AND-OR結構，因此可以執行任何交換表示式
![](https://i.imgur.com/48Kt4uM.png)

### 8.2 SRAM
> SRAM內部使用正反器(Latch)電路儲存資料，由於正反器電路為一個雙穩態電路只要嘉佃路上的電源不中斷，即能持續地保持其資料的完整。所以又稱靜態RAM(static RAM)。
1. 商用SRAM: **需考慮時序的正確性**
* 控制信號:
> 晶片致能或選取(chip enable or chip select):致能整個元件的動作，低位啟動時，資料輸出端為高阻抗。
> 讀取與寫入控制(read and write control):控制資料流的方向
* 一般資料輸入端與資料輸出端共用相同接腳，因此其資料輸出一般都先經過一個三態緩衝器再接往資料輸出腳。三態緩衝器則由外加輸入端~OE(output enable)信號控制。只有在讀取資料時才打開緩衝器。~OE可避免同時多個DATA進到Data bus 產生干擾。
![](https://i.imgur.com/gM0mq7k.png)

* 在SRAM中只有一條讀取與寫入控制輸入線，則表示為R/~W (read/write)，若一個sram有兩條讀取與寫入控制線，則標示為~WE與 ~OE (~WE =0為寫入動作，~OE =0為讀取動作)
![](https://i.imgur.com/svFBbRe.png)
* 讀取時序
![](https://i.imgur.com/LLytw15.png)
> 資料輸出端在~CE 控制信號器動之後的Tclz及 ~OE控制信號啟動後的Tolz即開始啟動。
> 在連續的讀取動作中，~CE與~OE 控制信號可以持續維持在啟動狀態，而只更動位址信號，資料輸出端的資料在位址改變時，**依然會維持一段穩定的時間**，稱為**輸出資料持住時間**(tOH)。
> 位址存取時間(address access time,tAA):由加入位址信號到SRAM元件的位址輸入端起算到成立的資料出現在資料輸出端的時間

> ~CE存取時間(~CE access time,tAC):~CE的負緣到資料出現在資料輸出端的時間
> ~OE存取時間(~OE access time,tOE):~OE的負緣到資料出現在資料輸出端的時間
* 寫入時序
> 資料設定時間(data setup time,tDS):資料在被鎖進記憶器之前，必須保持在穩定質狀態的最小時間
> 資料持住時間(data hold time,tDH):資料再被鎖入記憶器之後，必須持續保持在其穩定值狀態的的最小時間
> ![](https://i.imgur.com/7dAhGRz.png)
2. SRAM 與 CPU界接

(1) SRAM 記憶器模組設計:
> A19~A16及M/~IO等信號產生62256需要的~CE信號。
> ![](https://i.imgur.com/nUoM2Kr.png)
(2)8086 CPU 讀取時序圖 :
![](https://i.imgur.com/ELVNJro.png)
![](https://i.imgur.com/1VigW7S.png)

### 8.3 快閃記憶器
> 唯讀記憶器(read-only memory,ROM)的內容一經決定後，就永久固定，不能再以任何方式加以改變。有隨意讀取的特性。EX:儲存開機程式
> ROM 具有低成本、高速度、系統設計彈性的特性。
> ROM三大類型:
> 1. 罩網ROM(mask ROM):包裝後就不能改變，用於大量生產的產品中以降低成本。
> 2. 可規劃ROM(programmable ROM,PROM)
> 3. 可清除可規劃ROM(erasable PROM,EPROM):可以任意地規劃或清除其內容。
1. 商用快閃記憶器元件

> (1) 接腳功能:解腳分布與SRAM元件相同
> A18~A00(位址信號，輸入):指定欲存取的位元組位置
> 
> D7~D0:記憶器的資料輸入與輸出信號端
> 
> ~CE(chip enable):當~CE接於**低電位**時，可以讀取或是寫入資料於記憶器中
> 
> ~OE(output enable):~OE控制資料輸出緩衝器，當被啟動時，允許自記憶器中讀取資料
> 
> ~WE(write enable):~WE控制對記憶器的資料寫入動作
> 
>(2) 資料讀取:與SRAM元件相同。在讀取資料時，晶片選擇輸入信號(~CE) 與輸出智能(~OE) 控制信號必須都啟動(即為低電位)。
> 在連續的讀取動作中，~CE 與 ~OE 控制信號可以持續維持在啟動狀態，而只更動位址信號，資料輸出端的資料在位址改變時，**依然會維持一段穩定的時間**，稱為**輸出資料持住時間**(tOH)。

>(3) 資料寫入: **~CE &~WE 都必須啟動(接於低電位)，而~OE 接於高電位。**
> 在 ~WE (~CE) 的寫入動作中，位址信號在~WE (~CE)啟動之前必須已經穩定一段時間，稱為位址設定時間(tAS)
> 
> 位址信號在~WE (~CE) 啟動之後，必須已經穩定一段時間，稱為位址持住時間(tAH)
> 
> 在~WE (~CE) 控制信號啟動之前，~CE (~WE) 控制信號必須已經啟動一段時間，稱為寫入設定時間(tCS)
> 
> 在~WE (~CE) 控制信號恢復為不啟動之後，~CE (~WE) 控制信號必須持續在啟動狀態，稱為寫入持住時間(tCH)
> ![](https://i.imgur.com/IXs4SX8.png)

2. 快閃記憶器元件與CPU界接
![](https://i.imgur.com/j9AaVje.png)

3. Example 
![](https://i.imgur.com/tstFEFT.png)
![](https://i.imgur.com/hxDVulA.png)
![](https://i.imgur.com/V93c2d7.png)


### 8.4 DRAM
> embedded main mem : SRAM
> PC main mem : DRAM

> DRAM內部使用電容器的電荷儲存特性保存資料，但由於儲存的電荷會隨著時間慢慢地消失，因而又稱為動態RAM(Dynamic RAM)，因此需部段的補充電荷稱為更新(refresh)

> 優點:密度、位緣成本、消耗功率均較SRAM低

> 缺點:位址使用多工輸入的方式，因此需要較複雜的界面電路(需做到分時多工)

1. 接腳分布圖 : 有較特別的兩條控制線~CAS & ~RAS ，因為DRAM原件為一個高容量的記憶器，若如同SRAM依樣，直接輸入所有位址線於DRAM元件中，其包裝的接腳數目必然大大的增加，因此DRAM採用分時多工的方式輸入
![](https://i.imgur.com/Y4x87AI.png)
2. 位址路徑與時序
![](https://i.imgur.com/N4tVJ2T.png)
![](https://i.imgur.com/0d61xY5.png)
![](https://i.imgur.com/I7sSiAV.png)







