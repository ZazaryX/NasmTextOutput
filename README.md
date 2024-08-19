# NASMTextOutputTutorial

Tutorial on how to write a NASM program to output text to the console for Linux and Windows.

## Introduction

In this tutorial, we'll focus on writing a NASM (Netwide Assembler) program that prints text to the console. This guide will cover both Linux and Windows environments, but won't delve into the installation details of NASM or other tools.

**Disclaimer:** I am not an expert in NASM, but I decided to share my knowledge and experience to help others understand how to write NASM programs for outputting text to the console. I hope this guide proves useful and makes it easier for others to grasp the basics of working with NASM.

## Writing the Program

We'll create a simple NASM program that outputs any text to the console.

### Windows
```asm
global _start

extern WriteFile
extern GetStdHandle
extern ExitProcess

section .data
msg: db "Hello Github!",10
msglen: equ $-msg

section .text
_start:
sub rsp,40
mov rax,-11
call GetStdHandle
mov rcx,rax
mov rdx,msg
mov r8d,msglen
lea r9,[rsp+32]
mov qword [rsp+32],0
call WriteFile

add rsp,40
xor rcx,rcx
call ExitProcess
```
## Analysis of the code Windows
```asm
global _start ; Declare the _start label as the entry point of the program
```
```asm
extern WriteFile ; External function to write to the file (console in this case)
extern GetStdHandle ; External function to get a handle to a specified device
extern ExitProcess ; External function to exit the process
; Declare external functions from Windows API
```
```asm
section .data ; Data section for storing variables
msg: db "Hello Github!", 10 ; Define a string with a newline character
msglen: equ $ - msg ; Compute the length of the string
```
```asm
section .text ; Code section
_start: ; Entry point of the program
```
```asm
sub rsp, 40 ; Allocate stack space for local variables and alignment
mov rax, -11 ; Load the constant value -11 into rax (handle for STD_OUTPUT_HANDLE)
call GetStdHandle ; Call GetStdHandle to get the handle for standard output
; rax now contains the handle for standard output
```
```asm
mov rcx, rax ; Pass the handle to WriteFile (first parameter)
; rcx now holds the file handle (standard output)
mov rdx, msg ; Pass the address of the message to WriteFile (second parameter)
; rdx now points to the message buffer 
mov r8d, msglen ; Pass the length of the message to WriteFile (third parameter)
; r8d contains the length of the message
lea r9, [rsp + 32] ; Load the address of the stack space for OVERLAPPED structure into r9
; r9 now points to the location on the stack where OVERLAPPED will be stored 
mov qword [rsp + 32], 0 ; Initialize the OVERLAPPED structure to zero
; [rsp + 32] is the address for OVERLAPPED structure; setting it to zero
; ensures synchronous operation for WriteFile 
call WriteFile ; Call WriteFile to output the message to the console
; WriteFile uses the parameters in rcx, rdx, r8d, and r9 to perform the write operation
```
```asm
add rsp, 40 ; Clean up stack space after the operation
xor rcx, rcx ; Set the exit code to 0 (success)
call ExitProcess ; Exit the program
; The program terminates with the exit code 0
```
### Linux
```asm
global _start

section .data
msg: "Hello Github!",10
msglen: equ $-msg

section .text
_start:
mov rax,1
mov rdi,1
mov rsi,msg
mov rdx,msglen
syscall
mov rax,60
xor rdi,rdi
syscall
```
## Analysis of the code Linux
```asm
global _start ; Declare _start as the entry point of the program
```
```asm
section .data ; Data section for storing variables
msg: db "Hello Github!", 10 ; Define a string with a newline character
msglen: equ $ - msg ; Compute the length of the string
```
```asm
mov rax, 1 ; Load sys_write system call number into rax
mov rdi, 1 ; Load file descriptor for stdout into rdi
mov rsi, msg ; Load address of the message into rsi
mov rdx, msglen ; Load length of the message into rdx
syscall ; Invoke the sys_write system call
```
```asm
mov rax, 60 ; Load sys_exit system call number into rax
xor rdi, rdi ; Set exit code to 0
syscall ; Invoke the sys_exit system call
```
## References
For ASCII codes and text encoding details, see [ASCII Code Table](https://www.ascii-code.com/).
For Linux system calls and their numbers, refer to [Linux Syscall Table](https://filippo.io/linux-syscall-table/).