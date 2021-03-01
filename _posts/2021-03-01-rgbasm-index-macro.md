---
layout: post
author: Dave
title:  "An RGBASM macro for indexing short tables in arbitrary locations"
categories: [software]
tags: [gameboy,macro]
---

I used to always align tables with 256-byte boundaries in my GameBoy programs. It makes lookups very efficient, because the index simply constitutes the lower byte of the 16-bit address. But many times indexing is not time critical, so the main benefit of aligned tables is probably that it's very convenient to write and read such code. 
The drawback of aligned tables is that, unless the tables are all 256 bytes large, you will get memory fragmentation. This is not such a big proglem in the ROM, because the linker can remedy the fragmentation as long as there are small segments to fill the gaps.
But when it comes to RAM it's a different story. RAM is scarse, and my experience is that you'll waste precious parts of it due to fragmentation if you constrain the data locations. So we could benefit from locating the data more freely. Thus, a macro for indexing tables in arbitrary locations can be useful.

Here is a macro which adds the unsigned accumulator to a 16-bit number (register or constant), and stores the result in a register (like `\1 = \2 + a`) with reasonable effiency and without thrashing any registers.

```
INDEX:  MACRO
        add     LOW(\2)
        ld      LOW(\1),a
        adc     HIGH(\2)
        sub     LOW(\1)
        ld      HIGH(\1),a
        ENDM
```

Typical use:

`INDEX hl,table` equivalent of `hl = table+a`

`INDEX de,de` equivalent of `de += a`
