# `parameter`

```verilog
module adder #(
  parameter int N,
  parameter logic [31:0] W = 32'd100000,
  parameter type element_t = logic[31:0]
)(
  input logic[N-1:0] a, b,
  output logic[N-1:0] c
);
  assign c = a + b;
endmodule

module top #(
  parameter logic SIM = 1'b0  //  1'b1-simulation; 1'b0-on board
)(input logic clk, resetn
 );
  logic [31:0] a, b, c;    
  adder #(.N(32), .element_t(logic [63:0])) adder_inst1(.a, .b, .c);  // 32bits adder
  logic [15:0] d, e, f;
  adder adder_inst2(.a(d), .b(e), .c(f)); // 16bits adder
endmodule

module sim();
    top #(.SIM(1'b1)) top_inst (.clk, .resetn); // simulation
    // -DDEBUG
endmodule
```

## Global `parameter`
```verilog
parameter logic[5:0] OP_ADDI = 6'b000010;

always_comb begin
  unique case(op)
    OP_ADDI: begin
      ...
    end
  endcase
end
```
