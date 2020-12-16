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
