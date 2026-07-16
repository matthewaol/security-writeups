1. Flag Hunters: 
We're given song lyrics and a reader function which reads and prints the lyrics. We know that the flag exists at the very beginning of the song. 
We can exploit a ";" delimiter to cause the script to start reading at the line we want. 

```
import re
import time


# Read in flag from file
flag = open('flag.txt', 'r').read()

secret_intro = \
'''Pico warriors rising, puzzles laid bare,
Solving each challenge with precision and flair.
With unity and skill, flags we deliver,
The ether’s ours to conquer, '''\
+ flag + '\n'


song_flag_hunters = secret_intro +\
'''

[REFRAIN]
We’re flag hunters in the ether, lighting up the grid,
No puzzle too dark, no challenge too hid.
With every exploit we trigger, every byte we decrypt,
We’re chasing that victory, and we’ll never quit.
CROWD (Singalong here!);
RETURN
# ... More song lyrics

def reader(song, startLabel):
  lip = 0
  start = 0
  refrain = 0
  refrain_return = 0
  finished = False

  # Get list of lyric lines
  song_lines = song.splitlines()
  
  # Find startLabel, refrain and refrain return
  for i in range(0, len(song_lines)):
    if song_lines[i] == startLabel:
      start = i + 1
    elif song_lines[i] == '[REFRAIN]':
      refrain = i + 1
    elif song_lines[i] == 'RETURN':
      refrain_return = i

  # Print lyrics
  line_count = 0
  lip = start
  while not finished and line_count < MAX_LINES:
    line_count += 1
    for line in song_lines[lip].split(';'):
      if line == '' and song_lines[lip] != '':
        continue
      if line == 'REFRAIN':
        song_lines[refrain_return] = 'RETURN ' + str(lip + 1)
        lip = refrain
      elif re.match(r"CROWD.*", line):
        crowd = input('Crowd: ')
        song_lines[lip] = 'Crowd: ' + crowd
        lip += 1
      elif re.match(r"RETURN [0-9]+", line):
        lip = int(line.split()[1])
      elif line == 'END':
        finished = True
      else:
        print(line, flush=True)
        time.sleep(0.5)
        lip += 1



reader(song_flag_hunters, '[REFRAIN]')
[matt ~] $ cat decoderctf.py 
with open("enc", "r", encoding="utf-8") as f:
    data = f.read()

d = []
for c in data: 
    n = ord(c) # converting back into integer 
    # now reverse 
    
    d.append(chr(n >> 8)) 
    d.append(chr(n & 0xFF))

print(''.join(d))      
```

Exploiting this line, we input ";RETURN 0" to trigger this input case, and to start reading at line 0 of the lyrics where the flag is stored. 

Flag: picoCTF{***}

2. Transformation: 
We're given a python script (below) and a unicode file. 
```python
''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```
Given the unicode file, we can reverse the operations performed on it given from the python script. It combines two ASCII characters into one unicode character. Examining the python script, it performs a right shift by 8 on the high bytes, and then nothing on the low bytes. 
To decode, we extract the high byte with left shift by 8 and extract the low byte by bit masking with 0xFF.
We can use this python script to decode and reverse the operations. 
```
with open("enc", "r", encoding="utf-8") as f:
    data = f.read()

d = []
for c in data: 
    n = ord(c) # converting back into integer 
    # now reverse 
    
    d.append(chr(n >> 8)) 
    d.append(chr(n & 0xFF))

print(''.join(d))
``` 

And receive: ***

3. vault-door-training

We're given the following java program: 

```
import java.util.*;

class VaultDoorTraining {
    public static void main(String args[]) {
        VaultDoorTraining vaultDoor = new VaultDoorTraining();
        Scanner scanner = new Scanner(System.in); 
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	   System.out.println("Access granted.");
	} else {
	   System.out.println("Access denied!");
	}
   }

    // The password is below. Is it safe to put the password in the source code?
    // What if somebody stole our source code? Then they would know what our
    // password is. Hmm... I will think of some ways to improve the security
    // on the other doors.
    //
    // -Minion #9567
    public boolean checkPassword(String password) {
        return password.equals("***");
    }
}
```

Inspecting the source code, we can see the password with the `checkPassword` function. Using this as the flag, we complete the challenge. Hooray! 

4. vault-door-1

