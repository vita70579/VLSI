TCL & Design compiler
=====================
# synthesis
  >synthesis=translation+logic optimization+gate mapping<br>
  >translation: HDL→GTECH (default library)<br>
  >gate mapping: GTECH→ddc (netlist(電路), .v(用於post-sim), .sdf(延遲訊息), .sdc(約束訊息))<br>

# library
Technology library:<br>
  >1. Target library: 由半導體vendor提供的器件資源庫。<br>
  >2. Link library: 第三方IP庫,在合成時不轉換為標準單元,而是由vendor提供的模型來模擬真實行為。<br>
Symbol library: 圖形庫<br>
Synthetic library: Synopsys Designware的IP庫<br>
# timing path
  >起點: 輸入端口, 時鐘引腳<br>
  >終點: 輸出端口, 除了時鐘引腳外的其他輸入<br>
  >>- 因此，timing path可以是輸入到暫存器/暫存器到暫存器/暫存器到輸出/輸入到輸出<br>
  >>- critical path: 最長的一條timing path。
# timing violation
## 定義:
  - setup time: edge到來前資料須穩定一段時間
  - hold time: edge觸發後資料須保持一段時間
  - metastable: 在不滿足setup time與hold time的時間內對資料進行採樣而得到不可預期的結果
## 約束: 暫存器到暫存器之間路徑的約束
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im.png)
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im1.png)
>Tcq: clock觸發至Q成功將D鎖出的時間 (暫存器延遲)<br>
>Tskew: clock skew<br>
>Tcomb: 組合邏輯運算時間<br>
  >>- setup time: (Tclk+Tskew)-(Tcq+Tcomb)>Tsetup:<br>
  以DFF1的p1時刻為基準，數據需要再DFF2的p3時刻滿足setup要求，那麼從p1時刻DFF1的值傳到DFF2的D端需要的時間為:(Tcq+Tcomb)，那麼對於DFF2而言下一個正緣來臨時刻相對於p1為(Tclk+Tcomb)。
  >>- hold time: (Tcq+Tcomb)>(Thold+Tskew:)<br>
  對於hold time，即是相對於DFF2的D端而言，p2時鐘沿來了之後，不要立刻影響到p3採到的數據，使得p3能正常的採得數據，而p2來臨之後，傳遞到D端時間還是(Tcq+Tcomb)。而這時要求至少在p3+hold以後才運算 完成，則需要D端至少保持(Thold + Tskew)這麼長的時間。<br>
## 約束: 輸入端口到暫存器之間路徑的約束
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im2.png)<br>
>由於我們要合成的對象是灰色方塊中的組合邏輯N，但要分析其時序約束條件必須將外部的時序電路同時考慮進去:
>- 由方塊圖可以看出約束式: (Tclk+Tskew)-(Tcq+Tcomb_M+Tcomb_N) > Tsetup
>- 因此我們可以推導出合成組合邏輯N的約束條件: (Tclk+Tskew)-(Tcq+Tcomb_M) > Tsetup+Tcomb_N，故DC會依照組合邏輯N的延遲條件優化。
>- 我們可以合理設置不確定時間(δ)以及指定Tskew與外部延遲(Tcq, Tcomb_M)，可以得到: Tcomb_N_max=Tclk+Tskew-(Tcq+Tcomb_M)-δ-Tsetup
## 約束: 暫存器到輸出端口之間路徑的約束
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im3.png)<br>
>由於我們要合成的對象是灰色方塊中的組合邏輯S，但要分析其時序約束條件必須將外部的時序電路同時考慮進去:
>- 由方塊圖可以看出約束式: (Tclk+Tskew)-(Tcq+Tcomb_S+Tcomb_T) > Tsetup
>- 因此我們可以推導出合成組合邏輯N的約束條件: (Tclk+Tskew)-(Tcq+Tcomb_T) > Tsetup+Tcomb_S，故DC會依照組合邏輯N的延遲條件優化。
>- 我們可以合理設置不確定時間(δ)以及指定Tskew與外部延遲(Tcq, Tcomb_T)，可以得到: Tcomb_S_max=Tclk+Tskew-(Tcq+Tcomb_T)-δ-Tsetup
# time bugeting (時間預算)
>![Image](https://github.com/vita70579/VLSI/raw/main/Image/im4.png)<br>
>由於Soc電路較大，需要對設計進行劃分，但是各區塊的設計者往往無法得知其他區塊的輸入輸出delay，因此我們採用time bugeting的設計方法:
>- time bugeting: 假設輸入和輸出內部電路各占用工作週期的40%
>- 即:Tclk = Tcq+Tcomb_X+Tcomb_Y+Tsetup (此處不考慮Tskew)，其中Tcomb_X,Y分別佔用工作週期的40%，因此工作週期剩下的20%包含了Tcq,Tsetup
>- 則margin定義為20%Tclk-Tcq-Tsetup
