---
tags: [Reverse Engineering]
title: Exatlon V1
created: '2020-06-09T09:52:29.865Z'
modified: '2020-11-29T16:36:17.509Z'
---

# Exatlon V1

- ran the program - asks for a password
- file exatlon found it had no headers (weird)
- tried to decompile with objdump - didn't work (wtf)
- decompiled in ghidra
  - still no headers and no main function
  - strange so far - code was also VERY obfuscated and made little sense
  - looked at ascii of hex dump - saw it mentioned UPX this means the file is UPX packed
- unpack and find entry point with readelf (-h) - 0x4049ad
- break at offset in gdb
- run
- disas *0xoffset,+50
- found address of main fucntion  - 0x49e0a0
- disas
- open radare2 and search strings to see if we can find anything else of interest. We dont but we find main func again to confirm this
- ghidra - main function look at function graph
  - see enter password here and notice the "looks good ^^"
  - hmm this must be the password
  - looks to be encoded - try and decode
  - sees exatlon function is called - seems to be doing something with user input
  - trying to set breakpoint at different parts in the memory and step through to see what it does.


oo+ - read/writemode
V - visual mode
P  - changes view
C - to use arrow keys toselect where to input nop
i - insert hex
A - insert assembly 
  ```bash
  

  ```
