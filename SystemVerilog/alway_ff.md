# `always_ff`

## `循序電路`
`always_ff`用於描述觸發器。
```verilog
always_ff @(posedge clk) begin
    if (~resetn) begin
        q <= '0;
    end else if (en) begin
        q <= d;
    end
end
```
```verilog
logic [3:0] cr_st, nx_st;

always_ff @(posedge clk) begin
    if (~resetn) begin
        cr_st <= '0;
    end else if (en) begin
        cr_st <= nx_st;
    end
end
```
