# `precomplie`

## `C語言中的預編`

```c
#include <stdio.h>

#ifndef __SHARE_H
#define __SHARE_H

#endif

#define N 1000000 + 3

int a[N];
```
## `SystemVerilog`
有了預編命令,就可以利用header file,提升代碼可讀性。
SV中的預編命令,和C基本一致,用`` ` ``開頭。

## Stntax
```verilog
`include "mips.svh" // header file (.svh)

`ifndef __SHARE_SVH
`define __SHARE_SVH

`endif

`define LINES 0x10

logic a[`LINES-1:0];
```
## Usage

* 配置一些參數,類似`parameter`。
* 根據不同config,生成不同電路。
  * `generate if`语句的粒度为电路语句。而```ifdef``之类的预编译命令可以是任意粒度的。
    ```verilog
    assign a = b + c
      #ifdef D_INSIDE
      + d
      #endif
    ;
    
    generate if (D_INSIDE) begin
      assign a = b + c + d;
    end else begin
    	assign a = b + c;    
    end
    ```
* 使用頭文件,類似`package`。
