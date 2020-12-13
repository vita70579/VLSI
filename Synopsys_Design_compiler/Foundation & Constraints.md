Foundation
=========
># Synthesis
  >synthesis=translation+logic optimization+gate mapping<br>
  >translation: HDL→GTECH (default library)<br>
  >gate mapping: GTECH→ddc (netlist(電路), .v(用於post-sim), .sdf(延遲訊息), .sdc(約束訊息))<br>

># Library
Technology library:<br>
  >1. Target library: 由半導體vendor提供的器件資源庫。<br>
  >2. Link library: 第三方IP庫,在合成時不轉換為標準單元,而是由vendor提供的模型來模擬真實行為。<br>
Symbol library: 圖形庫<br>
Synthetic library: Synopsys Designware的IP庫<br>

Time Constraint
===============
># Timing path
  >起點: 輸入端口, 時鐘引腳<br>
  >終點: 輸出端口, 除了時鐘引腳外的其他輸入<br>
  >>- 因此，timing path可以是輸入到暫存器/暫存器到暫存器/暫存器到輸出/輸入到輸出<br>
  >>- critical path: 最長的一條timing path。
  
># Time Constraint
>## 定義:
  >>- setup time: edge到來前資料須穩定一段時間
  >>- hold time: edge觸發後資料須保持一段時間
  >>- metastable: 在不滿足setup time與hold time的時間內對資料進行採樣而得到不可預期的結果
>## 定義:
  >>- latency: clock到FF的CLK pin的延遲
  >>>- clock source latency: 實際clock原點到設計對象(block)的延遲
  >>>- network latency: block中clock起點到DFF的延遲
  >>- transition: 位準變化的延遲 (電位20%~80%的時間)
  >>- skew: clock到不同CLK pin之間的時間差
  
>## 約束: 暫存器到暫存器之間路徑的約束
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im.png)
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im1.png)
>Tcq: clock觸發至Q成功將D鎖出的時間 (暫存器延遲)<br>
>Tskew: clock skew<br>
>Tcomb: 組合邏輯運算時間<br>
  >>- setup time: (Tclk+Tskew)-(Tcq+Tcomb)>Tsetup:<br>
  以DFF1的p1時刻為基準，數據需要再DFF2的p3時刻滿足setup要求，那麼從p1時刻DFF1的值傳到DFF2的D端需要的時間為:(Tcq+Tcomb)，那麼對於DFF2而言下一個正緣來臨時刻相對於p1為(Tclk+Tcomb)。
  >>- hold time: (Tcq+Tcomb)>(Thold+Tskew:)<br>
  對於hold time，即是相對於DFF2的D端而言，p2時鐘沿來了之後，不要立刻影響到p3採到的數據，使得p3能正常的採得數據，而p2來臨之後，傳遞到D端時間還是(Tcq+Tcomb)。而這時要求至少在p3+hold以後才運算 完成，則需要D端至少保持(Thold + Tskew)這麼長的時間。<br>
  
>## 約束: 輸入端口到暫存器之間路徑的約束
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im2.png)<br>
>由於我們要合成的對象是灰色方塊中的組合邏輯N，但要分析其時序約束條件必須將外部的時序電路同時考慮進去:
  >>- 由方塊圖可以看出約束式: (Tclk+Tskew)-(Tcq+Tcomb_M+Tcomb_N) > Tsetup
  >>- 因此我們可以推導出合成組合邏輯N的約束條件: (Tclk+Tskew)-(Tcq+Tcomb_M) > Tsetup+Tcomb_N，故DC會依照組合邏輯N的延遲條件優化。
  >>- 我們可以合理設置不確定時間(δ)以及指定Tskew與外部延遲(Tcq, Tcomb_M)，可以得到: Tcomb_N_max=Tclk+Tskew-(Tcq+Tcomb_M)-δ-Tsetup
  
>## 約束: 暫存器到輸出端口之間路徑的約束
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im3.png)<br>
>由於我們要合成的對象是灰色方塊中的組合邏輯S，但要分析其時序約束條件必須將外部的時序電路同時考慮進去:
  >>- 由方塊圖可以看出約束式: (Tclk+Tskew)-(Tcq+Tcomb_S+Tcomb_T) > Tsetup
  >>- 因此我們可以推導出合成組合邏輯N的約束條件: (Tclk+Tskew)-(Tcq+Tcomb_T) > Tsetup+Tcomb_S，故DC會依照組合邏輯N的延遲條件優化。
  >>- 我們可以合理設置不確定時間(δ)以及指定Tskew與外部延遲(Tcq, Tcomb_T)，可以得到: Tcomb_S_max=Tclk+Tskew-(Tcq+Tcomb_T)-δ-Tsetup
  
