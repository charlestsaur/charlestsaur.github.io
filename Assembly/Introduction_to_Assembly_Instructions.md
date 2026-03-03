# Introduction to Assembly Instructions

This document was written by me and GPT together.

| Instruction | Meaning / Common Use                             |
|-------------|--------------------------------------------------|
| mov         | Move data / Assignment                           |
| xor         | XOR (frequently used to zero a register)         |
| int         | Interrupt call (BIOS / DOS / software interrupt) |
| rep         | Repeat (used with string instructions)           |
| rdtsc       | Read Time Stamp Counter                          |
| div         | Unsigned divide                                  |
| cmp         | Compare two operands                             |
| jne         | Jump if not equal                                |
| je          | Jump if equal                                    |
| jz          | Jump if zero                                     |
| jmp         | Jump (unconditional)                             |
| cld         | Direction flag = 0 (forward string ops)          |
| std         | Direction flag = 1 (backward string ops)         |
| push        | Put value onto stack                             |
| pop         | Remove value from stack                          |
| call        | Call subroutine (push return address + jump)     |
| ret         | Return from subroutine                           |

## `mov` — Move (Assignment)

**Purpose:** Copies data from one place to another

(Note: it is **copy**, not swap/exchange)

```asm
mov ax, 5        ; AX = 5
mov bx, ax       ; BX = AX          (BX now also contains 5)
mov [0x7c00], ax ; Write the value of AX to memory address 0x7c00
```

C language equivalent:

```c
a = b;
```

## `xor` — Exclusive OR

**Purpose:** Performs bitwise exclusive OR operation

```asm
xor ax, ax
```

This is **very common** and its real purpose here is: **clear AX to zero**

Why does it work?

Because **any number XOR itself = 0**  
So `ax ^ ax = 0`

Advantages (compared to `mov ax, 0`):

- Modern CPUs treat xor reg, reg as a special zeroing idiom with no dependency on the old register value.
- The instruction is shorter (2 bytes instead of 3)
- Does not require an immediate number

This is **the most common way** to zero out a register in x86 assembly.

## `int` — Software Interrupt

**Purpose:** Calls BIOS or operating system functions

```asm
int 0x10   ; BIOS video / display services
int 0x13   ; BIOS disk read/write services
int 0x16   ; BIOS keyboard services
```

Extremely important in real-mode bootloaders.

Classic example:

```asm
mov ah, 0x0e     ; AH = 0x0E → teletype output function
mov al, 'A'      ; AL = character to print
int 0x10         ; call BIOS video interrupt
```

Meaning: Print the character **A** to the screen (using teletype mode).

Quick notes about this pattern:

- `AH` usually selects **which sub-function** you want
- Other registers (AL, BH, CX, DX…) pass parameters depending on the function
- `int 0x10` is the most commonly used BIOS interrupt in simple boot code

## `rep` — Repeat String Operation

**Purpose:** Repeats the following string instruction (multiple times)

The number of repetitions is controlled by the **CX** register (in 16-bit mode) or **ECX** (in 32-bit mode).

Classic and very common example:

```asm
mov cx, 100       ; we want to repeat 100 times
rep movsb         ; repeat movsb CX times
```

Meaning: Copy 100 bytes from the source (pointed to by DS:SI) to the destination (pointed to by ES:DI), automatically advancing SI and DI each time.

### Most frequently used combinations with `rep`:

```asm
rep movsb    ; copy CX bytes   (byte copy)
rep movsw    ; copy CX words   (word copy, 16-bit)
rep movsd    ; copy CX dwords  (double-word copy, 32-bit)

rep stosb    ; fill CX bytes with value in AL
rep stosw    ; fill CX words with value in AX
rep stosd    ; fill CX dwords with value in EAX

rep lodsb    ; (much less common) read CX bytes into AL one by one
```

On modern CPUs (especially with ERMS support), `rep movsb` is highly optimized and often faster than manual word/dword copying.

### Important notes (real mode / 16-bit code):

- `rep` only works with these string instructions:  
  `movs(b/w/d)`, `stos(b/w/d)`, `lods(b/w/d)`, `scas(b/w/d)`, `cmps(b/w/d)`
- Direction flag (`DF`) controls whether SI/DI are **incremented** (DF=0, `cld`) or **decremented** (DF=1, `std`)
- Almost always use `cld` before `rep movs` / `rep stos` in bootloader/boot sector code

Typical safe pattern in boot code:

```asm
cld                  ; clear direction flag → forward copying
mov cx, 512          ; example: copy 512 bytes (1 sector)
mov si, 0x7c00       ; source
mov di, 0x0600       ; destination
rep movsb            ; fast block copy
```

## `rdtsc` — Read Time-Stamp Counter

