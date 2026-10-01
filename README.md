# A Chinese Localisation

Chinese translations for the companion mods MandateOverhaul,
HistoryCorrections and BaseModifiersTweaks. 139 keys.
Target EU4 version 1.37.5.0.

The language lives in its own mod so it can be toggled without touching gameplay,
and so the gameplay mods stay plain ASCII. Any key this mod does not define falls
back to the English in the mod that owns it, which is a graceful failure.

## Why this is not just a UTF-8 file

**EU4's engine does not decode multi-byte UTF-8.** Writing CJK normally into a
`.yml` renders one `?` per character in game. The font is fine; the text encoding
is the problem.

The workaround, reverse-engineered from the Chinese Language Mod for 1.37, is to
write each CJK character as a **three-byte group** — a marker byte, then the
codepoint's low and high bytes — with each of those three bytes written as its
CP-1252 character and the result UTF-8 encoded. ASCII and Latin-1 pass through
literally.

Bytes that would break EU4's file syntax are shifted, and the marker records
which were:

| Marker | Meaning |
| --- | --- |
| `0x10` | no shift |
| `0x11` | low byte stored `+14` |
| `0x12` | high byte stored `-9` |
| `0x13` | both |

The low byte is shifted when it is one of
`00 0A 0D 20 22 23 24 2F 3A 3B 3D 40 5B 5C 5D 5F 7B 7D 7E 80 A3 A4 A7 BD`; the
high byte when it is one of `20 22 5B 5C 5D 5F 7B 7D 7E 80`. Those are exactly the
characters that would otherwise terminate a string or be read as markup — quote,
hash, `$`, colon, braces, backslash — plus EU4's own formatting codes, `A7` being
`§`, the colour marker.

A Python codec implementing this was verified by round-tripping all 146 of the
reference mod's localisation files byte-identically, and by independently
reproducing their bytes for known phrases. It is not included here; the table
above is enough to reimplement it.

## Two things that make it work

**`localisation/replace/`.** That is the documented way to override a key held by
vanilla *or by another mod*, and it sidesteps filename collisions entirely.

**The name.** EU4 ignores the launcher's load order and resolves conflicts by mod
*name*, with the earlier-sorting name winning. "A Chinese Localisation" sorts ahead
of the gameplay mods on purpose. A mod that loads last loses every conflict.

## Install

Copy this folder into

    Documents/Paradox Interactive/Europa Universalis IV/mod/

and create a sibling `A Chinese Localisation.mod` next to it containing the same
lines as `descriptor.mod` plus an absolute path, forward slashes:

    path="C:/Users/<you>/Documents/Paradox Interactive/Europa Universalis IV/mod/A Chinese Localisation"

Localisation files are UTF-8 **with BOM**. A single-byte encoding makes EU4 fail
to read the file and display the raw key instead.

## Note

No files from the Chinese Language Mod are redistributed here; only its encoding
scheme was studied, and it is described above. This is an unofficial personal mod,
not affiliated with or endorsed by Paradox Interactive.
