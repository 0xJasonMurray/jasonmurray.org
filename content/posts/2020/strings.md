---
title: "Using strings to peek into binaries"
date: 2020-08-19T15:34:04-05:00
toc: false
images:
categories:
  - tech
tags: 
  - strings
  - security
---

Unlike text files, shell scripts, and other human readable file formats, looking into a binary file typically displays gibberish:

```
jemurray@shell:~/sec$ cat hacker
ELF>P@`9@8
          @@@@hhh   HH-==HP-=DDPtd   <<QtdRtd-==/lib64/ld-linux-x86-64.so.2GNUGNURNɇx
                                                                                     jǑ>Cem?
                                                                                             ![ j "libc.so.6system__cxa_finalize__libc_start_mainGLIBC_2.2.5_ITM_deregisterTMCloneTableH=f/DH=/H/H9tH>/Ht_regisH=y/H5r/H)HHH?HHtH/HfD=9/u/UH=.Ht?@H/Ht5/%/@%/h%/f1I^HHPTLzH
                                                         H=/-h/]{UHH@HEHfAWIAVIAUAATL%P,UH-P,SL)SHtLLDAHH9u[]A\A]A^A_8<LT1|,zRx
                                                                                                                                                          Rx
                                                                                                                                                           J
D                                                                                                                                                           ?;*3$"\eIAC
D≠]BEE E(H▮H8G@┘8A▮A(B BB▮
▮

@P      ⎺⎻⎺⎺\⎺=6(@GCC: (Deb☃▒┼ 8↓3↓▮↑6) 8↓3↓▮⎻
P

@P  @ ===?@ @0@
               !7▮@F=└▮≤=D!=== @
                                 └  @1▮@8Lk @x (@ ]8@─P→▮@5I▮@ "c⎼├⎽├┤°°↓cde⎼e±☃⎽├e⎼_├└_c┌⎺┼e⎽__d⎺_±┌⎺b▒┌_d├⎺⎼⎽_▒┤│c⎺└⎻┌e├ed↓7325__d⎺_±┌⎺b▒┌_d├⎺⎼⎽_▒┤│_°☃┼☃_▒⎼⎼▒≤_e┼├⎼≤°⎼▒└e_d┤└└≤__°⎼▒└e_d┤└└≤_☃┼☃├_▒⎼⎼▒≤_e┼├⎼≤▒▒c┐e⎼↓c__FRAME_END____☃┼☃├_▒⎼⎼▒≤_e┼d_DYNAMIC__☃┼☃├_▒⎼⎼▒≤_⎽├▒⎼├__GNU_EH_FRAME_HDR_GLOBAL_OFFSET_TABLE___┌☃bc_c⎽┤_°☃┼☃_ITM_de⎼e±☃⎽├e⎼TMC┌⎺┼eT▒b┌e_ed▒├▒⎽≤⎽├e└@@GLIBC_2↓2↓5__┌☃bc_⎽├▒⎼├_└▒☃┼@@GLIBC_2↓2↓5__d▒├▒_⎽├▒⎼├__±└⎺┼_⎽├▒⎼├____d⎽⎺_▒▒┼d┌e_IO_⎽├d☃┼_┤⎽ed__┌☃bc_c⎽┤_☃┼☃├__b⎽⎽_⎽├▒⎼├└▒☃┼__TMC_END___ITM_⎼e±☃⎽├e⎼TMC┌⎺┼eT▒b┌e__c│▒_°☃┼▒┌☃≥e@@GLIBC_2↓2↓5↓⎽≤└├▒b↓⎽├⎼├▒b↓⎽▒⎽├⎼├▒b↓☃┼├e⎼⎻↓┼⎺├e↓ABI↑├▒±↓┼⎺├e↓±┼┤↓b┤☃┌d↑☃d↓±┼┤↓▒▒⎽▒↓d≤┼⎽≤└↓d≤┼⎽├⎼↓±┼┤↓┴e⎼⎽☃⎺┼↓±┼┤↓┴e⎼⎽☃⎺┼_⎼↓⎼e┌▒↓d≤┼↓⎼e┌▒↓⎻┌├↓☃┼☃├↓⎻┌├↓±⎺├↓├e│├↓°☃┼☃↓⎼⎺d▒├▒↓e▒_°⎼▒└e_▒d⎼↓e▒_°⎼▒└e↓☃┼☃├_▒⎼⎼▒≤↓°☃┼☃_▒⎼⎼▒≤↓d≤┼▒└☃c↓±⎺├↓⎻┌├↓d▒├▒↓b⎽⎽↓c⎺└└e┼├$N⎺
                                                         ▮V^⎺\\┐⎺⎻⎻BP  PP@          <=?@ @0@0000P0-     P6V8
```

By using the `strings` command, information within the binary is displayed in a slightly "more" human readable format:

```
jemurray@shell:~/sec$ strings hacker
/lib64/ld-linux-x86-64.so.2
libc.so.6
system
__cxa_finalize
__libc_start_main
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u/UH
/bin/fakH
e/rm -rfH
[]A\A]A^A_
;*3$"
GCC: (Debian 8.3.0-6) 8.3.0
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.7325
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
hacker.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
_ITM_deregisterTMCloneTable
_edata
system@@GLIBC_2.2.5
__libc_start_main@@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
__bss_start
main
__TMC_END__
_ITM_registerTMCloneTable
__cxa_finalize@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.ABI-tag
.note.gnu.build-id
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.got.plt
.data
.bss
.comment
```

It still looks like gibberish right?  But look closer...  What is this little piece:

```
/bin/fakH
e/rm -rfH
```

Finding the string `rm -rf` in a binary file called `./hacker` is probably not good.

While this is a made up example, `strings` is one of the primary tools I use when investigating malicious software while doing forensic analysis on compromised systems.

