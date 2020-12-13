![Image](https://github.com/vita70579/VLSI/raw/main/Image/im13.png)<br>
Clock Definition
================
1. Clock **clk** has a frequency of 333.33MHz.
- **Tclk = 3ns**
2. The maximum external clock generator delay to the clock port is 700ps.
- **source latency = 700ps = 0.7ns**
3. The maximum insertion delay from the clock port to all the register clock pins is 300ps +/- 30ps.
- **network latency = 300ps = 0.3ns**
- **clock skew = 30ps = 0.03ns**
4. The clock period can fluctuate +/- 40ps due to jitter.
- **jitter = 40ps = 0.04ns**
5. Apply 50ps of "setup margin" to the clock period.
- **setup uncertainty = 0.03+0.03+0.04+0.05 = 0.15ns**
6. The worst case rise/fall transition time of any clock pin is 120ps.
- **transition = 120ps = 0.12ns**

Register setup time
===================
Assume a maximum setup time of 0.2ns for any register.

Input ports (sequential logic)
==============================
1. The maximum delay from ports data1 and data2 through the internal input logic S is 2.2ns. <br>
![Image](https://github.com/vita70579/VLSI/raw/main/Image/im14.png)<br>
**Tclk - (Tcq + TA + Ts) > Tsetup <br>
Tcq + TA < Tclk - Ts -Tsetup <br>
Maximum input daley = (Tcq +TA)max = Tclk - Ts -Tsetup -Tuncertainty = 3.0 - 0.15 - 2.2 - 0.2 = 0.45ns**
2. The latest F3 data arrival time at the sel ports is 1.4ns absolute time. <br>
![Image](https://github.com/vita70579/VLSI/raw/main/Image/im15.png)<br>
- **absolute time = source latency + network leatency + Tcq + TB**
- **input delay = Tcq + TB = absolute time - source latency - network latency = 1.4 - 0.7 - 0.3 = 0.4ns**
