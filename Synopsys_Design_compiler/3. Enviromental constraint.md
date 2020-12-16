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
