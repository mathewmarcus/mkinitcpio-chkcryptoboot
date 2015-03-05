# mkinitcpio-chkcryptoboot

## Introduction
Archlinux mkinitcpio hook to check if the bootloader was modified and warn the user to not enter his root device password. Based on chkboot.

This effectively makes the bootloader code tamper evident, while allowing the root luks device to be more tamper resistant, by demonstrating to the user that the bootloader was modified. This can only be used with a bootloader that knows how to unlock an encrypted boot partition, like GRUB with it's CRYPTODISK feature.

It does support both MBR bootloaders and UEFI bootloaders. In the case of UEFI, the ESP partition must be left unencrypted, and a separate encrypted boot partition must be created.

## Attacks
Your BIOS must be secured with a password, if it's possible. And you should disallow booting from other medias. Except, in the case of booting from an external device to try to thwart an evil maid attack. In either case, if your BIOS is modified, It might trick you into booting from a compromised bootloader. This bootloader can try to change your boot partition, after it's decrypted, to make the initramfs hook not to run, try to boot another kernel, etc. In this case, there will be no warn and you will probably end up typing your root device password. If you use chkboot in conjunction with this hook it will probably detect the changes and warn you, after the fact. But, since the bootloader configuration, kernel and initramfs are kept in the encrypted boot partition, this kind of attack is much harder to accomplish, because the attacker does only have minimal information about the bootloader being used. It does not have information about bootloader menu entries, kernels being used, etc. The compromised bootloader would need to account for all these things.

For this reason, the user can create a customised kernel cmdline parameter, that can be checked upon boot, to try to detect if you booted from a compromised bootloader or if your boot partition was bypassed. Since this is entirely up to the user, and, to make the parameter user friendly, it must have the name=value form. Much care must be taken with the name, to avoid colliding with common parameters. I suggest using two hashes, one for the name and other for the value. After root gets mounted, it can try to detect if the cmdline has the name=value, and warn the user. Just keep in mind the cmdline has a limit in size, including spaces, between 256 and 4096, depending on the architeture. All this adds complexity into the mix, hopefully making a compromised bootloader a very hard task to accomplish.
