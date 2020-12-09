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
定義:<br>
  - setup time: edge到來前資料須穩定一段時間
  - hold time: edge觸發後資料須保持一段時間
  - metastable: 在不滿足setup time與hold time的時間內對資料進行採樣而得到不可預期的結果
約束:<br>
![Image](https://github.com/vita70579/VLSI/raw/main/Image/DFF.png)
![Image](https://github.com/vita70579/VLSI/raw/main/Image/timing.png)
<p align="center">
  - Tcq: clock觸發至Q成功將D鎖出的時間 (暫存器延遲)
  - Tskew: clock skew
  - Tcomb: 組合邏輯運算時間<br>
    setup time: (Tclk+Tskew)-(Tcq+Tcomb)>Tsetup<br>
    hold time: (Tcq+Tcomb)>(Thold+Tskew)<br>
