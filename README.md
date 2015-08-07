# DPCM-Test
Famicom DPCM Channel Test Program

Tests loading and running a DMC sample on Famicom/NES.
Assembles in ASM6.
The batch file "build.bat" may be used in Windows if ASM6 is in path.

Info from Nesdev forums:

Playing a sample requires setting the frequency ($4010),
the sample address ($4012) and length ($4013) of the sample,
then enabling the channel with $4015. Optionally you also set the starting
output level of the sample ($4011).

DPCM samples must begin in the memory range $C000-FFFF at an address set by
register $4012 (address = %11AAAAAA.AA000000).
The length of the sample in bytes is set by
register $4013 (length = %LLLL.LLLL0001)."

In other words when the DMC sample is restarted, the address counter is set to
register $4012 * $40 + $C000 and the bytes counter is set to
register $4013 * $10 + 1." Specify the length of the sample in 16 byte
increments by writing a value to $4013. Writing $01 means 17 bytes length
and $02 means 33 bytes.
Maximum sample length is 4096 byte (but you need a mapper that can bankswitch
$C000-$DFFF, such as MMC3 or FME-7 if you have many that big samples).
If you have a lot of samples and little else that needs to be accessed while a
sample is playing, you could try upside-down UNROM (#180) or MMC1.).

The frequency state ($4010) applies immediately.
The sample address and length ($4012 and $4013) apply at the next sample
start or loop.
To play a new sample, you use $4015. Typically you want to write to $4015
with the DMC bit clear first to stop any playing sample, then write a second
time with the bit set to start the new sample.

Register $4011 - Changing the starting level of a sample
The sample format only lets you specify each point as "go up" or "go down",
not at exact value. As the sample is played, if the level is too low to
go down further, or too high to go up further, it will stay where
it is. By using different starting levels before playing the sample, you might
be able to get different variations of sound from the same sample.

In Ikinari Musician, a sample with a general downward trend is used.
When the starting level is set to higher values, this causes the sample to be
audible slightly longer before it bottoms out and effectively becomes silent.
Using the different starting levels produces different variations of a click
sound from the same sample.

Making sounds directly
APU says "The $4011 register can be used to play PCM samples directly by
setting the counter value at a high frequency. Because this requires intensive
use of the CPU, when used in games all other gameplay is usually halted to
facilitate this."

That is, if you are able to load the "direct load" register at a fast enough
rate, you can make sounds beyond the restrictions of the "go up" / "go down"
sample format. You are in charge of the exact value of each point, but you
have to spend a lot of time updating the register.
