[https://wiki.osdev.org/Bare_Bones](https://wiki.osdev.org/Bare_Bones)

## boot.s
You can then assemble boot.s using: 
`i686-elf-as boot.s -o boot.o`


## kernel.c
Compile using: 
`i686-elf-gcc -c kernel.c -o kernel.o -std=gnu99 -ffreestanding -O2 -Wall -Wextra`


## linker.ld
You can then link your kernel using: 
`i686-elf-gcc -T linker.ld -o myos.bin -ffreestanding -O2 -nostdlib boot.o kernel.o -lgcc`



## ...

### Verifying Multiboot
If you have GRUB installed, you can check whether a file has a valid Multiboot version 1 header, which is the case for your kernel. It's important that the Multiboot header is within the first 8 KiB of the actual program file at 4 byte alignment. This can potentially break later if you make a mistake in the boot assembly, the linker script, or anything else that might go wrong. If the header isn't valid, GRUB will give an error that it can't find a Multiboot header when you try to boot it. This code fragment will help you diagnose such cases: 
`grub2-file --is-x86-multiboot myos.bin`

### Building a bootable cdrom image
You can now create a bootable image of your operating system by typing these commands: 
```
mkdir -p isodir/boot/grub
cp myos.bin isodir/boot/myos.bin
cp grub.cfg isodir/boot/grub/grub.cfg
grub2-mkrescue -o myos.iso isodir
```

### Testing your operating system (QEMU)
`qemu-system-i386 -cdrom myos.iso`
Additionally, QEMU supports booting multiboot kernels directly without bootable medium: 
`qemu-system-i386 -kernel myos.bin`