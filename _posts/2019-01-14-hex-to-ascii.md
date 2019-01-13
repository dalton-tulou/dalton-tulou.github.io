---
layout: post
author: Dave
title:  "Tricky hex to ascii conversion"
categories: [software]
tags: [branchless, tricks, z80]
---

When I was working on a game I decided to use BCD for keeping track of score and time.  So I began reading up on decimal adjustment on the Z80, and a very interesting trick came my way.

Hex to ASCII conversion is the process of converting a number in the range 0x0 ... 0xf to a character in the range "0" ... "9" / "A" ... "F". It's a simple process, but it's somewhat obstructed by the fact that characters are not in a continuous range like the numbers. "0" through "9" is 0x30 through 0x39, and "A" through "F" is 0x41 through 0x46.

A straight-forward C-implementation looks like this:

```c
char hex_to_ascii(int digit)
{
    if (digit < 10)
    {
        return 0x30 + digit;
    }
    else
    {
        return 0x41 + (digit - 10);
    }
}
```

But in order to apply the trick, we must first reformulate to get rid of the else-clause, like this:

```c
char hex_to_ascii(int digit)
{
    if (digit > 9)
    {
        digit = digit + 7;  // 0x41-0x30-10 = 7
    }
    
    return 0x30+digit;
}
```

So we see that the conversion essentially boils down to adding 0x30 to the digit, and then add another 7 if the hex digit was greater than 9.

What's this got to do with decimal adjustment? Well, decimal adjustment actually adds 6 to a byte if the lower nibble (bits 3 down to 0) is greater than 9. That's almost enough. If we can just make that addition overflow too, then we could add the missing 1 from the carry flag.

So here we go, this time in Z80 assembly language:

```
                 ; $000 ... $009 / $00a ... $00f
1:    add $90    ; $090 ... $099 / $09a ... $09f
2:    daa        ; $090 ... $099 / $100 ... $105
3:    adc $40    ; $0d0 ... $0d9 / $041 ... $046
4:    daa        ; $030 ... $039 / $041 ... $046
```

The first two lines perform a BCD addition of 0x90 to the accumulator. This is abusing the instruction, because the accumulator is not guaranteed to be within BCD range in the first place. Numbers 0x0A ...0x0F will case overflow, both in the lower and upper nibble, thus adjusting into 0x100 ... 0x105, where the leading 1 goes into the carry flag.

Lines 3 and 4 performs another BCD addition, also bringing in the carry. The numbers that did not overflow in the previous DAA, 0x90 ... 0x99, will have 0x40 added to them, ending up in the range 0xd0 ... 0xd9. Decimal adjust will put that in range 0x130 ... 0x139, where the leading 1 goes into carry.

Thus the number which previously overflowed, 0x00 ... 0x05, will have 0x41 added to them thanks to the carry flag, and end up in the range 0x41 ... 0x46. The following DAA will not affect them, as they are valid BCD.

Thus we have converted a hex digit in the range 0x0 ... 0xf to ASCII in only four instructions. That's pretty cool.

However, if things are turned around, there's an even better way. Consider translating the hex digit using subtraction rather than addition. Then we'd make use of carry in the range 0 ... 9 to get the numbers in place. Carry can easily be generated using a *compare*.

```
               ; $00 ... $09 / $0a ... $0f
1:    cp  $0A  ;    carry    /   no carry
2:    sbc $69  ; $96 ... $9f / $a1 ... $a6
3:    daa      ; $30 ... $39 / $41 ... $46
```

Lines 1 and 2 are straight forward, there's no BCD magic going. Only on line 3 is there trickery. It will effectively subtract 0x66 from the numbers in range 0x96 ... 0x9f, putting them in the 0x30 ... 0x39 range. The numbers in range 0xa1 ... 0xa6 will have 0x60 subtracted from them, putting them in range 0x41 ... 0x46.
 
References:

[https://stackoverflow.com/questions/8119577/z80-daa-instruction](https://stackoverflow.com/questions/8119577/z80-daa-instruction)
[http://www.vcfed.org/forum/archive/index.php/t-26198.html](http://www.vcfed.org/forum/archive/index.php/t-26198.html)