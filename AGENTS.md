# AGENTS.md

ZMK config for a Toucan2 split keyboard (36 keys). The keymap is in `config/toucan.keymap`, combos are in `config/combos.dtsi`, and key-position names are in `config/toucan_keys.h`.

## Layer design: one keymap, two OS layer sets

I use this keyboard with both macOS and Linux. Instead of keeping separate keymaps, the keymap holds **a full set of layers for each OS**, and I switch between the sets when I change machines:

| macOS | Linux | Role |
|-------|-------|------|
| 0 `mac` (default) | 6 `lnx` | Base (Colemak-DH + home-row mods) |
| 1 `Nav` | 7 `Nav` | Navigation / editing |
| 2 `Sym` | 8 `Sym` | Symbols |
| 3 `Num` | 9 `Num` | Numbers / F-keys (tri-layer: Nav + Sym) |
| 4 `MOU` | 10 `MOU` | Mouse buttons |
| 5 `RS Layer` | — | Shared: reset, bootloader, BT profiles, OS switch |
| 11 `Media` | (same) | Shared: one media layer for both OSes, held from the right outer thumb (35) |

- The Linux set starts at layer 6 and **mirrors the macOS set position for position**. It differs only where the OS needs something different, such as Cmd vs Ctrl shortcuts, Cmd-Tab vs Alt-Tab, word navigation, zoom, and how umlauts are typed (Option-U on macOS, Unicode Ctrl+Shift+U on Linux).
- Thumb keys on the Linux layers point to the Linux layer numbers (7/8/9/10). Their layers sit above layer 6 and override it.
- The RS layer (5) is shared by both OSes and is reached from the Num layer's `&to 5` thumb key.
- The Media layer (11) is **one layer shared by both OSes**, so don't make a Linux copy of it. It sits above every other layer, and every unused key is `&none`, so nothing falls through to the layers below. `extra_3` stays last as the reserved slot for ZMK Studio.

## Shift-reversible keys

This keymap makes **heavy use of shift-reversible keys**: one key that does an action in one direction on tap and in the opposite direction with **Shift held**. Each one is a `zmk,behavior-mod-morph` on `MOD_LSFT|MOD_RSFT`. The unshifted binding is the forward/"in" direction, and the shifted binding is backward/"out". Shift is usually held on the MAGIC thumb key (33) while the Nav layer is active.

| Key | Tap | Shift + tap | macOS / Linux behavior |
|-----|-----|-------------|------------------------|
| Zoom ± (pos 5, Nav) | Zoom in | Zoom out | `zoom_rev` / `zoom_rev_lin` || Select word (pos 27, Nav) | Select word → | Select word ← | `sel_word` / `sel_word_lin` |
| Jump word (pos 28, Nav) | Jump word → | Jump word ← | `jump_word` / `jump_word_lin` |

When adding paired directional actions (in/out, forward/back, next/prev), prefer **one shift-reversible key** over two separate keys. Define a macOS and a Linux variant, and place them at the same position in both layer sets.

## Rules for changes

- **Keep the two sets in sync.** A layout change to a macOS layer should be made at the same position on its Linux twin (and the other way round), unless the difference is OS-specific on purpose.
- A Linux-only change should not leak into the macOS layers, and the reverse holds too.
- Conditional (tri-)layers, combo `layers` lists, and any hardcoded layer indices must cover **both** sets (e.g. `1 2 → 3` and `7 8 → 9`).
- Adding or removing a layer shifts the indices. Re-check every `&mo`, `&lt`, `&to`, `&tog`, conditional layer, and combo layer list afterwards.
- Changes to the keymap need **both halves** reflashed.
