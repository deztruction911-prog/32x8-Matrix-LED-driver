# 32x8-Matrix-LED-driver

README for the MASM_32x8LED_Driver

How to install:

First copy the .ZIP file, then go to Arduino/libraries in file explorer. Paste the .ZIP file. Then open the IDE, click on "Sketch", then "Include Library", then "Include .ZIP Library". After that, click on MASM_32_8LED_Driver. If it worked, you should see it in the "Contributed Libraries" section.




About:
the MASM_32x8LED_Driver is a simple, custom, driver for the 32x8 LED Matrix. It does not include any external libraries. It uses about 44 bytes of RAM and 2404 bytes of flash (Program storage space) as it is a very minimalistic library. It allows the user to display custom characters, or light up specific pixels.




Important variables:
pixels[8]
a 32 byte memory buffer which is written to by other functions before DisplayMemory is called to push it to the Matrix. It is divided into 8 rows (0-7)


PixelOn/PixelOff: Used with state in SetPixel

UpdatePixel/NoUpdate: Used with any function containing the "update" parameter. Simply tells the function to either display the change or not. Will still write to pixels

ShiftDown/ShiftUp: Used with the ShiftCol functions

ShiftLeft, ShiftRight: Used with the ShiftRow function or ShiftPixel

Commands:
nop: Tells the display not to do anything with the data it receives.

row1: Tells the display that the data is for row 1 of it's block (there are 4 blocks).
row2: Tells the display that the data is for row 2 of it's block.
row3: Tells the display that the data is for row 3 of it's block.
row4: Tells the display that the data is for row 4 of it's block.
row5: Tells the display that the data is for row 5 of it's block.
row6: Tells the display that the data is for row 6 of it's block.
row7: Tells the display that the data is for row 7 of it's block.
row8: Tells the display that the data is for row 8 of it's block.

DecodeMode, brightness, ScanLimit, shutdown, and DispTest are all explained in the .h file.




Functions:

SendFull(cmda, data, cmdb, datb, cmdc, datc, cmdd, datd)
Sends 4 commands and 4 pieces of data to the matrix. The 64 bit packet it sends consists of 4
16 bit packets where the upper byte is the command/instruction and the lower byte is the data for that instruction


DisplayMemory()
Pushes the 32 byte memory buffer to the Matrix. The memory buffer itself cannot be bypassed except by SendFull, SendPacket, and SendByte. That means all other functions write to the buffer and call DisplayMemory to push the buffer and update the display


SetRow(row, RowData, update)
Sets pixels[row] to the RowData, a 32 bit value.
0000 0000 0000 0000 0000 0000 0000 0000
This is how the pixels are seen. RowData will be treated like this. This means that 2 will light up the second pixel but 3 will light up the second and third.


SetCol(ColNum, ColData, update)
Sets a column (0-31) based on ColNum. ColData will be treated as an 8 bit value where the LSB is treated as the topmost pixel and the MSB as the bottommost.


EnablePixel(yPos, xPos, update)
Sets a pixel at (xPos, yPos) to 1 regardless of it's previous state. 0, 0 is simply the top right corner. Uses OR to change only the required pixel


DisablePixel(yPos, xPos, update)
It's similar to EnablePixel but does the opposite, setting the pixel at (xPos, yPos) to 0 regardless of it's previous state. Uses AND and NOT to set only the selected pixel.
First it shifts 1 to it's required xPos E.X: (1<<5) to get 0x0010
Then it NOTs it to make it 0xffef. This makes sure that any other pixels are AND by 1, which ensures they remain the same state. The required pixel is AND by 0, which disables it.


TogglePixel(yPos, xPos, update)
Uses XOR to toggle a pixel, if it was 1, it will now be 0, and etc


SetPixel(yPos, xPos, state, update)
Sets a pixel at (xPos, yPos) to either high or low depending on whether state is 1 or 0. To replace state, you can use the pre-defined PixelOn or PixelOff.


ShiftPixel(yPos, xPos, dir, update)
Shifts a pixel at (xPos, yPos) left or right depending on if dir is 1 or 0. To replace dir, you can use ShiftLeft or ShiftRight.


ShiftRow(RowNum, dir, update)
Shifts the entire row at RowNum either left or right if dir is ShiftLeft or ShiftRight.


ShiftAllRow(dir, update)
Shifts every row either left or right if dir is ShiftLeft or ShiftRight.


ShiftCol(ColNum, dir, update)
Shifts a column at ColNum either up or down depending on if dir is 1 or 0. To replace dir, you can use ShiftUp or ShiftDown


ShiftAllCol(dir, update)
Shifts every column up or down if dir if either ShiftUp or ShifttDown


ClearAll()
Clears the entire display using a very cool animation. If needed, feel free to change it or remove said animation


SendChar(CharacterArray[8], offset, update)
Sends out a custom character array which must have 8 elements and in each element a 5 bit binary number with a 1 or 0 depending on which LED's you want on. offset is simply how far the character will be displayed from xPos 0


BootMatrix(data, clock, ChipSelect)
These reference the pins. Include BootMatrix in setup() with the pins connected to Din, CLK, and CS on the matrix display. Make sure this is the first function called from MASM_32x8LED_Driver as it handles all the pin mappings
