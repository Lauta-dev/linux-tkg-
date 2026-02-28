# linux-tkg — Intel Celeron N2807 (Silvermont)

Kernel Linux personalizado compilado con [linux-tkg](https://github.com/Frogging-Family/linux-tkg)
de [Frogging-Family](https://github.com/Frogging-Family), optimizado específicamente para hardware
Intel Atom/Silvermont de bajo consumo.

---

## Hardware objetivo

| Propiedad | Valor |
|---|---|
| **CPU** | Intel Celeron N2807 @ 1.58GHz (max 2.16GHz) |
| **Arquitectura** | x86_64 |
| **Núcleos / Hilos** | 2 núcleos / 2 hilos (sin HT) |
| **Micro-arquitectura** | Silvermont (Bay Trail) |
| **Familia / Modelo** | 6 / 55 stepping 8 |
| **L1d / L1i / L2** | 48 KiB / 64 KiB / 1 MiB |
| **Virtualización** | VT-x |
| **WiFi** | Realtek RTL8723BE (PCIe) |
| **Distro** | Arch Linux (EndeavourOS) |

### Instrucciones SIMD disponibles
`SSE` `SSE2` `SSE4.1` `SSE4.2` `SSSE3` `PCLMULQDQ` `MOVBE` `POPCNT` `RDRAND`

---

## Configuración de compilación

| Opción | Valor |
|---|---|
| **Versión del kernel** | `6.12-latest` |
| **CPU scheduler** | `pds` (Project C's Priority and Deadline Skiplist) |
| **Compiler** | LLVM / Clang |
| **LTO mode** | ThinLTO |
| **Optimización CPU** | `silvermont` (`-march=silvermont`) |
| **Kernel on diet** | `true` — subset reducido de módulos |
| **modprobed-db** | `true` — solo módulos usados por este hardware |
| **Debug symbols** | deshabilitado (`_debugdisable=true`, `_STRIP=true`) |
| **Timer frequency** | 1000 Hz |
| **CPU governor** | `performance` |
| **TCP congestion** | cubic (default) |
| **Preemption** | Preemptible Kernel (Low-Latency Desktop) |
| **Fsync** | habilitado |
| **Clear Linux patches** | habilitado |
| **Custom cmdline** | `intel_pstate=passive split_lock_detect=off mitigations=off nowatchdog` |

---

## Módulos incluidos (modprobed-db)

Módulos cargados por este hardware específico:

| Categoría | Módulos |
|---|---|
| **Input / Teclado** | `usbhid` `hid_generic` `psmouse` |
| **WiFi** | `rtl8723be` `cfg80211` `mac80211` `rfkill` `btcoexist` |
| **Bluetooth** | `bluetooth` `btusb` `btrtl` |
| **Filesystems** | `xfs` `vfat` `fat` `nls_utf8` |
| **Video** | incluidos en config base |

### Config fragments (`essential.myfrag`)

Módulos forzados como built-in porque están en el kernel oficial de Arch
como `=y` pero no aparecen en modprobed-db:

```
CONFIG_SERIO_I8042=y
CONFIG_KEYBOARD_ATKBD=y
CONFIG_EXT4_FS=y
CONFIG_BTRFS_FS=y
```

---

## Mitigaciones de seguridad

El kernel se compila con `mitigations=off` en la cmdline para maximizar
el rendimiento en este hardware de bajo consumo. Las vulnerabilidades
conocidas para el N2807 son:

| Vulnerabilidad | Estado |
|---|---|
| Meltdown | Mitigado (PTI) — está en cmdline off |
| Spectre v1 | Mitigado |
| Spectre v2 | Mitigado (Retpolines + IBRS) |
| MDS | Mitigado (Clear CPU buffers) |
| La mayoría restantes | Not affected |

> ⚠️ Si usás este equipo en entornos de producción o multiusuario,
> considerá quitar `mitigations=off` de la cmdline.

---

## Build automático (GitHub Actions)

El kernel se compila automáticamente via GitHub Actions en cada push
usando un contenedor `archlinux:latest`.

```
.github/workflows/arch-builder.yml
```

Los artefactos y releases se publican automáticamente con el tag
`v{version}-{scheduler}`, por ejemplo: `v6.12.74-pds`.

---

## Instalación

Descargá los paquetes del [último release](../../releases/latest):

```bash
sudo pacman -U linux*pds*.pkg.tar.zst linux*pds*-headers*.pkg.tar.zst
```

Regenerá el initramfs si es necesario:

```bash
sudo mkinitcpio -P
```

---

## Basado en

- **[linux-tkg](https://github.com/Frogging-Family/linux-tkg)** por [Frogging-Family](https://github.com/Frogging-Family)
- Patches: PDS, BMQ, BORE, glitched-eevdf, Clear Linux, fsync, OpenRGB
- Kernel upstream: [gregkh/linux](https://github.com/gregkh/linux)