We're given the following program:
```java
import java.util.*;

class VaultDoor1 {
    public static void main(String args[]) {
        VaultDoor1 vaultDoor = new VaultDoor1();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
	String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	   System.out.println("Access granted.");
	} else {
	   System.out.println("Access denied!");
	}
    }

    // I came up with a more secure way to check the password without putting
    // the password itself in the source code. I think this is going to be
    // UNHACKABLE!! I hope Dr. Evil agrees...
    //
    // -Minion #8728
    public boolean checkPassword(String password) {
        return password.length() == 32 &&
               password.charAt(0)  == 'd' &&
               password.charAt(29) == '4' &&
               password.charAt(4)  == 'r' &&
               password.charAt(2)  == '5' &&
               password.charAt(23) == 'r' &&
               password.charAt(3)  == 'c' &&
               password.charAt(17) == '4' &&
               password.charAt(1)  == '3' &&
               password.charAt(7)  == 'b' &&
               password.charAt(10) == '_' &&
               password.charAt(5)  == '4' &&
               password.charAt(9)  == '3' &&
               password.charAt(11) == 't' &&
               password.charAt(15) == 'c' &&
               password.charAt(8)  == 'l' &&
               password.charAt(12) == 'H' &&
               password.charAt(20) == 'c' &&
               password.charAt(14) == '_' &&
               password.charAt(6)  == 'm' &&
               password.charAt(24) == '5' &&
               password.charAt(18) == 'r' &&
               password.charAt(13) == '3' &&
               password.charAt(19) == '4' &&
               password.charAt(21) == 'T' &&
               password.charAt(16) == 'H' &&
               password.charAt(27) == '0' &&
               password.charAt(30) == 'e' &&
               password.charAt(25) == '_' &&
               password.charAt(22) == '3' &&
               password.charAt(28) == 'e' &&
               password.charAt(26) == 'a' &&
               password.charAt(31) == 'b';
    }
```

The `checkPassword` function contains our desired password, but each character is placed at a jumbled indexes. We can unjumble them with a python script, using the index and characters as key-value pairs: 

```
password = ['_'] * 32

c_at = [ (0, 'd'), 
        (29, '4'),
        (4,'r'), 
        (2,'5'),
        (23,'r'),
        (3,'c'),
        (17,'4'), 
        (1,'3'),
        (7,'b'),
        (10,'_'),
        (5,'4'),
        (9,'3'),
        (11,'t'),
        (15,'c'),
        (8,'l'),
        (12,'H'),
        (20,'c'),
        (14,'_'),
        (6,'m'),
        (24,'5'),
        (18,'r'),
        (13,'3'),
        (19,'4'),
        (21,'T'),
        (16,'H'),
        (27,'0'),
        (30,'e'),
        (25,'_'),
        (22,'3'),
        (28,'e'),
        (26,'a'),
        (31,'b')] 

for i, c in c_at:
    password[i] = c

print("".join(password))
```
This gives us *** which is our flag. 

5. vault-door-3 

We're given the following program: 

```java
import java.util.*;

class VaultDoor3 {
    public static void main(String args[]) {
        VaultDoor3 vaultDoor = new VaultDoor3();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
        }
    }

    // Our security monitoring team has noticed some intrusions on some of the
    // less secure doors. Dr. Evil has asked me specifically to build a stronger
    // vault door to protect his Doomsday plans. I just *know* this door will
    // keep all of those nosy agents out of our business. Mwa ha!
    //
    // -Minion #2671
    public boolean checkPassword(String password) {
        if (password.length() != 32) {
            return false;
        }
        char[] buffer = new char[32];
        int i;
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
        String s = new String(buffer);
        return s.equals("jU5t_a_sna_3lpm11g54e_u_4_m4r042");
    }
}
```

Recognizing the patterns in the loops, we can simply use a python script to reverse the processes of switching around the characters and indices, by re-performing the same operations.

```
buffer = "jU5t_a_sna_3lpm11g54e_u_4_m4r042" 
password = ["?"] * 32  
# first 8 indices are same

i = 0 

while i < 8: 
    password[i] = buffer[i]
    i += 1

while i < 16: 
    password[i] = buffer[23-i]
    i += 1

i = 16
while i < 32: 
    password[i] = buffer[46-i]
    i += 2

i = 31
while i >= 17:
    password[i] = buffer[i] 
    i -= 2 

print("".join(password))
# jU5t_a_s1mpl3_an4gr4m_4_u_e45012
```

Upon reversing the operations, we receive *** which we use as our flag. 

6. vault-door-4

