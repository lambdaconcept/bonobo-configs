interface bonobo
transport select swd
adapter_khz 10000

reset_config srst_only

source [find target/swj-dp.tcl]

if { [info exists CHIPNAME] } {
   set _CHIPNAME $CHIPNAME
} else {
   set _CHIPNAME iphone
}

if { [info exists ENDIAN] } {
   set _ENDIAN $ENDIAN
} else {
   set _ENDIAN little
}

if { [info exists CPUTAPID] } {
   set _CPUTAPID $CPUTAPID
} else {
   if { [using_jtag] } {
      set _CPUTAPID 0x4ba02477
   } {
      # SWD IDCODE
      set _CPUTAPID 0x4ba02477
   }
}
swj_newdap $_CHIPNAME cpu -irlen 6 -ircapture 0x1 -irmask 0xf -expected-id $_CPUTAPID
dap create $_CHIPNAME.dap -chain-position $_CHIPNAME.cpu


# MEM-AP
target create $_CHIPNAME.dbg mem_ap -endian $_ENDIAN -dap $_CHIPNAME.dap -ap-num 1
target create $_CHIPNAME.mem mem_ap -endian $_ENDIAN -dap $_CHIPNAME.dap -ap-num 4

# CPU0
cti create $_CHIPNAME.cpu0.cti -dap $_CHIPNAME.dap -ap-num 1 -ctibase 0xc2020000
target create $_CHIPNAME.cpu0 aarch64 -endian $_ENDIAN -dap $_CHIPNAME.dap -ap-num 1 -dbgbase 0xc2010000 -cti $_CHIPNAME.cpu0.cti -coreid 0 -apple-utt 4 0x202040000 64

# CPU1
cti create $_CHIPNAME.cpu1.cti -dap $_CHIPNAME.dap -ap-num 1 -ctibase 0xc2120000
target create $_CHIPNAME.cpu1 aarch64 -endian $_ENDIAN -dap $_CHIPNAME.dap -ap-num 1 -dbgbase 0xc2110000 -cti $_CHIPNAME.cpu1.cti -coreid 1 -apple-utt 4 0x202140000 64

# SMP
target smp $_CHIPNAME.cpu0 $_CHIPNAME.cpu1

# SEP
target create $_CHIPNAME.sep cortex_a -endian $_ENDIAN -dap $_CHIPNAME.dap -ap-num 1 -dbgbase 0xcda20000


init
