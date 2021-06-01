## `If`
```verilog
always_comb begin
    b = 1'b0;
    if (a[3]) begin
        b = 1'b1;
    end else if (a[2]) begin
        b = 1'b0;
    end else begin   
    end
end
```
## `For`
1. for在always_comb中，會被`展開`。
2. for相關的語句：`break`，`continue`
```verilog
logic [15:0]a;
logic [3:0] b;

always_comb begin
    b = '0;
    for (int i = 15; i >= 0; i--) begin
        if (a[i]) begin
            b = i[3:0];
            break;
        end
    end
end
```

`循環變量上下界應為常數。`

```verilog
always_comb begin
    for (int i = 0; i < 16; i++) begin
        a[i] = b[i] & (c[i] == d[i] | e[i]); // compiler不認為i是常數，a[i:i+3]非法
    end
end

for (genvar i = 0; i < 16; i++) begin
    assign a[i] = b[i] & (c[i] == d[i] | e[i]); // compiler認為i是常數，a[i:i+3]合法
    always_comb begin
        
    end
end
```
