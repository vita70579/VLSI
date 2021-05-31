# always_comb中的控制語句：case

## `unique case`:並行比較
```verilog
always_comb begin
    b = 1'b0;
    unique case (a[3:0])
        4'd1: begin
            b = 1'b1;
        end
        4'd0: begin
            b = 1'b0;
        end
        default: begin    
        end
    endcase
end
```
## `priority case`:順序比較:=if...else...
```verilog
always_comb begin
    priority case (1'b1)
        a[3]: begin
        end
        a[2]: begin 
        end
        default: begin
        end
    endcase
end
```
```verilog
always_comb begin
    if(a[3]) begin
        
    end else if(a[2]) begin
        
    end else if() begin  end //...
end
```
