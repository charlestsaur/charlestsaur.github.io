# Mini_Assembly_Snake_Game

No separate ASM file was used.

The following is the source code:

```asm
[org 0x7c00]
[bits 16]

start:
    xor ax, ax
    mov ds, ax
    mov ax, 0x0013
    int 0x10
    mov ax, 0xa000
    mov es, ax

init_game:
    xor di, di
    mov cx, 32000
    xor eax, eax
    rep stosw

    mov word [0x8000], 32160
    mov si, 0x8000
    mov bx, 0x8000
    mov bp, 1

spawn_apple:
    rdtsc
    xor dx, dx
    mov cx, 64000
    div cx
    mov di, dx
    cmp byte [es:di], 0
    jne spawn_apple
    mov byte [es:di], 40

game_loop:
    mov ah, 0x86
    mov cx, 0
    mov dx, 0x4000
    int 0x15

    mov ah, 0x01
    int 0x16
    jz update_pos

    xor ah, ah
    int 0x16

    cmp al, 'w'
    je move_up
    cmp al, 's'
    je move_down
    cmp al, 'a'
    je move_left
    cmp al, 'd'
    je move_right
    jmp update_pos

move_up:
    mov bp, -320
    jmp update_pos
move_down:
    mov bp, 320
    jmp update_pos
move_left:
    mov bp, -1
    jmp update_pos
move_right:
    mov bp, 1

update_pos:
    mov ax, [si]
    add ax, bp
    mov di, ax

    cmp ax, 64000
    jae game_over

    cmp byte [es:di], 40
    je eat_apple
    cmp byte [es:di], 0
    jne game_over

    mov byte [es:di], 2
    add si, 2
    mov [si], di

    mov di, [bx]
    mov byte [es:di], 0
    add bx, 2
    jmp game_loop

eat_apple:
    mov byte [es:di], 2
    add si, 2
    mov [si], di
    jmp spawn_apple

game_over:
    jmp start

times 510-($-$$) db 0
dw 0xaa55
```
