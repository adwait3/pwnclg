# pwn college

# `level-1`

```asm
.intel_syntax noprefix
mov rdi, 0x1337
```

## flag > ``


```
note - 

I looked up why exactly `.intel_syntax noprefix` worked. \
it was because the thing was probably using at&t syntax which is poo. \
I gotta specify what syntax to let the assembly use in the beginning.
```
<br><br><br>

***
<br><br><br>

# `level-2`


### Challenge :
```
Welcome to ASMLevel2
==================================================

To interact with any level you will send raw bytes over stdin to this program.
To efficiently solve these problems, first run it to see the challenge instructions.
Then craft, assemble, and pipe your bytes to this program.

For instance, if you write your assembly code in the file asm.S, you can assemble that to an object file:
  as -o asm.o asm.S

Note that if you want to use Intel syntax for x86 (which, of course, you do), you'll need to add the following to the start of asm.S:
  .intel_syntax noprefix

Then, you can copy the .text section (your code) to the file asm.bin:
  objcopy -O binary --only-section=.text asm.o asm.bin

And finally, send that to the challenge:
  cat ./asm.bin | /challenge/run

You can even run this as one command:
  as -o asm.o asm.S && objcopy -O binary --only-section=.text ./asm.o ./asm.bin && cat ./asm.bin | /challenge/run

In this level you will be working with registers. You will be asked to modify
or read from registers.



In this level you will work with multiple registers. Please set the following:
  rax = 0x1337
  r12 = 0xCAFED00D1337BEEF
  rsp = 0x31337
```

#### Solution :

I found insteresting shtuff : https://github.com/sidmittal32/pwn.college/tree/main/Assembly%20Crash%20Course

a wild forensics head spotted. \
I liked his approach of doing it via python scripts. \
I find python more comfier than assmebly. \
finna endorse this now. 

here's what I cooked :
```python
import pwn
pwn.context.update(arch="amd64")

code = pwn.asm("""
mov rax, 0x1337
mov r12, 0xCAFED00D1337BEEF
mov rsp, 0x31337
""")

process = pwn.process("/challenge/run")
process.write(code)

print(process.readall())
```

