Here are the (not so easy to follow) instructions to install SysIII on the SIMH VAX Emulator.

Credits to [https://web.archive.org/web/20190411165353/http://a.papnet.eu/UNIX/sysIII_vax/Installation](https://web.archive.org/web/20190411165353/http://a.papnet.eu/UNIX/sysIII_vax/Installation):

-----------------------------------------------

# UNIX System III on VAX

The distribution files that you could once get from bitsavers are not in good shape. I've put the original files [here](https://web.archive.org/web/20190411165353/http://a.papnet.eu/UNIX/sysIII_vax/files/dist_orig). Most importantly the root file system image is completely unusable. I have no idea what happened to it but it's not a file system image that should work with any UNIX. Luckily the tape also comes with the contents of the root fs in a cpio archive, and since 32V's fs is pretty much compatible with SysIII and can also execute SysIII executables I created a fixed root image for the SysIII installation from 32V. You can find the distribution files [here](https://web.archive.org/web/20190411165353/http://a.papnet.eu/UNIX/sysIII_vax/files) and a working tape [here](https://web.archive.org/web/20190411165353/http://a.papnet.eu/UNIX/sysIII_vax/files/dist.tape).

Make sure you have read the [setup](https://web.archive.org/web/20190411165353/http://a.papnet.eu/UNIX/sysIII_pdp11/files/docs/setup.pdf) document, because that's what we'll follow and it contains more info than this quick guide.

## Installing the root file system

Create a file `install.ini`:

```set tto 7b
set rp0 rp06
att rp0 disk.hp
set tu0 te16
att tu0 dist.tape
; tape boot
D 30000 20009FDE
D 30004 D0512001
D 30008 3204A101
D 3000C C113C08F
D 30010 A1D40424
D 30014 008FD00C
D 30018 C1800000
D 3001C 8F320800
D 30020 10A1FE00
D 30024 00C139D0
D 30028 04c1d004
D 3002C 07e15004
D 30030 0000f750
g 30000
g 0
```

And run the installer:

```
% vax780 install.ini 

VAX 11/780 simulator V4.0-0 Beta        git commit id: fc417114
RP0: creating new file
Overwrite last track? [N]

HALT instruction, PC: 00030033 (HALT)
UNIX tape boot loader

UNIX -- Initial Load: Tape-to-Disk

The type of disk drive on which the Root file system will reside,
as well as the type of tape drive that will be used for Tape 1   
must be specified below.                                      

Answer the questions with a 'y' or 'n' followed by
a carriage return or line feed.                   
There is no type-ahead -- wait for the question to complete.
The character '@' will kill the entire line                 
while the character '#' will erase the last character typed.

RP06 at NEXUS 8?: y
Drive number (0-7)?: 0
Disk drive 0 selected.

Mount a formatted pack on drive 0.
Ready?: y                         

TE16 at NEXUS 9?: y
Drive number (0-7)?: 0
Tape drive 0 selected.

The tape on drive 0 will be read from the current position
at 800bpi, 5120 characters (10 blocks) per record,        
and written onto the pack on drive 0 starting at block 0.

Ready?: y
Size of filesystem to be copied is 7000 blocks.
What is the pack volume label? (e.g. p0001):   
The pack will be labeled p0001.              
The boot block for your type of disk drive will now be installed.

The file system copy is now complete.

To boot the basic UNIX for your disk and tape drives
as indicated above, mount this pack on drive 0      
and read in the boot block (block 0) using whatever
means you have available; see vaxops(8).           

Then boot the program unixhpht using the stand-alone shell.
Normally:  $$ unixhpht                                     

The system will initially come up single-user; see init(8).
If you have an upper case only console terminal,           
you must execute: stty lcase; see stty(1).      

After UNIX is up, link the file unixhpht to unix using ln(1).
    # ln /unixhpht /unix                                 

Set the date(1).

Good Luck!

The tape will now be rewound.


HALT instruction, PC: 000008CB (RET)
sim>
```

## Booting UNIX

Create a file `install2.ini` that we'll use to boot from disk:

```
set tto 7b
set rp0 rp06
att rp0 disk.hp
set tu0 te16
att tu0 dist.tape
; disk boot
D 30000 00009FDE
D 30004 D0512001
D 30008 D004A101
D 3000C 0400C113
D 30010 10008F32
D 30014 D40424C1
D 30018 8FD00CA1
D 3001C 80000000
D 30020 320800C1
D 30024 A1FE008F
D 30028 28C1D410
D 3002C 14C1D404
D 30030 C139D004
D 30034 c1d00400
D 30038 e1500404
D 3003C 00f75007
g 30000
g 2
```

And boot. Now we pretty much just follow '2.6 Initial Load of /usr' of the setup document except we make a bigger filesystem, and what the two source archives are I'm not so sure about either.

```
% vax780 install2.ini 

VAX 11/780 simulator V4.0-0 Beta        git commit id: fc417114

HALT instruction, PC: 00030040 (HALT)

$$ unixhpht

UNIX/3.0.1: unixhpht
real mem  = 8323072 
avail mem = 8207872
unix               
single-user
#
# echo </dev/mt4

# echo </dev/mt4

# echo </dev/mt4

# echo </dev/mt4

# /etc/mkfs /dev/rrp1 322278 7 418
isize = 65496
m/n = 7 418
# labelit /dev/rrp1 usr p0002
Current fsname: , Current volname: , Blocks: 322278
Date last mounted: Wed Dec 24 11:44:43 1980
NEW fsname = usr, NEW volname = p0002 -- DEL if wrong!!
# mount /dev/rp1 /usr
# cd /usr
# cpio -idmB </dev/rmt4
24920 blocks
# cpio -idmB </dev/rmt4
540 blocks
cpio -idmB </dev/rmt4
700 blocks
# mkdir man
# cd man
# cpio -idmB </dev/rmt4
7560 blocks
#
```

Now we configure the system so we can go into multiuser mode:

```
# ed /etc/rc
1197
/umount/
        : put umounts here
a
        /etc/umount /dev/rp1
.
/mount/
    : put mounts here
a
    /etc/mount /dev/rp1 /usr
.
w
1246
q
#
# ln /unixhpht /unix
```

Now halt the system:

```
# sync
# sync
# sync
# 
Simulation stopped, PC: 80000625 (BLBC 8000BEF4,80000625)
sim> q
Goodbye
```

## Booting the installed system

To boot the system I use this `boot.ini`:

```
set tto 7b
set rp0 rp06
att rp0 disk.hp
; disk boot
D 30000 00009FDE
D 30004 D0512001
D 30008 D004A101
D 3000C 0400C113
D 30010 10008F32
D 30014 D40424C1
D 30018 8FD00CA1
D 3001C 80000000
D 30020 320800C1
D 30024 A1FE008F
D 30028 28C1D410
D 3002C 14C1D404
D 30030 C139D004
D 30034 c1d00400
D 30038 e1500404
D 3003C 00f75007
g 30000
g 2
```

Boot the file unix and type init 2 to go into multi-user mode:

```
% vax780 boot.ini 

VAX 11/780 simulator V4.0-0 Beta        git commit id: fc417114

HALT instruction, PC: 00030040 (HALT)

$$ unix

UNIX/3.0.1: unixhpht
real mem  = 8323072 
avail mem = 8207872
unix               
single-user
# init 2
# process accounting started
errdemon started
cron started
multi-user
type ctrl-d

login: root
UNIX Release 3.0
# sync
# sync
# sync
# 
Simulation stopped, PC: 80000625 (BLBC 8000BEF4,80000625)
sim> q
Goodbye
```

And that's all!

