# CoreFreq
## Purpose
CoreFreq is a CPU monitoring software designed for the Intel 64-bits Processors w/ architectures Atom, Core2, Nehalem, SandyBridge and above.

![alt text](http://blog.cyring.free.fr/images/CoreFreq_Top.gif "CoreFreq Top")

CoreFreq provides a framework to retrieve CPU data with a high degree of precision:

* Core frequencies & ratios; SpeedStep (EIST), Turbo Boost, Hyper-Threading (HTT) and Base Clock
* Performance counters including Time Stamp Counter (TSC), Unhalted Core Cycles (UCC), Unhalted Reference Cycles (URC)
* Number of instructions per cycle or second, IPS, IPC, or CPI
* CPU C-States C0 C1 C3 C6 C7 - C1E - Auto/UnDemotion of C1 C3
* DTS Temperature and Tjunction Max, Thermal Monitoring TM1 TM2 state
* Topology map including Caches for boostrap & application CPU
* Processor features, brand & architecture strings


To reach this goal, CoreFreq implements a Linux Kernel module which employs the followings:

* asm code to keep as near as possible the read of counters
* per-CPU slab data memory and per-CPU high-resolution timer
* compliant with suspend & resume to save and restore the Performance register bits
* a shared memory to protect kernel & user-space parts of the software
* atomic synchronization of threads to avoid mutexes and deadlock


## Build & Run
 1- Download or clone the source code into a working directory.
 
 2- Build the programs.
```
make
```

```
cc -c corefreqd.c -o corefreqd.o
cc -lpthread -lrt -o corefreqd corefreqd.c
cc -c corefreq-cli.c -o corefreq-cli.o
cc -lrt -o corefreq-cli corefreq-cli.c
make -C /lib/modules/4.7.2-1-ARCH/build M=/workdir/CoreFreq modules
make[1]: Entering directory '/usr/lib/modules/4.7.2-1-ARCH/build'
  CC [M]  /workdir/CoreFreq/corefreqk.o
  Building modules, stage 2.
  MODPOST 1 modules
  CC      /workdir/CoreFreq/corefreqk.mod.o
  LD [M]  /workdir/CoreFreq/corefreqk.ko
make[1]: Leaving directory '/usr/lib/modules/4.7.2-1-ARCH/build'
```

### Start

 3- Load the kernel module, as root.
```
modprobe corefreqk
```
 _or_
```
insmod corefreqk.ko
```
 4- Start the daemon, as root.
```
./corefreqd
```
 5- Start the client, as a user.
```
./corefreq-cli
```

### Stop

 6- Press [CTRL]+[C] to stop the client.

 7- Press [CTRL]+[C] to stop the daemon.

 8- Unload the kernel module with the ```rmmod``` command
```
rmmod corefreqk.ko
```

## Screenshots
### Linux kernel module
Use ```dmesg``` or ```journalctl -k``` to check if the module is started
```
CoreFreq: Processor [06_1A] Architecture [Nehalem/Bloomfield] CPU [8/8]
```

### Daemon
```
CoreFreq Daemon.  Copyright (C) 2015-2016 CYRIL INGENIERIE

  Processor [Intel(R) Core(TM) i7 CPU 920 @ 2.67GHz]
  Architecture [Nehalem/Bloomfield] 8/8 CPU Online.
```

### Client
Without arguments, the corefreq-cli program displays Top Monitoring  
_Remark_: Drawing will stall if the terminal width is lower than 80 columns, or its height is less than required.

 * With the option '-c', the client traces counters.
![alt text](http://blog.cyring.free.fr/images/CoreFreq_Counters.gif "CoreFreq Counters")

 * Using option '-m' corefreq-cli shows the CPU topology
![alt text](http://blog.cyring.free.fr/images/CoreFreq_Topology.png "CoreFreq CPU & caches topology")

 * With the option '-i' corefreq-cli traces the number of instructions per second / cycle
```
CPU     IPS            IPC            CPI
#00     0.000579/s     0.059728/c    16.742698/i
#01     0.000334/s     0.150569/c     6.641471/i
#02     0.000598/s     0.161326/c     6.198641/i
#03     0.000294/s     0.233535/c     4.282013/i
#04     0.000240/s     0.042931/c    23.293141/i
#05     0.000284/s     0.158661/c     6.302765/i
#06     0.000128/s     0.128031/c     7.810631/i
#07     0.000088/s     0.150406/c     6.648674/i
```

 * Use option '-s' to show Processor information (BSP)
![alt text](http://blog.cyring.free.fr/images/CoreFreq_SysInfo.png "CoreFreq System Info")

## ArchLinux
[corefreq-git](https://aur.archlinux.org/packages/corefreq-git) can be installed from the Arch User Repository.

## Debian
 * Install the prerequisite packages.
```
apt-get install dkms git
```
 * As a user, clone and build CoreFreq.
```
git clone https://github.com/cyring/CoreFreq.git
cd CoreFreq
make
```
 * As root, change to the build directory then start the module followed by the daemon.
```
insmod corefreqk.ko
./corefreqd
```
 * As a user, start the client.
```
./corefreq-cli
```

## Algorithm
![alt text](http://blog.cyring.free.fr/images/CoreFreq-algorithm.png "CoreFreq algorithm")

# About
[CyrIng](https://github.com/cyring)
 -------