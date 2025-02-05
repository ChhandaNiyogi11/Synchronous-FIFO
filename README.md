# Synchronous FIFO Implementation

This repository contains the Verilog code for a **Synchronous FIFO (First In, First Out)** memory buffer. The FIFO is designed to handle efficient data storage and retrieval operations in a sequential manner, adhering to synchronous clocked behavior.

## Features

- **Parameterizable Depth**: The FIFO depth can be customized by setting the `depth` parameter.
- **Empty and Full Flags**: Provides status indicators to signal when the FIFO is empty or full.
- **Clock-Driven Operations**: Synchronous read and write operations based on the clock signal.
- **Reset Logic**: Ensures proper initialization of the FIFO pointers and data output.

## Module Overview

The main module is defined as follows:

```verilog
module fifo #(parameter depth=8)(
    input clk, rst, wr_en, rd_en,
    input [7:0] d_in,
    output full, empty,
    output reg [7:0] d_out
);
parameter pointer_width = $clog2(depth);
reg [pointer_width:0] wr_ptr, rd_ptr;
reg [7:0] mem[depth-1:0];

// Reset Logic
always @(posedge clk) begin
    if (!rst) begin
        wr_ptr <= 0;
        rd_ptr <= 0;
        d_out <= 0;
    end
end

// Writing into FIFO buffer
always @(posedge clk) begin
    if (wr_en && !full) begin
        mem[wr_ptr[pointer_width-1:0]] <= d_in;
        wr_ptr <= wr_ptr + 1;
    end
end

// Reading from FIFO buffer
always @(posedge clk) begin
    if (!rst) d_out <= 0;
    else if (rd_en && !empty) begin
        d_out <= mem[rd_ptr[pointer_width-1:0]];
        rd_ptr <= rd_ptr + 1;
    end
end

// Status flags
assign empty = (wr_ptr == rd_ptr);
assign full = ({~wr_ptr[pointer_width], wr_ptr[pointer_width-1:0]} == rd_ptr);

endmodule
```

### Parameters
- `depth`: Defines the number of entries the FIFO can hold.

### Inputs
- `clk`: Clock signal.
- `rst`: Reset signal (active low).
- `wr_en`: Write enable signal.
- `rd_en`: Read enable signal.
- `d_in [7:0]`: 8-bit data input.

### Outputs
- `full`: High when the FIFO is full.
- `empty`: High when the FIFO is empty.
- `d_out [7:0]`: 8-bit data output.

## How It Works

1. **Initialization**:
   - Upon reset, the write pointer (`wr_ptr`), read pointer (`rd_ptr`), and data output (`d_out`) are set to 0.

2. **Writing Data**:
   - Data is written into the memory array (`mem`) at the location specified by `wr_ptr`.
   - The write pointer is incremented after each write operation.
   - Writing is allowed only if the FIFO is not full.

3. **Reading Data**:
   - Data is read from the memory array (`mem`) at the location specified by `rd_ptr`.
   - The read pointer is incremented after each read operation.
   - Reading is allowed only if the FIFO is not empty.

4. **Status Flags**:
   - The `empty` flag is high when the read and write pointers are equal.
   - The `full` flag is high when the pointers indicate that the FIFO is completely filled.

## Applications

- Data buffering.
- Rate matching between producer and consumer processes.

## Contributions

Contributions are welcome! Feel free to submit issues or pull requests to enhance the functionality of this module.

