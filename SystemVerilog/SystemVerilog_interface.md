# `interface`
電路圖清晰地標明了元件的每位輸入是從哪個元件的輸出得到的,而元件例化語句無法做到這一點:

## Nature
* 元件例化語句部標出訊號是作為輸入還是輸出。
* 訊號相關模塊地例化代碼可能隔了很遠。
* 不同模塊可能複用同一部分接口。
* 添加一個街口需要修改多處代碼。


## Syntax
```verilog
interface interface_name(input logic d, output logic e);
    // signals
    logic c;
    
    // modports
    modport modport_name1(input c, d, output e);
    modport modport_name2(output c);
endinterface
```
```verilog
// module
module module_name(
	interface_name.modport_name1 variable_name // input logic c, d, output logic e
);
    // use `variable_name` like a struct
    logic d;
    assign d = variable_name.c;
    
    assign variable_name.e = d;
endmodule
```
```verilog
module top();
    // instantiate an interface involves declaring the singals inside it
    interface_name intf_inst(.d(), .e());
    // logic c, d, e
    module_name instance_name(.variable_name(intf_inst.modport_name1));
    
    interface_name intf_inst2(.d(), .e());
    ...
endmodule
```

* interface的宣告放到header file裡,可以大幅減少代碼量。
* Verilator暫不支持interface語法。