**Purpose:** Reads the processor’s internal high-frequency counter (time-stamp counter, TSC)

In a typical 16-bit real-mode bootloader, rdtsc is almost never used. It is mainly found in more modern code or in special detection scenarios.

```asm
rdtsc
```

After execution:

- The **lower 32 bits** of the counter go into **EAX**
- The **upper 32 bits** go into **EDX**
- Together **EDX:EAX** forms a 64-bit value

This 64-bit number represents the approximate number of **clock cycles** elapsed since the processor was **reset / powered on**.

### Common uses:

- High-resolution / low-overhead timing measurements
- Performance benchmarking and micro-benchmarking
- Generating seeds for pseudo-random number generators (especially in older code)
- Detecting VM / emulator timing anomalies (in some anti-debug / anti-VM tricks)

### Important notes:

Available starting from: Pentium (i586) and later (not available on 386 / 486)

- **Not guaranteed to be synchronized** across multiple cores (unless using `rdtscp` + invariant TSC)
- Frequency is **not fixed** across different CPUs (e.g. 2 GHz CPU → ~2 billion ticks per second, but varies with turbo / frequency scaling)
- On modern systems with **invariant TSC** (constant rate, not affected by frequency changes), it becomes much more reliable for timing

### Typical usage pattern (simple timing example):

```asm
rdtsc
mov     ebx, eax     ; save lower 32 bits
mov     ecx, edx     ; save upper 32 bits

; ... code you want to measure ...

rdtsc
sub     eax, ebx
sbb     edx, ecx     ; EDX:EAX = end - start (64-bit subtraction)
```

or (more modern, with `rdtscp` which also serializes):

```asm
rdtscp      ; serializing version (also puts TSC_AUX → ECX)
lfence      ; sometimes added for stronger ordering
```

## `div` — Unsigned Division

**Purpose:** Performs **unsigned** integer division

Important:  
It does **not** use the syntax `div a, b` like most instructions.  
Instead, the **dividend** (the number being divided) is **fixed** — it always comes from specific register(s).

### 8-bit division (divisor is 8-bit)

```asm
mov ax, 10       ; dividend = 10 (in AX)
mov bl, 3        ; divisor = 3
div bl           ; AX ÷ BL
```

After execution:

- **Quotient**  → **AL**
- **Remainder** → **AH**

Example result:

```plaintext
10 ÷ 3 = 3 remainder 1
→ AL = 3
→ AH = 1
```

### 16-bit division (divisor is 16-bit)

Dividend is the 32-bit value in **DX:AX**  
(divisor is a 16-bit register or memory operand)

```asm
mov dx, 0        ; upper 16 bits of dividend
mov ax, 1000     ; lower 16 bits → DX:AX = 1000
mov bx, 7
div bx           ; (DX:AX) ÷ BX
```

After execution:

- **Quotient**  → **AX**
- **Remainder** → **DX**

### 32-bit division (divisor is 32-bit) — 80386+

Dividend is the 64-bit value in **EDX:EAX**

```asm
mov edx, 0
mov eax, 1000000
mov ebx, 400
div ebx          ; (EDX:EAX) ÷ EBX
```

Result:

- Quotient  → **EAX**
- Remainder → **EDX**

### Key warnings / common traps

- **Unsigned only** — use `idiv` for signed division
- If the divisor is 0 → **#DE** (Divide Error exception)
- If the quotient does **not** fit in the destination register (AL/AX/EAX) → also **#DE** (very important in real-mode/bootloader code!)
  - Example: dividing 1000 by 1 when using 8-bit `div bl` → quotient 1000 cannot fit in AL → crash

### Very common pattern in boot/sector code (16-bit):

```asm
xor dx, dx       ; clear DX (important!)
mov ax, 720      ; example value
mov bx, 10
div bx           ; AX = 72, DX = 0   (720 ÷ 10)
```

## `cmp` — Compare

**Purpose:** Performs a **subtraction** but **discards the result** — only updates the flags

```asm
cmp ax, bx
```

This is effectively equivalent to: Internally performs AX - BX and sets flags based on the result.

But:

- It **does not store** the result anywhere
- It **only modifies the CPU flags** (mainly ZF, SF, CF, OF)

`cmp` is almost always used right before a conditional jump.

## `jne` — Jump if Not Equal

(Jump if **Not** Equal / Jump if **Not** Zero)

```asm
cmp ax, bx
jne somewhere
```

Jumps to the label if **AX ≠ BX**  
(i.e. if the result of the subtraction is **not zero** → ZF = 0)

## `je` — Jump if Equal

(Jump if Equal / Jump if Zero)

```asm
cmp ax, bx
je equal_label
```

Jumps if **AX = BX**  
(i.e. if the subtraction result **is zero** → ZF = 1)

## `jz` — Jump if Zero

