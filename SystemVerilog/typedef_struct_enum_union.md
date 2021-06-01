# `typedef`

數字電路中,萬物皆為二進制。
類型統一為`logic`,符合這一規律,但這對樣的代碼較無可讀性。

* 需要管理變量的位數。
* 同一位數的訊號,具有不同意義。

## Format：`typedef` `existing_type` `new_type;`

```verilog
typedef logic[31:0] word_t;
typedef logic[5:0] entry_t;
typedef entry_t[31:0] table_t;
```

## Syntax：

```verilog
word_t a, b;
assign b = {a[15:0], a[31:16]};

table_t table1; // logic [31:0][5:0]
assign table1[1] = '0;
assign table1[0][1] = '0;
```

## Example：

```verilog
typedef logic[3:0] code_t;
typedef logic[15:0] info_t;

typedef logic[31:0] paddr_t; // physical address
typedef logic[31:0] vaddr_t; // virtual address
```



# `struct`
struct可以描述一組相關的數據。
```verilog
alufunc_t alufunc;
logic mem_read;
logic mem_write;
logic regwrite;

logic [6:0] control;
assign control = {alufunc, mem_read, mem_write, regwrite};
```

## Syntax：

```verilog
// type definition
typedef struct packed {
    logic [3:0] alufunc;
    logic mem_read;
    logic mem_write;
    logic regwrite;
    logic [3:0] reg_addr;
} control_t;

// variable declaration
control_t control;

logic regwrite;
assign regwrite = control.regwrite; // assign regwrite = control[4];

// using structs without typedef
struct packed {
    logic [3:0] alufunc;
    logic mem_read;
    logic mem_write;
    logic regwrite;
} control_without_typedef;
```

## Usage:

```verilog
typedef struct packed {
    
} pipeline_decode_t;

pipeline_decode_t p, p_nxt;
always_ff @(posedge clk) begin
    p <= p_nxt;
end
```



# `enum`

## Format：

```verilog
typedef enum <datatype> {
    IDEN_1, IDEN_2
} typename;
```

## Example：

```verilog
typedef enum logic [3:0] {
    ALU_ADD, ALU_AND, ALU_SUB
} alufunc_t;

alufunc_t alufunc;
```

1. 狀態機編碼
2. 類型的變量預設編碼起始為0。
3. enum item 名稱不可衝突。
4. Vivado simulation 中會顯示item name。
5. enum 類型變數只能被賦值為item。

```verilog
typedef enum logic [1:0] {
    STATE_0, STATE_1, STATE_2
} state_t;

state_t state, state_nxt;
always_ff @(posedge clk) begin
    if (~resetn) begin
        // state <= '0; illegal
        state <= state_t'(0); // = state <= STATE_0 (illegal)
    end else begin
        state <= state_nxt;
    end
end
```

# `union`

## Syntax：

```verilog
typedef union packed {
    struct packed {
        logic zero;
        logic [31:0] aluout;
    } alu;
    struct packed {
        logic branch_taken;
        logic [31:0] pcbranch;
    } branch;
    struct packed {
        logic [31:0] addr;
        logic mem_read;
    } memory;
} result_t;

result_t res;

logic [31:0] addr, aluout;
assign addr = res.memory.addr; // assign addr = res[32:1] (線路複用)
assign aluout = res.alu.aluout; // assign aluout = res[31:0] (線路複用)
```
