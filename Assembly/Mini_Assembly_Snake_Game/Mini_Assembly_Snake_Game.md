# Mini Assembly Snake Game (x86 Real-Mode Bootloader)

This document was written by me and GPT together.

[source_code](source_code.md)

## Overview

This project is a 16-bit x86 real-mode bootloader written in assembly language that runs directly on IBM PC–compatible hardware. The program is loaded by the BIOS at boot time and executes without any operating system. It implements a minimal version of the classic **Snake** game using BIOS interrupts and direct memory manipulation for graphics rendering, keyboard input, timing control, and game logic.

Unlike applications running under DOS or modern operating systems, this program operates in a **bare-metal environment**, meaning:

* No OS services are available.
* No standard libraries are linked.
* All hardware interaction is performed via BIOS interrupts or direct memory access.

## Execution Environment

### Bootloader Context

When a PC boots:

1. The BIOS loads the first 512 bytes from the boot device into memory at physical address `0x7C00`.
2. The CPU begins executing from `CS:IP = 0000:7C00`.
3. The boot sector must end with the signature `0xAA55`.

The program begins with:

```asm
[org 0x7c00]
[bits 16]
```

* `org 0x7c00` tells the assembler the code will be loaded at address `0x7C00`.
* `bits 16` ensures 16-bit instruction encoding.

## Video Mode Initialization

### VGA Mode 13h

The game uses **VGA Mode 13h**, which provides:

* Resolution: 320 × 200 pixels
* Color depth: 256 colors
* Linear framebuffer
* Framebuffer size: 320 × 200 = 64,000 bytes
* Video memory segment: `0xA000`

Initialization code:

```asm
start:
    xor ax, ax
    mov ds, ax
    mov ax, 0x0013
    int 0x10
    mov ax, 0xa000
    mov es, ax
```

### Explanation

* `xor ax, ax` → Clear AX.
* `mov ds, ax` → Set data segment to 0.
* `mov ax, 0x0013` / `int 0x10` → BIOS interrupt to switch to Mode 13h.
* `mov es, 0xA000` → ES points to VGA framebuffer.

From this point onward, writing to `[es:offset]` directly modifies pixels on screen.

## Screen Clearing

```asm
init_game:
    xor di, di
    mov cx, 32000
    xor eax, eax
    rep stosw
```

### Why 32000?

* Each `stosw` writes **2 bytes**.
* 32000 words × 2 bytes = 64000 bytes (full framebuffer).

### Mechanism

* `DI = 0`
* `ES = 0xA000`
* `rep stosw` clears entire screen to color 0 (black)

This is an efficient memory-clearing technique in 16-bit assembly.

## Game State Representation

### Memory Layout

The snake’s body is stored in conventional memory starting at address `0x8000`.

```asm
mov word [0x8000], 32160
mov si, 0x8000
mov bx, 0x8000
mov bp, 1
```

### Register Usage

| Register    | Purpose                  |
| ----------- | ------------------------ |
| `SI`        | Snake head pointer       |
| `BX`        | Snake tail pointer       |
| `BP`        | Direction increment      |
| `[0x8000+]` | Snake body position list |

### Position Encoding

Each snake segment stores a **linear framebuffer offset** (0–63999).

Pixel address formula:

```plaintext
offset = y * 320 + x
```

Initial position `32160` corresponds to:

```plaintext
32160 / 320 = 100.5 → approx middle of screen
```

## Apple Generation

```asm
spawn_apple:
    rdtsc
    xor dx, dx
    mov cx, 64000
    div cx
    mov di, dx
    cmp byte [es:di], 0
    jne spawn_apple
    mov byte [es:di], 40
```

### Randomness Source

* `rdtsc` reads CPU timestamp counter.
* The result is divided by 64000.
* The remainder (`DX`) becomes a random pixel offset.

### Placement Logic

1. Choose random offset.
2. Check if pixel is empty (`0`).
3. If occupied → retry.
4. If empty → write apple color (`40`).

Note: `rdtsc` requires a 586+ CPU.

## Main Game Loop

```asm
game_loop:
    mov ah, 0x86
    mov cx, 0
    mov dx, 0x4000 
    int 0x15
```

### Timing Control

* BIOS `int 0x15`, AH=0x86 provides a delay function.
* `CX:DX` specifies microsecond delay.
* Controls game speed.

## Keyboard Input Handling

```asm
mov ah, 0x01
int 0x16
jz update_pos

xor ah, ah
int 0x16
```

### Logic

* AH=01 → check if key available.
* If zero flag set → no key.
* AH=00 → read key.

Direction control:

```asm
cmp al, 'w'
je move_up
cmp al, 's'
je move_down
cmp al, 'a'
je move_left
cmp al, 'd'
je move_right
```

Uses ASCII comparison for WASD control.

## Movement and Collision Detection

```asm
update_pos:
    mov ax, [si]
    add ax, bp
    mov di, ax
```

### Movement Model

* Snake head position stored at `[SI]`
* Direction stored in `BP`
* New position = current position + direction

### Boundary Check

```asm
cmp ax, 64000
jae game_over
```

Only checks upper boundary; edge wrapping and vertical boundary checks are incomplete.

### Collision Logic

```asm
cmp byte [es:di], 40
je eat_apple
cmp byte [es:di], 0
jne game_over
```

Cases:

| Pixel Value | Meaning    |
| ----------- | ---------- |
| 0           | Empty      |
| 2           | Snake body |
| 40          | Apple      |

If pixel ≠ 0 and ≠ 40 → collision.

## Snake Growth Logic

### Normal Move

```asm
mov byte [es:di], 2
add si, 2
mov [si], di

mov di, [bx]
mov byte [es:di], 0
add bx, 2
```

Process:

1. Draw new head.
2. Append new head position.
3. Remove tail.
4. Advance tail pointer.

Snake length remains constant.

### Apple Consumption

```asm
eat_apple:
    mov byte [es:di], 2
    add si, 2
    mov [si], di
    jmp spawn_apple
```

Difference:

* Tail is NOT removed.
* Snake grows by one segment.
* New apple generated.

## Game Over

```asm
game_over:
    jmp start
```

Game restarts by reinitializing environment.

No score tracking or message display due to boot sector size constraints.

## Boot Sector Footer

```asm
times 510-($-$$) db 0
dw 0xaa55
```

### Explanation

* Pads program to 510 bytes.
* Appends mandatory boot signature `0xAA55`.

Without this signature, BIOS will refuse to boot.

## Building the Boot Image

### Assemble

```bash
nasm -f bin boot.asm -o snake.img
```

### Run with QEMU

```bash
qemu-system-i386 snake.img
```

### Write to USB Drive (Linux/macOS Example)

```bash
sudo dd if=snake.img of=/dev/sdX bs=512
```

Replace `/dev/sdX` with correct device.

(todo) Windows runs rufus.exe

## Technical Limitations

1. Incomplete boundary detection (horizontal wrapping not handled).
2. No score display.
3. No structured memory protection.
4. Requires 586+ CPU for `rdtsc`.
5. Entire program constrained to 512 bytes.