works like a charm : \
![image](https://github.com/IC3lemon/pwncollege/assets/150153966/060661d3-bdee-4cc0-8e70-e669e4fd69b0)


## flag > `pwn.college{AHW04pE0RuTX-KXY5gDYFvTtUWZ.dBTM4MDL4UjM3QzW}`
<br><br><br>

***
<br><br><br>

# `level-3`


### Challenge :
```
Welcome to ASMLevel3
==================================================

To interact with any level you will send raw bytes over stdin to this program.
To efficiently solve these problems, first run it to see the challenge instructions.
Then craft, assemble, and pipe your bytes to this program.

For instance, if you write your assembly code in the file asm.S, you can assemble that to an object file:
  as -o asm.o asm.S

Note that if you want to use Intel syntax for x86 (which, of course, you do), you'll need to add the following to the start of asm.S:
  .intel_syntax noprefix

Then, you can copy the .text section (your code) to the file asm.bin:
  objcopy -O binary --only-section=.text asm.o asm.bin

And finally, send that to the challenge:
  cat ./asm.bin | /challenge/run

You can even run this as one command:
  as -o asm.o asm.S && objcopy -O binary --only-section=.text ./asm.o ./asm.bin && cat ./asm.bin | /challenge/run

In this level you will be working with registers. You will be asked to modify
or read from registers.

We will now set some values in memory dynamically before each run. On each run
the values will change. This means you will need to do some type of formulaic
operation with registers. We will tell you which registers are set beforehand
and where you should put the result. In most cases, its rax.



Many instructions exist in x86 that allow you to do all the normal
math operations on registers and memory.

For shorthand, when we say A += B, it really means A = A + B.

Here are some useful instructions:
  add reg1, reg2       <=>     reg1 += reg2
  sub reg1, reg2       <=>     reg1 -= reg2
  imul reg1, reg2      <=>     reg1 *= reg2

div is more complicated and we will discuss it later.
Note: all 'regX' can be replaced by a constant or memory location

Do the following:
  add 0x331337 to rdi

We will now set the following in preparation for your code:
  rdi = 0x5ff
```

#### Solution :

pretty straight forward, add 0x331337 to rdi, put the result in rax. \
```python
import pwn
pwn.context.update(arch="amd64")

code = pwn.asm("""
add rdi, 0x331337
mov rax, rdi""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```
yay

![image](https://github.com/IC3lemon/pwncollege/assets/150153966/7b0e82ac-270a-495a-bdbe-221f842b5a7b)


## flag > `pwn.college{QgVVrFKwLV3rPYhaof2VsK0vMA_.0VN5EDL4UjM3QzW}`
<br><br><br>

***
<br><br><br>

# `level-4`


### Challenge :

```
Welcome to ASMLevel4
==================================================

To interact with any level you will send raw bytes over stdin to this program.
To efficiently solve these problems, first run it to see the challenge instructions.
Then craft, assemble, and pipe your bytes to this program.

For instance, if you write your assembly code in the file asm.S, you can assemble that to an object file:
  as -o asm.o asm.S

Note that if you want to use Intel syntax for x86 (which, of course, you do), you'll need to add the following to the start of asm.S:
  .intel_syntax noprefix

Then, you can copy the .text section (your code) to the file asm.bin:
  objcopy -O binary --only-section=.text asm.o asm.bin

And finally, send that to the challenge:
  cat ./asm.bin | /challenge/run

You can even run this as one command:
  as -o asm.o asm.S && objcopy -O binary --only-section=.text ./asm.o ./asm.bin && cat ./asm.bin | /challenge/run

In this level you will be working with registers. You will be asked to modify
or read from registers.

We will now set some values in memory dynamically before each run. On each run
the values will change. This means you will need to do some type of formulaic
operation with registers. We will tell you which registers are set beforehand
and where you should put the result. In most cases, its rax.



Using your new knowledge, please compute the following:
  f(x) = mx + b, where:
    m = rdi
    x = rsi
    b = rdx

Place the result into rax.

Note: there is an important difference between mul (unsigned
multiply) and imul (signed multiply) in terms of which
registers are used. Look at the documentation on these
instructions to see the difference.

In this case, you will want to use imul.

We will now set the following in preparation for your code:
  rdi = 0x22e7
  rsi = 0xd8
  rdx = 0x2578
```

### Solution :

Pretty straightforward yet again, mx + b \
m = rdi \
x = rsi \
b = rdx
and put the result in rax


```python
import pwn
pwn.context.update(arch="amd64")

code = pwn.asm("""
imul rdi, rsi
add rdx, rdi
mov rax, rdx
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{MqxIl8upDgiCNTs0T2_nhrrFZRx.0lN5EDL4UjM3QzW}`

<br><br><br>
***
<br><br><br>

# `level-5`


### Challenge :

```
Welcome to ASMLevel5
==================================================

We will now set some values in memory dynamically before each run. On each run
the values will change. This means you will need to do some type of formulaic
operation with registers. We will tell you which registers are set beforehand
and where you should put the result. In most cases, its rax.

Division in x86 is more special than in normal math. Math in here is
called integer math. This means every value is a whole number.

As an example: 10 / 3 = 3 in integer math.

Why?

Because 3.33 is rounded down to an integer.

The relevant instructions for this level are:
  mov rax, reg1; div reg2

Note: div is a special instruction that can divide
a 128-bit dividend by a 64-bit divisor, while
storing both the quotient and the remainder, using only one register as an operand.

How does this complex div instruction work and operate on a
128-bit dividend (which is twice as large as a register)?

For the instruction: div reg, the following happens:
  rax = rdx:rax / reg
  rdx = remainder

rdx:rax means that rdx will be the upper 64-bits of
the 128-bit dividend and rax will be the lower 64-bits of the
128-bit dividend.

You must be careful about what is in rdx and rax before you call div.

Please compute the following:
  speed = distance / time, where:
    distance = rdi
    time = rsi
    speed = rax

Note that distance will be at most a 64-bit value, so rdx should be 0 when dividing.

We will now set the following in preparation for your code:
  rdi = 0x47c
  rsi = 0x5f

```

New stuff. main point of interest was this 
```asm
For the instruction: div reg, the following happens:
  rax = rdx:rax / reg
  rdx = remainder
```
```
rdx:rax means that rdx will be the upper 64-bits of
the 128-bit dividend and rax will be the lower 64-bits of the
128-bit dividend.
```

so basically, `div reg` implies `rax = rax/reg` & `rdx = rax%reg`\
and since we gotta perform `rdi/rsi` \
we gotta set rax = rdi \
so then `div rsi` should perform `rax = rdi/rsi` \
and yes 

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, rdi
div rsi
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{Esre897YlX911GCMucki5ulDfqU.01N5EDL4UjM3QzW}`

<br><br><br>
***
<br><br><br>

# `level-6`


### Challenge :

```
Modulo in assembly is another interesting concept!

x86 allows you to get the remainder after a div operation.

For instance: 10 / 3 -> remainder = 1

The remainder is the same as modulo, which is also called the "mod" operator.

In most programming languages we refer to mod with the symbol '%'.

Please compute the following:
  rdi % rsi

Place the value in rax.

We will now set the following in preparation for your code:
  rdi = 0x12532de8
  rsi = 0x7
```


### Solution :

like we saw in the previous chal \
`div reg` implies `rax = rax/reg` & `rdx = rax%reg` \
So we gotta div rax = rdi with rsi and then call rdx, where the remainder of this operation is stored.

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, rdi
div rsi
mov rax, rdx
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

ez

## flag > `pwn.college{IVOCMljYxvZ4u891kIdzE8_WotI.0FO5EDL4UjM3QzW}`

<br><br><br>
***
<br><br><br>

# `level-7`


### Challenge :

```
Another cool concept in x86 is the ability to independently access to lower register bytes.

Each register in x86_64 is 64 bits in size, and in the previous levels we have accessed
the full register using rax, rdi or rsi.

We can also access the lower bytes of each register using different register names.

For example the lower 32 bits of rax can be accessed using eax, the lower 16 bits using ax,
the lower 8 bits using al.

MSB                                    LSB
+----------------------------------------+
|                   rax                  |
+--------------------+-------------------+
                     |        eax        |
                     +---------+---------+
                               |   ax    |
                               +----+----+
                               | ah | al |
                               +----+----+

Lower register bytes access is applicable to almost all registers.

Using only one move instruction, please set the upper 8 bits of the ax register to 0x42.

We will now set the following in preparation for your code:
  rax = 0xd4130547870400e0
```

### Solution :

```
MSB                                    LSB
+----------------------------------------+
|                   rax                  |
+--------------------+-------------------+
                     |        eax        |
                     +---------+---------+
                               |   ax    |
                               +----+----+
                               | ah | al |
                               +----+----+
```

from this chart, upper 8 bits of `ax` should mean `ah`, right ?
so, `mov ah, thevalue` ?

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov ah, 0x42
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

Yessir

## flag > `pwn.college{Eo0JqICCRvIeQzGK17K9qnyUO79.dFTM4MDL4UjM3QzW}`

<br><br><br>
***
<br><br><br>

# `level-8`


### Challenge :

```
It turns out that using the div operator to compute the modulo operation is slow!

We can use a math trick to optimize the modulo operator (%). Compilers use this trick a lot.

If we have "x % y", and y is a power of 2, such as 2^n, the result will be the lower n bits of x.

Therefore, we can use the lower register byte access to efficiently implement modulo!

Using only the following instruction(s):
  mov

Please compute the following:
  rax = rdi % 256       256 = 2**8
  rbx = rsi % 65536      65536 = 2**16

We will now set the following in preparation for your code:
  rdi = 0x238c
  rsi = 0x2a4b22cb
```
### Solution :

`If we have "x % y", and y is a power of 2, such as 2^n, the result will be the lower n bits of x.` \
so for rdi % 256, 256 = 2**8,  I gotta access lower 8 bits of rdi (`dil`) and put that in rax.
similarly, for rsi % 65536, 65536 = 2**16, I gotta access lower 16 bits of rsi (`si`) and put that in rbx

found this when looking up the registers and their structures
![image](https://github.com/IC3lemon/pwncollege/assets/150153966/02f45c0c-b37c-40de-ab85-cdf4cb9d44b4)

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, dil
mov rbx, si
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

but this wasn't working \
![image](https://github.com/IC3lemon/pwncollege/assets/150153966/833955bb-d442-48b1-8807-c508ab16a3d2) \
 **???**

 i looked it up and found this \
 ![image](https://github.com/IC3lemon/pwncollege/assets/150153966/dde8ec87-205f-43e6-b344-5f5a5a70e8aa) 

 So i think the mistake im making is that im tryna mov dil, which is a 8 bit reg, into rax, a 64 bit reg. \
 but they're asking to move the modulo result into rax wtf. 


 I then thought, maybe I ought to put the modulo result i.e. the 8 bit rdi reg into the 8 bit reg of rax \
 i.e. mov dil into al which implies moving the lower 8 bits of rdi into lower 8 of rax, maybe then it works. \

 aight so new code :
```python
code = pwn.asm("""
mov al, dil
mov bx, si
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

lesgoooo

## flag > `pwn.college{8BGmIY3wLY7nenKEROfs5S-uP8z.0VO5EDL4UjM3QzW}`

<br><br><br>
***
<br><br><br>

# `level-9`


### Challenge :

```
In this level you will be working with bit logic and operations. This will involve heavy use of
directly interacting with bits stored in a register or memory location. You will also likely
need to make use of the logic instructions in x86: and, or, not, xor.

Shifting bits around in assembly is another interesting concept!

x86 allows you to 'shift' bits around in a register.

Take, for instance, al, the lowest 8 bits of rax.

The value in al (in bits) is:
  rax = 10001010

If we shift once to the left using the shl instruction:
  shl al, 1

The new value is:
  al = 00010100

Everything shifted to the left and the highest bit fell off
while a new 0 was added to the right side.

You can use this to do special things to the bits you care about.

Shifting has the nice side affect of doing quick multiplication (by 2)
or division (by 2), and can also be used to compute modulo.

Here are the important instructions:
  shl reg1, reg2       <=>     Shift reg1 left by the amount in reg2
  shr reg1, reg2       <=>     Shift reg1 right by the amount in reg2
  Note: 'reg2' can be replaced by a constant or memory location

Using only the following instructions:
  mov, shr, shl

Please perform the following:
  Set rax to the 5th least significant byte of rdi.

For example:
  rdi = | B7 | B6 | B5 | B4 | B3 | B2 | B1 | B0 |
  Set rax to the value of B4

We will now set the following in preparation for your code:
  rdi = 0x5968c45ce82f8a3a
```

### Solution :

so set rax to B4, i.e. 5th value in rdi stack
so the 4th BYTE in rdi is B4
i amma shift B4 to the top of rdi, `shl rdi, 8*3`
and then put it into rax

but that didn't work
![image](https://github.com/IC3lemon/pwncollege/assets/150153966/7a5168d9-ef97-4ee5-909a-cd5756756dce)

![image](https://github.com/IC3lemon/pwncollege/assets/150153966/cd784140-2699-48c8-b445-6405401a74ca)

im guessing this because the trailing bytes after c4 shouldn't be there, the thing wants rax to be only 0xc4 
Ig i can shift rax to the rightmost by 7 bytes so that 0x00000000000000c4 i.e. 0xc4remains
so `shr rax, 8*7`

Aye, lesgo

```python
code = pwn.asm("""
shl rdi, 8*3
mov rax, rdi
shr rax, 8*7
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{MwZ0Ok68FJVrvXg-PMeh0PgpJ9l.0FMwIDL4UjM3QzW}`


<br><br><br>
***
<br><br><br>

# `level-10`


### Challenge :

```

Bitwise logic in assembly is yet another interesting concept!
x86 allows you to perform logic operations bit by bit on registers.

For the sake of this example say registers only store 8 bits.

The values in rax and rbx are:
  rax = 10101010
  rbx = 00110011

If we were to perform a bitwise AND of rax and rbx using the
"and rax, rbx" instruction, the result would be calculated by
ANDing each bit pair 1 by 1 hence why it's called a bitwise
logic.

So from left to right:
  1 AND 0 = 0
  0 AND 0 = 0
  1 AND 1 = 1
  0 AND 1 = 0
  ...

Finally we combine the results together to get:
  rax = 00100010

Here are some truth tables for reference:
      AND          OR           XOR
   A | B | X    A | B | X    A | B | X
  ---+---+---  ---+---+---  ---+---+---
   0 | 0 | 0    0 | 0 | 0    0 | 0 | 0
   0 | 1 | 0    0 | 1 | 1    0 | 1 | 1
   1 | 0 | 0    1 | 0 | 1    1 | 0 | 1
   1 | 1 | 1    1 | 1 | 1    1 | 1 | 0

Without using the following instructions:
  mov, xchg

Please perform the following:
  rax = rdi AND rsi

i.e. Set rax to the value of (rdi AND rsi)

We will now set the following in preparation for your code:
  rdi = 0x58a911704b90e090
  rsi = 0x7e2151fe15d0eadc
```

### Solution :

Went straight ahead with `and rdi, rsi` \
`mov rdi, rax`

but noo
mov not allowed \
![image](https://github.com/IC3lemon/pwncollege/assets/150153966/3bd6d084-146c-4a93-95ce-47c23a070e62) \
how tf do I do this without mov. 


I hit `and rdi,rsi` to check what was in rax \
![image](https://github.com/IC3lemon/pwncollege/assets/150153966/0538dbf3-347b-429d-8d7b-a9b6692c4a42)

so rax = 0Xfffffffffffffff rn \
i.e rax = 1111111111111111111111111111111111111111111111111111111111111111 (in binary)

ok so let A =1 Then, \
(A.B).C = B.C \
so `and rax, rsi` \
`and rax, rdi` \
should do the trick

by this logic, I cooked this 
```python
import pwn
code = pwn.asm("""
and rax, rsi
and rax, rdi
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{w8rKKXCMw-ioMbybzXbW6JcJhM3.0VMwIDL4UjM3QzW}`


<br><br><br>
***
<br><br><br>

# `level-11`


### Challenge :
```
Using only the following instructions:
  and, or, xor

Implement the following logic:
  if x is even then
    y = 1
  else
    y = 0

where:
  x = rdi
  y = rax

We will now set the following in preparation for your code:
  rdi = 0x3255ac06
```

### Solution :

not gonna lie, was insanely clueless on this one for a while.
not allowed to use mov, cmp, div

I looked up `how to check if a number is even or odd with bitwise operations.` \
and landed here : https://www.geeksforgeeks.org/check-if-a-number-is-odd-or-even-using-bitwise-operators \
not that helpful, just helped recall that the last bit of an even number is always 0, and the last in an odd number is always 1.

thinking this up took quite a while : \
1. set rax to 0000000000000000000000000000000000000000000000000000000000000000
   i figured `and rax, 0` should do it
   
2. and rdi with 1 \
   what that does :
   if rdi is even, the last bit would be 0 eg : 000111111000000001100001100000000000000111100011100010100111110
   then, after `and rdi, 1` : rdi = 0000000000000000000000000000000000000000000000000000000000000000
   similarly if rdi is odd, last bit would be 1 : 000111111000000001100001100000000000000111100011100010100111110
   then, after `and rdi, 1` : rdi = 0000000000000000000000000000000000000000000000000000000000000001

3. now the chall wants 1 if even;  0 if odd \
   but we got the opposite rn. to fix that, xor with 1

4. put the result in rax (which was set to 0)
   `or rax, rdi`

So the final solution i cooked :
```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
and rax, 0
and rdi, 1
xor rdi, 1
or rax, rdi
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

this shi was not easy. \
experienced insane amounts of skill issue.


got it tho 

## flag > `pwn.college{sCbmNSNCbrAXqk6Jr0npT5Xg41e.0lMwIDL4UjM3QzW}`

<br><br><br>

***
<br><br><br>

# `level-12`


### Challenge :

```
In this level you will be working with memory. This will require you to read or write
to things stored linearly in memory. If you are confused, go look at the linear
addressing module in 'ike. You may also be asked to dereference things, possibly multiple
times, to things we dynamically put in memory for your use.



Up until now you have worked with registers as the only way for storing things, essentially
variables such as 'x' in math.

However, we can also store bytes into memory!

Recall that memory can be addressed, and each address contains something at that location.

Note that this is similar to addresses in real life!

As an example: the real address '699 S Mill Ave, Tempe, AZ
85281' maps to the 'ASU Brickyard'.

We would also say it points to 'ASU Brickyard'.

We can represent this like:
  ['699 S Mill Ave, Tempe, AZ 85281'] = 'ASU Brickyard'

The address is special because it is unique.

But that also does not mean other addresses can't point to the same thing (as someone can have multiple houses).

Memory is exactly the same!

For instance, the address in memory that your code is stored (when we take it from you) is 0x400000.

In x86 we can access the thing at a memory location, called dereferencing, like so:
  mov rax, [some_address]        <=>     Moves the thing at 'some_address' into rax

This also works with things in registers:
  mov rax, [rdi]         <=>     Moves the thing stored at the address of what rdi holds to rax

This works the same for writing to memory:
  mov [rax], rdi         <=>     Moves rdi to the address of what rax holds.

So if rax was 0xdeadbeef, then rdi would get stored at the address 0xdeadbeef:
  [0xdeadbeef] = rdi

Note: memory is linear, and in x86_64, it goes from 0 - 0xffffffffffffffff (yes, huge).

Please perform the following:
  Place the value stored at 0x404000 into rax

Make sure the value in rax is the original value stored at 0x404000.

We will now set the following in preparation for your code:
  [0x404000] = 0x18f019
```

### Solution :

pretty direct, `mov rax, [some_address]        <=>     Moves the thing at 'some_address' into rax` \
ez pz 


```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, [0x404000]
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{E7q_xloSrB83fYmCS-elxqGhudH.dJTM4MDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-13`


### Challenge :

```
Please perform the following:
  Place the value stored in rax to 0x404000

We will now set the following in preparation for your code:
  rax = 0x1b540d
```

### Solution :

ogay its starting off too easy, something tough coming up later im sure

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov [0x404000], rax
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
""")
```

## flag > `pwn.college{wB8zWth9I8iDyL809383ald8PtP.dNTM4MDL4UjM3QzW}`

<br><br><br>

***

<br><br><br>

# `level-14`


### Challenge : 

```

Please perform the following:
  Place the value stored at 0x404000 into rax
  Increment the value stored at the address 0x404000 by 0x1337

Make sure the value in rax is the original value stored at 0x404000 and make sure
that [0x404000] now has the incremented value.

We will now set the following in preparation for your code:
  [0x404000] = 0x13c17a

```

### Solution :

I went straight ahead with 

```
mov rax, [0x404000]
add [0x404000], 0x1337
```

but it did not work, \
`Error: ambiguous operand size for 'add'` \
I looked it up and found out that you gotta specify how big of a data type ur address pointing at \
it might be a word, a dword or a qword. \
So I just gotta write dword ptr infront and aye : 

```python
code = pwn.asm("""
mov rax, [0x404000]
add dword ptr [0x404000], 0x1337
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{0UKLItBWOSZJWmGj4tWI9Llqp_o.01MwIDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-15`

### Challenge : 

```

Recall that registers in x86_64 are 64 bits wide, meaning they can store 64 bits.

Similarly, each memory location can be treated as a 64 bit value.

We refer to something that is 64 bits (8 bytes) as a quad word.

Here is the breakdown of the names of memory sizes:
  Quad Word   = 8 Bytes = 64 bits
  Double Word = 4 bytes = 32 bits
  Word        = 2 bytes = 16 bits
  Byte        = 1 byte  = 8 bits

In x86_64, you can access each of these sizes when dereferencing an address, just like using
bigger or smaller register accesses:
  mov al, [address]        <=>        moves the least significant byte from address to rax
  mov ax, [address]        <=>        moves the least significant word from address to rax
  mov eax, [address]       <=>        moves the least significant double word from address to rax
  mov rax, [address]       <=>        moves the full quad word from address to rax

Remember that moving into al does not fully clear the upper bytes.

Please perform the following:
  Set rax to the byte at 0x404000

We will now set the following in preparation for your code:
  [0x404000] = 0x138608
```

### Solution : 

` mov al, [address]        <=>        moves the least significant byte from address to rax` \
exactly what they asking\
and it workin

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov al, [0x404000]
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{4tIIXjsNDsyRD5QAAMNAJa45lPY.dRTM4MDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-16`

### Challenge : 

```
Please perform the following:
  Set rax to the byte at 0x404000
  Set rbx to the word at 0x404000
  Set rcx to the double word at 0x404000
  Set rdx to the quad word at 0x404000

We will now set the following in preparation for your code:
  [0x404000] = 0x8db95af7cb468c3f

```

### Solution :

Gonna use this again \
![image](https://github.com/IC3lemon/pwncollege/assets/150153966/7cb3cd50-b032-4d40-8978-84f60eda6272)

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov al, [0x404000]
mov bx, [0x404000]
mov ecx, [0x404000]
mov rdx, [0x404000]
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{Qgi8x7DvVUtNZgEgExgcCCgVO5G.0FNwIDL4UjM3QzW}`

<br><br><br>

***

<br><br><br>

# `level-17`

### Challenge : 
```

In this level you will be working with memory. This will require you to read or write
to things stored linearly in memory. If you are confused, go look at the linear
addressing module in 'ike. You may also be asked to dereference things, possibly multiple
times, to things we dynamically put in memory for your use.



It is worth noting, as you may have noticed, that values are stored in reverse order of how we
represent them.

As an example, say:
  [0x1330] = 0x00000000deadc0de

If you examined how it actually looked in memory, you would see:
  [0x1330] = 0xde
  [0x1331] = 0xc0
  [0x1332] = 0xad
  [0x1333] = 0xde
  [0x1334] = 0x00
  [0x1335] = 0x00
  [0x1336] = 0x00
  [0x1337] = 0x00

This format of storing things in 'reverse' is intentional in x86, and its called "Little Endian".

For this challenge we will give you two addresses created dynamically each run.

The first address will be placed in rdi.
The second will be placed in rsi.

Using the earlier mentioned info, perform the following:
  Set [rdi] = 0xdeadbeef00001337
  Set [rsi] = 0xc0ffee0000

Hint: it may require some tricks to assign a big constant to a dereferenced register.
Try setting a register to the constant value then assigning that register to the dereferenced register.

We will now set the following in preparation for your code:
  [0x404040] = 0xffffffffffffffff
  [0x404b98] = 0xffffffffffffffff
  rdi = 0x404040
  rsi = 0x404b98
```

### Solution : 
```
Hint: it may require some tricks to assign a big constant to a dereferenced register.
Try setting a register to the constant value then assigning that register to the dereferenced register.
```

i went ahead and did exactly this, stored the constants into rax, and then set rdi and rsi addresses to those constants via rax. 
```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, 0xdeadbeef00001337
mov [rdi], rax
mov rax, 0xc0ffee0000
mov [rsi], rax
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

ez pz

## flag > `pwn.college{cwg7GG1Z4y5o5JrR-BFRfLpe4yd.0VNwIDL4UjM3QzW}`

<br><br><br>

***

<br><br><br>

# `level-18`

### Challenge : 

```

Recall that memory is stored linearly.

What does that mean?

Say we access the quad word at 0x1337:
  [0x1337] = 0x00000000deadbeef

The real way memory is layed out is byte by byte, little endian:
  [0x1337] = 0xef
  [0x1337 + 1] = 0xbe
  [0x1337 + 2] = 0xad
  ...
  [0x1337 + 7] = 0x00

What does this do for us?

Well, it means that we can access things next to each other using offsets,
similar to what was shown above.

Say you want the 5th *byte* from an address, you can access it like:
  mov al, [address+4]

Remember, offsets start at 0.

Perform the following:
  Load two consecutive quad words from the address stored in rdi
  Calculate the sum of the previous steps quad words.
  Store the sum at the address in rsi

```

### Solution :

pretty straightforward.

- load the value at rdi pointed address, 
- add the above to the quad word beside it (8 bytes beside it ig)
- store the sum in rsi

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, [rdi]
add rax, [rdi + 8]
mov [rsi], rax
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{QHexeIbGOw8hE5FHFo5L_GMEN75.0lNwIDL4UjM3QzW}`

<br><br><br>

***

<br><br><br>

# `level-19`

### Challenge : 

```

In this level you will be working with the stack, the memory region that dynamically expands
and shrinks. You will be required to read and write to the stack, which may require you to use
the pop and push instructions. You may also need to use the stack pointer register (rsp) to know
where the stack is pointing.



In these levels we are going to introduce the stack.

The stack is a region of memory that can store values for later.

To store a value on the stack we use the push instruction, and to retrieve a value we use pop.

The stack is a last in first out (LIFO) memory structure, and this means
the last value pushed in the first value popped.

Imagine unloading plates from the dishwasher let's say there are 1 red, 1 green, and 1 blue.
First we place the red one in the cabinet, then the green on top of the red, then the blue.

Our stack of plates would look like:
  Top ----> Blue
            Green
  Bottom -> Red

Now, if we wanted a plate to make a sandwich we would retrieve the top plate from the stack
which would be the blue one that was last into the cabinet, ergo the first one out.

On x86, the pop instruction will take the value from the top of the stack and put it into a register.

Similarly, the push instruction will take the value in a register and push it onto the top of the stack.

Using these instructions, take the top value of the stack, subtract rdi from it, then put it back.
```

### Solution :

`On x86, the pop instruction will take the value from the top of the stack and put it into a register.`

`Similarly, the push instruction will take the value in a register and push it onto the top of the stack.`

`Using these instructions, take the top value of the stack, subtract rdi from it, then put it back`


v direct

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
pop rax
sub rax, rdi
push rax
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{A2-c678Og4w2NaZtSxzPTTyZYjy.01NwIDL4UjM3QzW}`

<br><br><br>

***

<br><br><br>

# `level-20`

### Challenge : 

```
In this level we are going to explore the last in first out (LIFO) property of the stack.

Using only following instructions:
  push, pop

Swap values in rdi and rsi.
i.e.
If to start rdi = 2 and rsi = 5
Then to end rdi = 5 and rsi = 2

We will now set the following in preparation for your code:
  rdi = 0x2d48d16f
  rsi = 0x3b43a057
```

### Solution :

```
Swap values in rdi and rsi.
i.e.
If to start rdi = 2 and rsi = 5
Then to end rdi = 5 and rsi = 2
```

ok so im supposed to use only push and pop for this. \
ill have to just exchange the values by pushing it into the stack and poppping into the required reg. \
and ill use rax as temp.

i used the same logic as
```
var1 = 'a'
var2 = 'b'

temp = var1
var1 = var2
var2 = temp
```

```python
code = pwn.asm("""
push rdi
pop rax
push rsi
pop rdi
push rax
pop rsi
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

ez pz

## flag > `pwn.college{UR-HMzQYGhoHzHBmx7YOELMF3Xb.0FOwIDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-21`

### Challenge : 

```

In the previous levels you used push and pop to store and load data from the stack.

However you can also access the stack directly using the stack pointer.

On x86, the stack pointer is stored in the special register, rsp.
rsp always stores the memory address of the top of the stack,
i.e. the memory address of the last value pushed.

Similar to the memory levels, we can use [rsp] to access the value at the memory address in rsp.

Without using pop, please calculate the average of 4 consecutive quad words stored on the stack.

Push the average on the stack.

Hint:
  RSP+0x?? Quad Word A
  RSP+0x?? Quad Word B
  RSP+0x?? Quad Word C
  RSP      Quad Word D

We will now set the following in preparation for your code:
  (stack) [0x7fffff200000:0x7fffff1fffe0] = ['0x18efb358', '0x1e85f25f', '0x368c5e55', '0xfd249a2'] (list of things)
```

### Solution :

Ye this was easy

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
add rax, qword ptr [rsp]
add rax, qword ptr [rsp+8]
add rax, qword ptr [rsp+16]
add rax, qword ptr [rsp+24]
mov rdi, 4
div rdi
push rax
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{MDtYB-yw94Gu_YhgXLyxhft6G2h.0VOwIDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-22`

### Challenge : 

```

In this level you will be working with control flow manipulation. This involves using instructions
to both indirectly and directly control the special register `rip`, the instruction pointer.
You will use instructions such as: jmp, call, cmp, and their alternatives to implement the requested behavior.



Earlier, you learned how to manipulate data in a pseudo-control way, but x86 gives us actual
instructions to manipulate control flow directly.

There are two major ways to manipulate control flow:
 through a jump;
 through a call.

In this level, you will work with jumps.

There are two types of jumps:
  Unconditional jumps
  Conditional jumps

Unconditional jumps always trigger and are not based on the results of earlier instructions.

As you know, memory locations can store data and instructions.

Your code will be stored at 0x4000e1 (this will change each run).

For all jumps, there are three types:
  Relative jumps: jump + or - the next instruction.
  Absolute jumps: jump to a specific address.
  Indirect jumps: jump to the memory address specified in a register.

In x86, absolute jumps (jump to a specific address) are accomplished by first putting the target address in a register reg, then doing jmp reg.

In this level we will ask you to do an absolute jump.

Perform the following:
  Jump to the absolute address 0x403000

We will now set the following in preparation for your code:
  Loading your given code at: 0x4000e1
```

### Solution :

`In x86, absolute jumps (jump to a specific address) are accomplished by first putting the target address in a register reg, then doing jmp reg.`

```
Perform the following:
  Jump to the absolute address 0x403000
```

Did exactly as instructed

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, 0x403000
jmp rax
""")

process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{0LH1LEyEE_dkfQId2wpfVErEKFX.dVTM4MDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-23`

### Challenge : 

```

Recall that for all jumps, there are three types:
  Relative jumps
  Absolute jumps
  Indirect jumps

In this level we will ask you to do a relative jump.

You will need to fill space in your code with something to make this relative jump possible.

We suggest using the `nop` instruction. It's 1 byte long and very predictable.

In fact, the as assembler that we're using has a handy .rept directive that you can use to
repeat assembly instructions some number of times:
  https://ftp.gnu.org/old-gnu/Manuals/gas-2.9.1/html_chapter/as_7.html

Useful instructions for this level:
  jmp (reg1 | addr | offset) ; nop

Hint: for the relative jump, lookup how to use `labels` in x86.

Using the above knowledge, perform the following:
  Make the first instruction in your code a jmp
  Make that jmp a relative jump to 0x51 bytes from the current position
  At the code location where the relative jump will redirect control flow set rax to 0x1
```

### Solution :

ok so, jmp to label, execute the mov there. but label gotta be 0x51 from the current location. so nop 51 times before the label. 
How do i nop 51 times ?? : `In fact, the as assembler that we're using has a handy .rept directive that you can use to
repeat assembly instructions some number of times`


  https://ftp.gnu.org/old-gnu/Manuals/gas-2.9.1/html_chapter/as_7.html


```python
code = pwn.asm("""
jmp udhar

.rept 0x51
nop
.endr

udhar:
    mov rax, 0x1
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

ayee

## flag > `pwn.college{MkC8JpbJ9kXcK9mfjHQoUGxFct2.dZTM4MDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-24`

### Challenge : 

```

Now, we will combine the two prior levels and perform the following:
  Create a two jump trampoline:
    Make the first instruction in your code a jmp
    Make that jmp a relative jump to 0x51 bytes from its current position
    At 0x51 write the following code:
      Place the top value on the stack into register rdi
      jmp to the absolute address 0x403000

```

### Solution : 

Same as the last one, just executing a bit more stuff

```python
code = pwn.asm("""
jmp udhar

.rept 0x51
nop
.endr

udhar:
    pop rdi
    mov rax, 0x403000
    jmp rax
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{ID5W-OgWnQWGeUtPjtGuV6wVu4e.0FMxIDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-25`

### Challenge : 

```

We will now introduce you to conditional jumps--one of the most valuable instructions in x86.
In higher level programming languages, an if-else structure exists to do things like:
  if x is even:
    is_even = 1
  else:
   is_even = 0

This should look familiar, since it is implementable in only bit-logic, which you've done in a prior level.

In these structures, we can control the program's control flow based on dynamic values provided to the program.

Implementing the above logic with jmps can be done like so:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
; assume rdi = x, rax is output
; rdx = rdi mod 2
mov rax, rdi
mov rsi, 2
div rsi
; remainder is 0 if even
cmp rdx, 0
; jump to not_even code is its not 0
jne not_even
; fall through to even code
mov rbx, 1
jmp done
; jump to this only when not_even
not_even:
mov rbx, 0
done:
mov rax, rbx
; more instructions here
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Often though, you want more than just a single 'if-else'.

Sometimes you want two if checks, followed by an else.

To do this, you need to make sure that you have control flow that 'falls-through' to the next `if` after it fails.

All must jump to the same `done` after execution to avoid the else.

There are many jump types in x86, it will help to learn how they can be used.

Nearly all of them rely on something called the ZF, the Zero Flag.

The ZF is set to 1 when a cmp is equal. 0 otherwise.

Using the above knowledge, implement the following:
  if [x] is 0x7f454c46:
    y = [x+4] + [x+8] + [x+12]
  else if [x] is 0x00005A4D:
    y = [x+4] - [x+8] - [x+12]
  else:
    y = [x+4] * [x+8] * [x+12]

where:
  x = rdi, y = rax.

Assume each dereferenced value is a signed dword.
This means the values can start as a negative value at each memory position.

A valid solution will use the following at least once:
  jmp (any variant), cmp

We will now run multiple tests on your code, here is an example run:
  (data) [0x404000] = {4 random dwords]}
  rdi = 0x404000
```

### Solution :

took insane inspiration from the example they gave. \
managed to do it first try though 

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov ebx, dword ptr [rdi + 4]
cmp dword ptr [rdi], 0x7f454c46
jne not_that
add ebx, dword ptr [rdi + 8]
add ebx, dword ptr [rdi + 12]
jmp done

not_that:
    cmp dword ptr [rdi], 0x00005A4D
    jne no
    sub ebx, dword ptr [rdi + 8]
    sub ebx, dword ptr [rdi + 12]
    jmp done

no:
    imul ebx, dword ptr [rdi + 8]
    imul ebx, dword ptr [rdi + 12]
    jmp done

done:
    mov rax, rbx
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{4Ic-bB9l9aao0TvIqsaXEsmLCc7.0VMxIDL4UjM3QzW}`



<br><br><br>

***

<br><br><br>

# `level-26`

### Challenge : 

```
The last jump type is the indirect jump, which is often used for switch statements in the real world.

Switch statements are a special case of if-statements that use only numbers to determine where the control flow will go.

Here is an example:
  switch(number):
    0: jmp do_thing_0
    1: jmp do_thing_1
    2: jmp do_thing_2
    default: jmp do_default_thing

The switch in this example is working on `number`, which can either be 0, 1, or 2.

In the case that `number` is not one of those numbers, the default triggers.

You can consider this a reduced else-if type structure.

In x86, you are already used to using numbers, so it should be no suprise that you can make if statements based on something being an exact number.

In addition, if you know the range of the numbers, a switch statement works very well.

Take for instance the existence of a jump table.

A jump table is a contiguous section of memory that holds addresses of places to jump.

In the above example, the jump table could look like:
  [0x1337] = address of do_thing_0
  [0x1337+0x8] = address of do_thing_1
  [0x1337+0x10] = address of do_thing_2
  [0x1337+0x18] = address of do_default_thing

Using the jump table, we can greatly reduce the amount of cmps we use.

Now all we need to check is if `number` is greater than 2.

If it is, always do:
  jmp [0x1337+0x18]
Otherwise:
  jmp [jump_table_address + number * 8]

Using the above knowledge, implement the following logic:
  if rdi is 0:
    jmp 0x403002
  else if rdi is 1:
    jmp 0x4030da
  else if rdi is 2:
    jmp 0x4031f2
  else if rdi is 3:
    jmp 0x4032a9
  else:
    jmp 0x403372

Please do the above with the following constraints:
  Assume rdi will NOT be negative
  Use no more than 1 cmp instruction
  Use no more than 3 jumps (of any variant)
  We will provide you with the number to 'switch' on in rdi.
  We will provide you with a jump table base address in rsi.

Here is an example table:
  [0x404030] = 0x403002 (addrs will change)
  [0x404038] = 0x4030da
  [0x404040] = 0x4031f2
  [0x404048] = 0x4032a9
  [0x404050] = 0x403372
```

### Solution : 

just like the example, 
```
check if rdi is greater than 3.

If it is, always do:
  jmp [rsi + 4 * 8]
Otherwise:
  jmp [rsi +  rdi * 8]
```

ez pz

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
cmp rdi, 3
jg else
imul rdi, 8
add rsi, rdi
jmp [rsi]

else:
    add rsi, 32
    jmp [rsi]
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{8JCaWQRfB2NfFyeq5-A2ZKIFSzL.0lMxIDL4UjM3QzW}`



<br><br><br>

***

<br><br><br>

# `level-27`

### Challenge : 

```

In a previous level you computed the average of 4 integer quad words, which
was a fixed amount of things to compute, but how do you work with sizes you get when
the program is running?

In most programming languages a structure exists called the
for-loop, which allows you to do a set of instructions for a bounded amount of times.
The bounded amount can be either known before or during the programs run, during meaning
the value is given to you dynamically.

As an example, a for-loop can be used to compute the sum of the numbers 1 to n:
  sum = 0
  i = 1
  while i <= n:
    sum += i
    i += 1

Please compute the average of n consecutive quad words, where:
  rdi = memory address of the 1st quad word
  rsi = n (amount to loop for)
  rax = average computed
```

### Solution : 

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, 0
mov rbx, -1
jmp while

while:
    add rbx, 1
    add rax, [rdi + rbx * 8]
    cmp rbx, rsi
    jle while

div rsi
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{A1iqXQb_blJLGG5P9Y_zL8NYa7M.01MxIDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-28`

### Challenge : 

```
In previous levels you discovered the for-loop to iterate for a *number* of times, both dynamically and
statically known, but what happens when you want to iterate until you meet a condition?

A second loop structure exists called the while-loop to fill this demand.

In the while-loop you iterate until a condition is met.

As an example, say we had a location in memory with adjacent numbers and we wanted
to get the average of all the numbers until we find one bigger or equal to 0xff:
  average = 0
  i = 0
  while x[i] < 0xff:
    average += x[i]
    i += 1
  average /= i

Using the above knowledge, please perform the following:
  Count the consecutive non-zero bytes in a contiguous region of memory, where:
    rdi = memory address of the 1st byte
    rax = number of consecutive non-zero bytes

Additionally, if rdi = 0, then set rax = 0 (we will check)!

An example test-case, let:
  rdi = 0x1000
  [0x1000] = 0x41
  [0x1001] = 0x42
  [0x1002] = 0x43
  [0x1003] = 0x00

then: rax = 3 should be set

We will now run multiple tests on your code, here is an example run:
  (data) [0x404000] = {10 random bytes},
  rdi = 0x404000
```

### Solution : 

```python
import pwn
pwn.context.update("arch64")

code = pwn.asm("""
mov rax, 0
mov rbx, 0
cmp rdi, 0
je exit
mov rbx, -1

while:
    add rbx, 1
    cmp byte ptr [rdi+rbx], 0
    jne while

exit:
    mov rax,rbx
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{M-YdS2P0zH591p15ZX_rsf03Whp.0FNxIDL4UjM3QzW}`

<br><br><br>

***

<br><br><br>

# `level-29`

### Challenge : 

```

In this level you will be provided with a contiguous region of memory again and will loop
over each performing a conditional operation till a zero byte is reached.
All of which will be contained in a function!

A function is a callable segment of code that does not destroy control flow.

Functions use the instructions "call" and "ret".

The "call" instruction pushes the memory address of the next instruction onto
the stack and then jumps to the value stored in the first argument.

Let's use the following instructions as an example:
  0x1021 mov rax, 0x400000
  0x1028 call rax
  0x102a mov [rsi], rax

1. call pushes 0x102a, the address of the next instruction, onto the stack.
2. call jumps to 0x400000, the value stored in rax.

The "ret" instruction is the opposite of "call".

ret pops the top value off of the stack and jumps to it.

Let's use the following instructions and stack as an example:

                              Stack ADDR  VALUE
  0x103f mov rax, rdx         RSP + 0x8   0xdeadbeef
  0x1042 ret                  RSP + 0x0   0x0000102a

Here, ret will jump to 0x102a

Please implement the following logic:
  str_lower(src_addr):
    i = 0
    if src_addr != 0:
      while [src_addr] != 0x00:
        if [src_addr] <= 0x5a:
          [src_addr] = foo([src_addr])
          i += 1
        src_addr += 1
    return i

foo is provided at 0x403000.
foo takes a single argument as a value and returns a value.

All functions (foo and str_lower) must follow the Linux amd64 calling convention (also known as System V AMD64 ABI):
  https://en.wikipedia.org/wiki/X86_calling_conventions#System_V_AMD64_ABI

Therefore, your function str_lower should look for src_addr in rdi and place the function return in rax.

An important note is that src_addr is an address in memory (where the string is located) and [src_addr] refers to the byte that exists at src_addr.

Therefore, the function foo accepts a byte as its first argument and returns a byte.
```

### Solution : 

```python
import pwn
pwn.context.update("arch64")
code = pwn.asm("""
mov rax, 0

mov rsi,rdi
cmp rsi,0
je exit

while_loop:
    mov bl, [rsi]
    cmp bl, 0
    je exit

    cmp bl,0x5a
    jg foo_skip

    mov dil,bl

    mov rdx,rax
    mov rcx,0x403000
    call rcx
    mov [rsi],al
    mov rax,rdx

    inc rax
    jmp foo_skip


foo_skip:
    inc rsi
    jmp while_loop

exit:
    ret
""")
process = pwn.process("/challenge/run")
process.write(code)
print(process.readall())
```

## flag > `pwn.college{gBAJEsA01f1kSZzQRVF6N14gJxD.0VNxIDL4UjM3QzW}`


<br><br><br>

***

<br><br><br>

# `level-30`

### Challenge : 

```
In the previous level, you learned how to make your first function and how to call other functions.

Now we will work with functions that have a function stack frame.

A function stack frame is a set of pointers and values pushed onto the stack to save things for later use and allocate space on the stack for function variables.

First, let's talk about the special register rbp, the Stack Base Pointer.

The rbp register is used to tell where our stack frame first started.

As an example, say we want to construct some list (a contigous space of memory) that is only used in our function.

The list is 5 elements long, and each element is a dword.

A list of 5 elements would already take 5 registers, so instead, we can make space on the stack!

The assembly would look like:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
; setup the base of the stack as the current top
mov rbp, rsp
; move the stack 0x14 bytes (5 * 4) down
; acts as an allocation
sub rsp, 0x14
; assign list[2] = 1337
mov eax, 1337
mov [rbp-0x8], eax
; do more operations on the list ...
; restore the allocated space
mov rsp, rbp
ret
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Notice how rbp is always used to restore the stack to where it originally was.

If we don't restore the stack after use, we will eventually run out.

In addition, notice how we subtracted from rsp, because the stack grows down.

To make the stack have more space, we subtract the space we need.

The ret and call still works the same.

Once, again, please make function(s) that implements the following:
most_common_byte(src_addr, size):
  i = 0
  while i <= size-1:
    curr_byte = [src_addr + i]
    [stack_base - curr_byte] += 1
    i += 1

  b = 0
  max_freq = 0
  max_freq_byte = 0
  while b <= 0xff:
    if [stack_base - b] > max_freq:
      max_freq = [stack_base - b]
      max_freq_byte = b
    b += 1

  return max_freq_byte

Assumptions:
  There will never be more than 0xffff of any byte
  The size will never be longer than 0xffff
  The list will have at least one element
Constraints:
  You must put the "counting list" on the stack
  You must restore the stack like in a normal function
  You cannot modify the data at src_addr
```
