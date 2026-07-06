# LIADAM

External read-only ESP overlay for **[MECCHA CHAMELEON](https://store.steampowered.com/)** (Unreal Engine 5.6).

Built for **research and educational purposes** into UE5 memory layout, offset resolution, world-to-screen projection, and DirectX 11 transparent overlay rendering.

> **Source code is intentionally kept private.** See [Why no source](#why-no-source).

---

## What it is

LIADAM runs as a **separate process** alongside the game. It attaches read-only to `PenguinHotel-Win64-Shipping.exe`, walks the Unreal Engine object model to resolve offsets dynamically each run, and draws an ESP on top of a transparent Direct3D 11 layer.

- **External** — no injection, no code lives inside the game.
- **Read-only** — every memory access is `ReadProcessMemory`. Nothing is written back to the game.
- **Self-contained** — one `.exe`, no dependencies, no runtimes to install.
- **Standard Windows APIs** — no drivers, no kernel access, no hypervisor tricks.

## Features

| Category           | Features                                                                                     |
| ------------------ | -------------------------------------------------------------------------------------------- |
| Visuals            | Boxes (filled/outline), dots, tracers with selectable origin, off-screen arrows, names, distance |
| Aimbot (mouse)     | FOV circle, adjustable smoothing/speed, target Z offset, closest-to-crosshair or nearest-in-world, target stickiness, optional humanize |
| Theme              | Live accent-color picker, ESP color pickers, quick presets                                    |
| Misc               | Streamproof (hides overlay from OBS/Discord capture), debug counters, config auto-save       |
| Loader             | Custom LIADAM loader with fake login, product select, animated loading, live engine status   |

## Requirements

- Windows 10 build 2004+ or Windows 11 (needed for the streamproof hide-from-capture flag)
- Administrator privileges (memory reads across processes require it)
- MECCHA CHAMELEON running before you attach

There are **no runtime downloads** — the exe is fully self-contained. No .NET, no Visual C++ Redistributable, no external DLLs.

## Install & run

1. Download `meccha_esp.exe` from the [latest release](../../releases/latest).
2. Right-click → **Run as administrator**.
3. The LIADAM loader appears. Any username / password combination works.
4. Select **MECCHA CHAMELEON** on the product screen → click **LAUNCH**.
5. If the game isn't running yet the loader will sit on **WAITING FOR GAME** and continue automatically as soon as the game process appears.

## Controls

| Key       | Action                       |
| --------- | ---------------------------- |
| **INSERT**| Toggle the in-game menu      |
| **F1**    | Toggle ESP on/off            |
| **F2**    | Toggle aimbot on/off         |
| **END**   | Exit LIADAM                  |

## Configuration

Every setting persists to `liadam.cfg` next to the exe. It's written automatically on exit and can be saved manually from **Misc → Save config**. Deleting the file restores defaults.

## Why no source

The source code is **intentionally not public**. Two reasons:

1. **Skid protection.** Publishing the source is an open invitation for people who don't understand the code to compile and redistribute knock-offs, which floods the game with more cheaters. That's the opposite of what this project is for.
2. **Game health.** Fewer working forks in circulation means a better experience for real players.

If you're doing similar research and want to talk about specific techniques in general terms — pattern scanning, UStruct child-property walks, FName pool layout detection, W2S math — DM me. I'm happy to discuss.

## What this is NOT

To be very clear about the intent, LIADAM is deliberately **not** any of the following:

- **Not obfuscated.** The binary is a plain Visual Studio release build. No packing, no VM protection, no anti-debug, no multi-layer deob. If you're worried it's malware, load it in Ghidra / Binary Ninja and read it — I'd rather you verify than trust me. The trade-off is that it's also trivially fingerprintable by anti-cheat, which is fine: an easy-to-detect research overlay is exactly the point.
- **Not driver-based.** Mouse aiming uses `SendInput` — a normal Windows user-mode API call. It is **not** a signed HID-emulation driver (Interception / Logitech LGHUB abuse / MAKCU-style hardware) and it is **trivially detectable** by any anti-cheat that cares to look. Also intentional.
- **No token grabbing, no info stealing.** No Discord token exfil, no browser cookie / password stealing, no clipboard sniffing, no keylogging, no screenshotting, no persistence, no autorun, no telemetry. LIADAM never talks to the network.
- **No self-modifying code, no runtime unpacking, no hijacking of other processes.**

If I wanted to build something harmful or hard to remove I would have built something different. This is a research overlay. It behaves like one.

## DMA / hardware-mouse variant

There is a second variant of LIADAM I built as a research exercise: memory reads via **DMA** (PCILeech + MemProcFS `VMMDLL_MemReadEx`) and mouse output routed through a **MAKCU** hardware mouse over USB serial.

That variant is **not published**. If this repo gets **5 stars**, I'll upload it too. Honest disclosure — porting the external build to DMA was mostly a mechanical exercise: the memory read primitive is the only real difference (`ReadProcessMemory` → `VMMDLL_MemReadEx`), and mouse output is `SendInput` → serial write. Everything above those two calls (offset resolution, world-to-screen, rendering, menu) is byte-identical between the two builds.

## Legal / Takedown

This project exists strictly for **personal research and educational purposes** into Unreal Engine 5 internals. It is not sold, not distributed for commercial gain, and is not affiliated with the developers of MECCHA CHAMELEON.

**If you are the developer or rights holder of MECCHA CHAMELEON** and would like this repository and its releases taken down, contact me on Discord at **`infernoytv`** and I will remove everything. No debate, no delay.

## Contact

- **Discord:** `infernoytv`

## Disclaimer

Use is at your own risk. The author accepts no responsibility for account bans, hardware bans, disciplinary action, or any other consequences resulting from use of this software. If a game you play uses aggressive anti-cheat, do not use this software with it.