>## 約束: 模組中組合邏輯的路徑約束 (模組中包含clock)
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im6.png)<br>
>Tcomb_F=Tclk-Tinput_delay-Toutput_delay<br>
  >>**set_input_delay 0.4 -clock CLK -add_delay \[get_ports B]<br>
  >>set_output_delay 0.2 -clock CLK -add_delay \[get_ports D]<br>**

>## time bugeting (時間預算)
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im4.png)<br>
>由於Soc電路較大，需要對設計進行劃分，但是各區塊的設計者往往無法得知其他區塊的輸入輸出delay，因此我們採用time bugeting的設計方法:
  >>- time bugeting: 假設輸入和輸出「內部電路」各占用工作週期的40%
  >>- 即:Tclk = Tcq+Tcomb_?+Tcomb_N+Tsetup (此處不考慮Tskew)
  >>- 若Tcomb_?,N分別佔用工作週期的40%，則工作週期剩下的20%包含了Tcq,Tsetup，可以定義margin(餘量)為:20%Tclk-Tcq-Tsetup
  
>>### 例子:
>>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im5.png)<br>
因假設輸入/輸出內部電路佔40%Tclk (Tclk=10ns)，因此可以設定外部延遲為10-40%Tclk=6ns<br>
  >>>**create_clock -period 10 \[get-ports CLK]<br>
  >>>set_input_delay -max 6 -clock CLK \[all_inputs]<br>
  >>>remove_input_delay \[get ports CLK]<br>
  >>>set_output_delay -max 6 -clock CLK \[all-putput]<br>**

Environmental constraint
========================
># 設置環境約束
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im7.png)<br>
>>- 設置驅動
>>- 設置驅動最大電容
>>- 設置連線延遲模型
>>- 設置負載電容

># Load Buget (負載預算):
>>- 假設輸入由驅動能力弱的負載單元驅動
>>- 限制輸入的輸入電容
>>- 估計輸出推動的模組數量

># 設定工作條件 (set operating conditions)
> 預設工作條件(nominal) 在.lib檔中有描述，若要在不同於nominal的電壓/溫度下工作則要另外設定。<br>
>- 溫度/電壓/製程對延遲的影響:<br>
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im8.png)<br>

># 連線延遲模型 (一般使用Wire Load Model):
>> 1. 在相同工藝下計算出邏輯閘個數範圍內，負載fanout=1,2,3,...的連線平均長度，並且透過單位長度的電阻/電容/面積計算出延遲。<br>
>> 2. 連接兩個不同模組的連線模型:
>>>- enclpdes: 兩模組之間的連線延遲估計以「包含著兩模組的上一層模組」的連線負載模型估計。為較不悲觀的延遲估計。
>>>- top: 即兩模組之間的連線延遲估計以「頂層模組」的連線負載模型估計。較悲觀的延遲估計。

複雜的時序約束
=============
有些特別的路徑並不在一個cycle內完成。<br>
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im9.png)<br>
>># setup check的設定
>>因為單元要執行n個cycle，所以要在DFF觸發後的n個週期做setup check<br>
>>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im10.png)<br>
>>**加法器的最大允許延遲: Tadder_max = 6\*Tclk - Tcq - Tsetup - Tuncertainty**
>># hold check的設定
>>正常情況而言，正緣觸發時C讀取的應該是6 cycle前的運算結果，而不是1cycle前的運算結果(加法器算太快所致)。<br>
>>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im11.png)<br>
>>- **在5 cycle後做hold check (電路複雜)**
>>- **在第一個cycle就坐hold check (電路簡單)**
>>**加法器的最小允許延遲: Tadder_min = Thold + Tuncertainty**
># 特例: 兩個DFF之間有單週期電路也有多週期電路
>>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im12.png)<br>
>> create_clock -period 10 [get_ports clk] <br>
>> set_multicycle_path -setup 2 **-from** FFA/CP **-through** Multiply/Out **-to** FFB/D <br>
>> set_multicycle_path -hold 1 **-from** FFA/CP **-through** Multiply/Out **-to** FFB/D <br>

Area Constraint
===============
># 面積單位
>>- 2-input-NAND-gete
>>- Transistors
>>- (um)^2
># 設定
>>- 不設定: DC做最小限度優化
>>- 設定面積約數: 達標後結束
>>- 0: 持續優化到收斂