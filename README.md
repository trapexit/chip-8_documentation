# CHIP-8

## Versions

* CHIP-8: Interpreter / VM created for RCA 1802 based machines designed for writing simple video games. Later picked up by HP-48 enthusiasts.
* CHIP-8C: An (unreleased?) version of the CHIP-8 language mentioned in VIPER Volume 1 Issue 2.
  * `New from RCA. The VIP will be sporting vivid color this fall with the introduction of the VIP COLOR BOARD from RCA. You'll have program control of three background colors & eight foreground colors with CHIP-8C, the color-langauge addition to CHIP-8. Available late October. Priced under $80.00.`
* CHIP-8E: Extended instructions found in Paul C. Moews' 1979 `Programs for the COSMAC ELF Interpreters`.
* CHIP-8X: `an expanded version of the original CHIP-8 interpreter which allows easy control over RCA's new options for the VIP: color card, simple sound, and expansion hex keyboard.`
* CHIP-8I: From `VIPER Volume 1 Issue 03`: `A Modification of CHIP-8 to Provide I/O Instructions` by Rick Simpson
* CHIP-10: A high resolution mode (128x64) on top of the CDP-1861 as described in `VIPER Volume 1 Issue 7`.
* HiRes CHIP-8: A version with a high resolution (64x64) mode.
* SCHIP-8: An extended version of CHIP-8 created originally for the HP-48 calculator. Provided twice the resolution of the original CHIP-8 platform plus several additional instructions.

**NOTE:** Some of the above conflict with one another which is why you may see opcodes that overlap.

## Opcodes

Each opcode is 16bits. References to registers are usually refered to by `X` and `Y`. Immediate values as `N`, `NN`, and `NNN`. `NNN` used for 12bit addresses. Registers include 8bit `V0` through `VF`, 12bit memory location `I`, 12bit program counter `PC`, 8bit delay timer `DT`, 8bit sound timer `ST`. There is also a 12bit stack pointer `SP`.

