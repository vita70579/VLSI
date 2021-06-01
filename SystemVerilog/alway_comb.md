# `always_comb`

always_comb:= `combinational circuit` `multi assign statement`

```verilog
always_comb begin
    a = 1'b1;
    b = a;
    a = 1'b0;
    c = a;
end
```

## Nature：

* 内部覆盖性

* 對外原子性

  ```verilog
  assign a = b; // a = 0
  always_comb begin
      b = 1'b1;
      c = a; // c = b
      b = 1'b0;
  end
  ```
