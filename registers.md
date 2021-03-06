# Registers

The registers are part of the user area stored in the context structure used by the scheduler. This structure can be manipulated to get and set values for those registers, and on intel for example, is possible to directly manipulate the DRx hardware registers to setup hardware breakpoints.

There are different commands to get values of the registers. For the General Purpose ones use:

    [0x4A13B8C0]> dr
    r15 = 0x00000000
    r14 = 0x00000000
    r13 = 0x00000000
    r12 = 0x00000000
    rbp = 0x00000000
    rbx = 0x00000000
    r11 = 0x00000000
    r10 = 0x00000000
    r9 = 0x00000000
    r8 = 0x00000000
    rax = 0x00000000
    rcx = 0x00000000
    rdx = 0x00000000
    rsi = 0x00000000
    rdi = 0x00000000
    oeax = 0x0000003b
    rip = 0x7f20bf5df630
    rsp = 0x7fff515923c0

    [0x7f0f2dbae630]> dr?rip ; get value of 'eip'
    0x7f0f2dbae630

    [0x4A13B8C0]> dr eip = esp   ; set 'eip' as esp
    
    
The interaction between the plugin and the core is done by commands returning radare instructions. This is used for example to set some flags in the core to set the values of the registers.

    [0x7f0f2dbae630]> dr*      ; Appending '*' will show radare commands
    f r15 1 0x0
    f r14 1 0x0
    f r13 1 0x0
    f r12 1 0x0
    f rbp 1 0x0
    f rbx 1 0x0
    f r11 1 0x0
    f r10 1 0x0
    f r9 1 0x0
    f r8 1 0x0
    f rax 1 0x0
    f rcx 1 0x0
    f rdx 1 0x0
    f rsi 1 0x0
    f rdi 1 0x0
    f oeax 1 0x3b
    f rip 1 0x7fff73557940
    f rflags 1 0x200
    f rsp 1 0x7fff73557940


    [0x4A13B8C0]> .dr*  ; include common register values in flags

An old copy of the registers is stored all the time to keep track of the changes done during the execution of the program. This old copy can be accessed with `oregs`.

      [0x7f1fab84c630]> dro
      r15 = 0x00000000
      r14 = 0x00000000
      r13 = 0x00000000
      r12 = 0x00000000
      rbp = 0x00000000
      rbx = 0x00000000
      r11 = 0x00000000
      r10 = 0x00000000
      r9 = 0x00000000
      r8 = 0x00000000
      rax = 0x00000000
      rcx = 0x00000000
      rdx = 0x00000000
      rsi = 0x00000000
      rdi = 0x00000000
      oeax = 0x0000003b
      rip = 0x7f1fab84c630
      rflags = 0x00000200
      rsp = 0x7fff386b5080


      [0x7f1fab84c630]> dr
      r15 = 0x00000000
      r14 = 0x00000000
      r13 = 0x00000000
      r12 = 0x00000000
      rbp = 0x00000000
      rbx = 0x00000000
      r11 = 0x00000000
      r10 = 0x00000000
      r9 = 0x00000000
      r8 = 0x00000000
      rax = 0x00000000
      rcx = 0x00000000
      rdx = 0x00000000
      rsi = 0x00000000
      rdi = 0x7fff386b5080
      oeax = 0xffffffffffffffff
      rip = 0x7f1fab84c633
      rflags = 0x00000202
      rsp = 0x7fff386b5080

The values in eax, oeax and eip has changed.

To store and restore the register values you can just dump the output of 'dr*' command to disk and then re-interpret it again:


    [0x4A13B8C0]> dr* > regs.saved ; save registers
    [0x4A13B8C0]> drp regs.saved ; restore


In the same way the eflags can be altered by the characters given in this way. Setting to '1' the selected flags:

    [0x4A13B8C0]> dr eflags = pst
    [0x4A13B8C0]> dr eflags = azsti

You can easily get a string representing the last changes in the registers using the 'drd' command (diff registers):

    [0x4A13B8C0]> drd
    orax = 0x0000003b was 0x00000000 delta 59
    rip = 0x7f00e71282d0 was 0x00000000 delta -418217264
    rflags = 0x00000200 was 0x00000000 delta 512
    rsp = 0x7fffe85a09c0 was 0x00000000 delta -396752448