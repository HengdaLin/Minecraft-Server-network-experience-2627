# Doel
Omdat de Smith proxmox node een oude server CPU met een slechte single core score heeft hebben we beslist om een nieuwe server met een desktop CPU specifiek een i7-9700KF. Deze CPU heeft een beter single core score, waardoor een minecraft server betere performance heeft.


# Problemen
Bij installatie van Proxmox treed er een probleem op waardoor proxmox installer volledig vastloopt bij het initializeren van de ramfs. Specifiek bij de lijn `Loading init ramfs ...`.

De betekenis hierachter is dat linux de "EFI handoff" niet correct uitvoert werd, waardoor het volledige systeem vastloopt.


# Vaststelling
- Het probleem ligt bij een compatibiliteit van de cpu virtulization.
- BIOS versie: `V1.0`
- Laatste BIOS versie: `7B24v1D`


# Oplossing
## geprobeerde oplossing
Eerst ben ik gestart bij de proxmox forums:

```text
https://forum.proxmox.com/threads/loading-initial-ramdisk-freeze.170163/post-794297

What you are seeing is a very early hang during initrd load on the Proxmox installer, and since it happens on both Proxmox 9.x and 8.x across multiple identical Supermicro H13 systems, it strongly suggests a firmware or compatibility issue rather than a bad ISO or USB stick. With H13 boards (which are AMD EPYC 9004 Genoa/Bergamo platforms), there are known quirks with certain Linux installers due to kernel/firmware interactions, especially around early EFI handoff. A few things you should try: first, when booting the installer, edit the GRUB line and append nomodeset noapic to the kernel options. Sometimes early framebuffer or ACPI/APIC initialization hangs on these boards. If that doesn’t help, try also acpi=off or pci=nomsi as tests. Second, check in the BIOS under Boot → CSM and make sure Compatibility Support Module is either disabled (pure UEFI) or, if already disabled, try enabling it. These platforms often require toggling CSM for certain OS bootloaders to behave properly. Third, confirm that SecureBoot is really disabled not only globally, but also that the “DB/KEK keys” are cleared in the BIOS; leaving factory keys loaded can cause issues even with SecureBoot disabled. Fourth, try booting the installer in “debug” mode (which you already did) and add break=init at the end of the kernel command line, which should drop you into an early initramfs shell and allow you to see whether the storage medium is visible at all. If it hangs even before that, it means the EFI stub is never handing off to the kernel properly.
Since you are using the very latest H13 boards, it’s also worth testing with the Proxmox kernel 6.8 installer ISO (instead of the default 6.5), which is available on the Proxmox download page. The 6.8 kernel has significantly better support for AMD Genoa/Bergamo platforms. If that still fails, try booting a stock Debian 12 Bookworm live ISO (since Proxmox is based on it). If Debian boots and Proxmox doesn’t, then it’s a kernel/initrd packaging issue in the Proxmox installer, and you can install Debian first and then add Proxmox packages manually.
Finally, make sure in BIOS that “Above 4G Decoding” and “SR-IOV” are enabled, and that “IOMMU” is set to Auto or Enabled. These options can affect early init handoff. On some Supermicro H13 systems, disabling “Redfish BMC Video” or switching video output to onboard VGA instead of AST2500/2600 has also resolved initrd hangs.
```

Daarbij heb ik de stappen afgelopen tot ik aan de instelling `SR-IOV` terrecht kwam. De equivalent voor het msi h370m bazooka moet onder `Settings > Advanced > PCIe/PCI Sub-system Settings` zitten, dit was niet zo.

Hierdoor ben ik beginnen kijken wat de laatste BIOS versie was van dit moederbord. Het bleek dus dat het moederbord nog nooit geupdate is geweest.

## Effectieve oplossing
Na het updaten van de BIOS naar versie `7B24v1D` de laatste versie van 9 augustus 2024 heeft het probleem zich volledig zelf opgelost. Na controle van de BIOS zijn nu meerder instellingen te zien bij CPU features en PCI Sub-system settings rond virtualization.


# Extra info
De Proxmox node heeft 2 ssd's in een Raid1 configuratie voor reliability.
Deze node zit in het VLAN 21 en zou berijkbaar moeten zijn via `http://10.21.1.24:8006`.

De Node heeft de hostnaam `sati`.