```asm
jz is_zero
```

Jumps if the **Zero Flag (ZF)** is set (ZF = 1)

**Important equivalence:**

```plaintext
je  ≡  jz     (they are exactly the same instruction — different mnemonic only)
jne ≡  jnz
```

Why?  
Because after `cmp`, equality means the subtraction gave **0**, which sets ZF = 1.

So in real code you will see both styles:

```asm
cmp al, 'A'
je  handle_A           ; most people write je when comparing values

cmp cx, 0
jz  end_of_list        ; many people write jz when checking for zero
```

Both are 100% correct and interchangeable.

## `jmp` — Unconditional Jump

**Purpose:** Jump **always**, no condition checked

```asm
jmp loop_start
```

- Ignores all flags
- Immediately transfers control to the target label/address
- Very common for creating loops, skipping code blocks, or implementing “goto” behavior

### Typical combined pattern (very frequent in assembly):

```asm
check:
    cmp     ax, 100
    je      done           ; if AX == 100 → jump to done
    cmp     ax, 0
    jz      error          ; if AX == 0 → jump to error

    ; ... normal code ...

    jmp     check          ; unconditional loop back

done:
    ; success path

error:
    ; failure path
```

## `cld` — Clear Direction Flag

**Purpose:** Clears the Direction Flag (DF = 0)

When DF = 0:

- String instructions increment SI/DI
- Memory is processed **forward**

```asm
cld
```

After execution:

- DF = 0
- `movsb`, `stosb`, `lodsb` will move **forward**

### Why is this important?

String instructions (`rep movsb`, `rep stosb`, etc.) depend on the Direction Flag.

If DF = 1, they go **backward**.

In bootloader / real-mode code, it is extremely common to do:

```asm
cld
rep movsb
```

### Important note

BIOS does **not guarantee** DF = 0.

So in boot code, it is considered **good practice** to execute:

```asm
cld
```

before any string operations.

## `std` — Set Direction Flag

**Purpose:** Sets the Direction Flag (DF = 1)

```asm
std
```

After execution:

- DF = 1
- String instructions decrement SI/DI
- Memory is processed **backward**

### Example

```asm
std
mov cx, 10
rep movsb
```

This copies 10 bytes **backwards**.

### Typical use case

- Reverse memory copying
- Specialized low-level memory routines

In simple bootloader code, `std` is rarely used.

## `push` — Push Onto Stack

**Purpose:** Push a value onto the stack

In 16-bit real mode:

- Stack grows **downward**
- SP decreases
- Value is written at SS:SP

```asm
push ax
```

Equivalent conceptual behavior:

```plaintext
SP = SP - 2
[SS:SP] = AX
```

### Example

```asm
mov ax, 1234h
push ax
```

Now the value `1234h` is stored on the stack.

### Pushing immediate values

```asm
push 5
```

### Common use cases

- Saving registers
- Passing parameters
- Preparing for `call`

## `pop` — Pop From Stack

**Purpose:** Pop a value from the stack

```asm
pop ax
```

Conceptually:

```plaintext
AX = [SS:SP]
SP = SP + 2
```

### Example

```asm
push ax
push bx

pop bx
pop ax
```

This restores registers in reverse order (LIFO — Last In, First Out).

### Very important rule

The number of `pop`s must match the number of `push`es.

Otherwise:

- Stack corruption
- Return address destroyed
- System crash

## `call` — Call Procedure

**Purpose:** Call a function / subroutine

```asm
call my_function
```

What happens internally (near call, 16-bit real mode):

1. The CPU pushes the return address onto the stack
2. Jumps to the target label

Conceptually:

```plaintext
push IP
IP = target
```

### Example

```asm
call print_char
```

Equivalent C concept:

```c
print_char();
```

### Why `call` is powerful

It allows:

- Code reuse
- Structured programming
- Cleaner bootloader design

## `ret` — Return From Procedure

**Purpose:** Return to the caller

```asm
ret
```

What it does:

1. Pops return address from stack
2. Jumps back to that address

Conceptually:

```plaintext
IP = pop()
```

### Example

```asm
my_function:
    ; do something
    ret
```

### Equivalent C concept:

```c
return;
```

### Typical `call` + `ret` pattern

```asm
main:
    call    hello
    jmp     $

hello:
    mov     ah, 0x0e
    mov     al, 'H'
    int     0x10
    ret
```

Flow:

```plaintext
main → call hello
CPU pushes return address
Jump to hello
hello executes
ret → pop return address
Return to main
```

## Very Common Bootloader Pattern

Saving registers inside a function:

```asm
my_function:
    push    ax
    push    bx

    ; do work

    pop     bx
    pop     ax
    ret
```

Why?

Because assembly has **no automatic register saving**.

You must manually preserve registers if you modify them.