Given the following program:
```java
import java.util.*;

class VaultDoor4 {
    public static void main(String args[]) {
        VaultDoor4 vaultDoor = new VaultDoor4();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	   System.out.println("Access granted.");
	} else {
	   System.out.println("Access denied!");
        }
    }

    // I made myself dizzy converting all of these numbers into different bases,
    // so I just *know* that this vault will be impenetrable. This will make Dr.
    // Evil like me better than all of the other minions--especially Minion
    // #5620--I just know it!
    //
    //  .:::.   .:::.
    // :::::::.:::::::
    // :::::::::::::::
    // ':::::::::::::'
    //   ':::::::::'
    //     ':::::'
    //       ':'
    // -Minion #7781
    public boolean checkPassword(String password) {
        byte[] passBytes = password.getBytes();
        byte[] myBytes = {
            106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  ,
            0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f,
            0142, 0131, 0164, 063 , 0163, 0137, 0145, 060 ,
            '2' , '1' , '3' , '8' , '7' , '2' , '1' , '3' ,
        };
        for (int i=0; i<32; i++) {
            if (passBytes[i] != myBytes[i]) {
                return false;
            }
        }
        return true;
    }
}

```

The `checkPassword` function checks if the given password matches the bytes within the myBytes function. Each element in the byte array is either a byte, integer, octal, or char. 

We can use a python script to convert each of the respective types into chars, and then input that to the program to retrieve the flag. 

```python
password = ['_'] * 32

nums = [106 , 85  , 53  , 116 , 95  , 52  , 95  , 98] 
byte = b'\x55\x6e\x43\x68\x5f\x30\x66\x5f'
octal = [0o142, 0o131, 0o164, 0o63, 0o163, 0o137, 0o145, 0o60]
char = ['2', '1', '3', '8', '7', '2', '1', '3']

p1 = ''.join([chr(i) for i in nums])
p2 = ''.join(byte.decode('ascii')) 
p3 = ''.join([chr(o) for o in octal])
p4 = ''.join(char)


password = p1 + p2 + p3 + p4 
print(password)
```

This feeds us the flag: *** 

**Secure Password Database**:

Problem description: 

I made a new password authentication program that even shows you the password you entered saved in the database! Isn't that cool? 

$ nc candy-mountain.picoctf.net 55520

We're given an executable: `System.out`
```
system.out: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=63224e5a94fa31cb071c82105d8a70ffc806ac0b, for GNU/Linux 3.2.0, not stripped
```

It appears to take in a password, the length of the password in bytes, and spits back out a code. 

For example, giving the password `hello` and inputting a number 5 for # of bytes: 
```
[matt ~] $ ./system.out 
Please set a password for your account:
hello 
How many bytes in length is your password?
5
You entered: 5
Your successfully stored password:
104 101 108 108 111 10 
Enter your hash to access your account!
```

To access my account, we must enter the hash. After messing around with a few values, we see this error message: 

```
[matt ~] $ ./system.out 
Please set a password for your account:
1
How many bytes in length is your password?
1
You entered: 1
Your successfully stored password:
49 10 
Enter your hash to access your account!
"4901"
system.out: heartbleed.c:69: main: Assertion `1 == 0' failed.
```

Referring to the heartbleed vulnerability, we can abuse the length check to try and leak memory. Going down this path, we read leaked bytes but these are not relevant to solving the problem.

Loading up the program in ghidra, we can view the pseudocode of the program: 

```c
    if (local_120 == acStack_b9 + 1) {
      printf("No digits were found");
                    /* WARNING: Subroutine does not return */
      __assert_fail("1 == 0","heartbleed.c",0x45,"main");
    }
    local_f8 = make_secret(local_e5);
    if (local_f8 == local_100) {
      local_f0 = fopen("flag.txt","r");
```

Viewing this code, the program calls a function called `make_secret`, in which we can dig deeper and find a function `hash` which eventually generates the hash the program asks for. 

```
void make_secret(long param_1)

{
  long local_10;
  
  for (local_10 = 0; obf_bytes[local_10] != '\0'; local_10 = local_10 + 1) {
    *(byte *)(local_10 + param_1) = obf_bytes[local_10] ^ 0xaa;
  }
  *(undefined1 *)(param_1 + 0xc) = 0;
  hash(param_1);
  return;
}
```

Using gdb to reveal the hash, we can start the program and break inside `hash`, then `finish` the function and examine the return value: 

```c
Enter your hash to access your account!
1

Breakpoint 1, 0x0000555555555311 in hash ()
(gdb) finish
Run till exit from #0  0x0000555555555311 in hash ()
0x00005555555553ce in make_secret ()
(gdb) info reg
rax            0xd3770d6251b31be2  -3209081493549540382

```

Inputting `-3209081493549540382` as the hash into the program, we then receive the flag back as `picoCTF{***}`. 
