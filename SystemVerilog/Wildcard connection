# Wildcard connection

```verilog
module adder (
  // inputs and outputs declaration
  input logic [3:0] a, b,
  output logic [3:0] c
);
// circuit code
assign c = a + b;
endmodule
```

## 2 Wildcard connection
```verilog
logic [3:0] b, c;
//logic [4:0] c;
adder adder_inst0(.a(4'b0010), .*); // adder adder_inst0(.a(4'b0010), .b(b), .c(c))
// modulename instancename(.portname1(value1), .portname2, .*);
```
