**fd**

Inspecting the code, we can see the integer `fd` is being modified by `atoi(argv[1] - 0x1234`. Our first argument will be an integer for `argv[1]`, and our second argument will go into the buffer `buf`, using `fd`.
```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
	if(argc<2){
		printf("pass argv[1] a number\n");
		return 0;
	}
	int fd = atoi( argv[1] ) - 0x1234;
	int len = 0;
	len = read(fd, buf, 32);
	if(!strcmp("LETMEWIN\n", buf)){
		printf("good job :)\n");
		setregid(getegid(), getegid());
		system("/bin/cat flag");
		exit(0);
	}
	printf("learn about Linux file IO\n");
	return 0;

}
```

Knowing that `fd = 0` will get input from the program's stdin, we can aim for this in order to fill `buf` with `LETMEWIN`, which will give us the flag.

To achieve `fd = 0`, we can input `4660` for argv[1] which is the decimal version of `0x1234`; executing `0x1234 - 0x1234 = 0`. Then, we'll input `LETMEWIN` for `argv[2]` to pass the`!strcmp("LETMEWIN\n", buf)`. 

Finally, we receive the flag: ***

**collision**
The problem hints at a hash collision (md5). 

Given a program with source code: 
```c
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
        int* ip = (int*)p;
        int i;
        int res=0;
        for(i=0; i<5; i++){
                res += ip[i];
        }
        return res;
}

int main(int argc, char* argv[]){
        if(argc<2){
                printf("usage : %s [passcode]\n", argv[0]);
                return 0;
        }
        if(strlen(argv[1]) != 20){
                printf("passcode length should be 20 bytes\n");
                return 0;
        }

        if(hashcode == check_password( argv[1] )){
                setregid(getegid(), getegid());
                system("/bin/cat flag");
                return 0;
        }
        else
                printf("wrong passcode.\n");
        return 0;
}
```

Examining the source, it looks like we must import a password that must be 20 bytes long and pass `check_password`. In `check_password`, we must submit 20 bytes that when summed up, will result in the `0x21DD09EC`. 

Let's crank out the math for the payload: 

```
>>> 568134124 // 5
113626824

>>> 568134124 - 568134120
...
4
>>> 113626824 + 4
113626828
>>> hex(113626828)
'0x6c5cecc' <- need 4 of these 
>>> hex(113626824)
'0x6c5cec8' <- need one of these
```

Since it's little endian, we'll craft the payload as: `"\xc8\xce\xc5\x06" * 4 + b"\xcc\xce\xc5\x06"`

Finally, let's send it to the binary: 

`./col "$(python3 -c 'import sys; sys.stdout.buffer.write(b"\xc8\xce\xc5\x06" * 4 + b"\xcc\xce\xc5\x06")')"

And receive ***
`