| Opcode | Mnemonic | Origin/Version | Description |
|--------|----------|----------------|-------------|
| 001N   | EXIT N   | CHIP-8  | From Peter Miller's chip8run. Exit emulator with a return value of `N`. |
| 00CN   | SCD N    | SCHIP-8 | Scroll display `N` lines down. |
| 00E0   | CLS      | CHIP-8  | Clears the display. Sets all pixels to `off`. |
| 00EE   | RET      | CHIP-8  | Return from subroutine. Set the `PC` to the address at the top of the stack and subtract `1` from the `SP`. |
| 00FA   | COMPAT   | CHIP-8  | Non-standard. Toggles changing of the `I` register by `save (FX55)` and `restore (FX65)` opcodes. |
| 00FB   | SCR      | SCHIP-8 | Scroll display `4` pixels to the right. |
| 00FC   | SCL      | SCHIP-8 | Scroll display `4` pixels to the left. |
| 00FD   | EXIT     | SCHIP-8 | Exit the interpreter. |
| 00FE   | LOW      | SCHIP-8 | Enable low res (64x32) mode. |
| 00FF   | HIGH     | SCHIP-8 | Enable high res (128x64) mode. |
| 0NNN   | CALL NNN | CHIP-8  | Call machine language subroutine at address `NNN`. |
| 02A0   | STEPCOL  | CHIP-8X | Steps background 1 color (-> blue -> black -> green -> red ->) |
| 1NNN   | JMP NNN  | CHIP-8  | Set `PC` to `NNN`. |
| 2NNN   | CALL NNN | CHIP-8  | Call subroutine a `NNN`. Increment the `SP` and put the current `PC` value on the top of the stack. Then set the `PC` to `NNN`. Generally there is a limit of 16 successive calls. |
| 3XNN   | SE VX, NN | CHIP-8 | Skip the next instruction if register `VX` is equal to `NN`. |
| 4XNN   | SNE VX, NN | CHIP-8 | Skip the next instruction if register `VX` is **not** equal to `NN`. |
| 5XY0   | SE VX, VY | CHIP-8 | Skip the next instruction if register `VX` equals `VY`. |
| 5XY1   | SGT VX, VY | CHIP-8E | Skip the next instruction if register `VX` is greater than `VY`. |
| 5XY1   | ADD VX, VY | CHIP-8X | Let `VX = VX + VY` (hex digits 00 to 77) (useful for manipulating the `NH, NV` parameters for low resolution color.) |
| 5XY2   | SLT VX, VY | CHIP-8E | Skip the next instruction if register `VX` is less than `VY`. |
| 5XY3   | SNE VX, VY | CHIP-8E | Skip the next instruction if register `VX` does not equal `VY`. |
| 6XNN   | LD VX, NN | CHIP-8 | Load immediate value `NN` into register `VX`. |
| 7XNN   | ADD VX, NN | CHIP-8 | Add immediate value `NN` to register VX. Does **not** effect `VF`. |
| 8XY0   | LD VX, VY | CHIP-8 | Copy the value in register `VY` into `VX`
| 8XY1   | OR VX, VY | CHIP-8 | Set `VX` equal to the bitwise `or` of the values in `VX` and `VY`. |
| 8XY2   | AND VX, VY | CHIP-8 | Set `VX` equal to the bitwise `and` of the values in `VX` and `VY`. |
| 8XY3   | XOR VX, VY | CHIP-8 | Set `VX` equal to the bitwise `xor` of the values in `VX` and `VY`. **Note:** This instruction was originally undocumented but functional due to how the 8XXX instructions were implemented on teh COSMAC VIP. |
| 8XY4   | ADD VX, VY | CHIP-8 | Set `VX` equal to `VX` plus `VY`. In the case of an overflow `VF` is set to `1`. Otherwise `0`. |
| 8XY5   | SUB VX, VY | CHIP-8 | Set `VX` equal to `VX` minus `VY`. In the case of an underflow `VF` is set `0`. Otherwise `1`. (`VF = VX > VY`) |
| 8XY6   | SHR VX, VY | CHIP-8 | Set `VX` equal to `VX` bitshifted right `1`. `VF` is set to the least significant bit of `VX` prior to the shift. Originally this opcode meant set `VX` equal to `VY` bitshifted right `1` but emulators and software seem to ignore `VY` now. **Note:** This instruction was originally undocumented but functional due to how the 8XXX instructions were implemented on teh COSMAC VIP. |
| 8XY7   | SUBN VX, VY | CHIP-8 | Set `VX` equal to `VY` minus `VX`. `VF` is set to `1` if `VY` > `VX`. Otherwise `0`. **Note:** This instruction was originally undocumented but functional due to how the 8XXX instructions were implemented on teh COSMAC VIP. |
| 8XYE   | SHL VX, VY | CHIP-8 | Set `VX` equal to `VX` bitshifted left `1`. `VF` is set to the most significant bit of `VX` prior to the shift. Originally this opcode meant set `VX` equal to `VY` bitshifted left `1` but emulators and software seem to ignore `VY` now. **Note:** This instruction was originally undocumented but functional due to how the 8XXX instructions were implemented on teh COSMAC VIP. |
| 9XY0   | SNE VX, VY | CHIP-8 | Skip the next instruction if `VX` does **not** equal `VY`. |
| 9XY1   | MUL VX, VY | CHIP-8E | Set `VF`, `VX` equal to `VX` multipled by `VY` where `VF` is the most significant byte of a 16bit word. |
| 9XY2   | DIV VX, VY | CHIP-8E | Set `VX` equal to `VX` divided by `VY`. `VF` is set to the remainder. |
| 9XY3   | BCD VX, VY | CHIP-8E | Let `VX`, `VY` be treated as a 16bit word with `VX` the most significant part. Convert that word to BCD and store the 5 digits at memory location `I` through `I+4`. `I` does not change. |
| ANNN   | LD I, NNN | CHIP-8 | Set `I` equal to `NNN`. |
| BNNN   | JMP V0, NNN | CHIP-8 | Set the `PC` to `NNN` plus the value in `V0`. |
| B0NN   | OUT NN | CHIP-8I | Output `NN` to port. |
| B1X0   | OUT VX | CHIP-8I | Output contents of `VX` to port. |
| B1X1   | IN VX | CHIP-8I | Read input from port and palce in `VX`. |
| BXY0   | COL VX, VY | CHIP-8X | Set `VY` color @ `VX(NH)`, `VX+1(NV)` (provides low resolution color 8x8.) |
| BXYN   | COL VX, VY, N | CHIP-8X | `N != 0`, set `VY` color @ `VX`, `VX+1` byte `N` bytes vertically (provides high resolution 8x32.) |
| CXNN   | RND VX, NN | CHIP-8 | Set `VX` equal to a random number ranging from `0` to `255` which is logically `and`ed with `NN`. |
| DXY0   | DRW VX, VX, 0 | SCHIP-8 | When in high res mode show a `16x16` sprite at `(VX, VY)`. |
| DXYN   | DRW VX, VY, N | CHIP-8 | Display `N`-byte sprite starting at memory location `I` at `(VX, VY)`. Each set bit of `xor`ed with what's already drawn. `VF` is set to `1` if a collision occurs. `0` otherwise. |
| EX9E   | SKP VX | CHIP-8 | Skip the following instruction if the key represented by the value in `VX` is pressed. |
| EXA1   | SKNP VX | CHIP-8 | Skip the following instruction if the key represented by the value in `VX` is **not** pressed. |
| EXF2   | SKP2 VX | CHIP-8X | Skip the following instruction if the key represented by the value in `VX` is pressed on hex keyboard 2. |
| EXF5   | SKNP2 VX | CHIP-8X | Skip the following instruction the the key represented by the value in `VX` is **not** pressed on hex keyboard 2. |
| FX07   | LD VX, DT | CHIP-8 | Set `VX` equal to the `delay timer`. |
| FX0A   | LD VX, KEY | CHIP-8 | Wait for a key press and store the value of the key into `VX`. |
| FX15   | LD DT, VX | CHIP-8 | Set the delay timer `DT` to `VX`. |
| FX18   | LD ST, VX | CHIP-8 | Set the sound timer `ST` to `VX`. |
| FX1E   | ADD I, VX | CHIP-8 | Add `VX` to `I`. `VF` is set to `1` if `I > 0x0FFF`. Otherwise set to `0`. |
| FX29   | LD I, FONT(VX) | CHIP-8 | Set `I` to the address of the CHIP-8 8x5 font sprite representing the value in `VX`. |
| FX30   | LD I, FONT(VX) | SCHIP-8 | Set `I` to the address of the SCHIP-8 16x10 font sprite representing the value in `VX`. |
| FX33   | BCD VX | CHIP-8 | Convert that word to BCD and store the 3 digits at memory location `I` through `I+2`. `I` does not change. |
| FX55   | LD [I], VX | CHIP-8 | Store registers `V0` through `VX` in memory starting at location `I`. `I` does not change. '
| FX65   | LD VX, [I] | CHIP-8 | Copy values from memory location `I` through `I + X` into registers `V0` through `VX`. `I` does not change. |
| FX75   | DISP VX | CHIP-8E | Display the value of `VX` on the COSMAC Elf hex display. |
| FX75   | LD R, VX | SCHIP-8 | Store `V0` through `VX` to HP-48 RPL user flags (X <= 7). |
| FX85   | LD VX, R | SCHIP-8 | Read `V0` through `VX` to HP-48 RPL user flags (X <= 7) |
| FX94   | LD I, VX | CHIP-8E | Load `I` with the address of the font sprite of the `ASCII` value found in `VX`. |
| FXFB   | IN VX | CHIP-8X | Copy contents from `input port` to `VX`. (Waits for EF4=1) |
| FXF8   | OUT VX | CHIP-8X | Output contents of `VX` to `output port`. Used to program simple sound. |

## Sound
TBD

## Display
TBD

## Control
TBD
