# TVampireD

**A gameplay modification for *Crusader Kings III* that adds playable vampirism, modelled on the fiction of *The Vampire Diaries*.**

[Available on the Steam Workshop →](https://steamcommunity.com/sharedfiles/filedetails/?id=3542088672)

---

## What this is, for anyone who hasn't played the game

*Crusader Kings III* is a medieval grand-strategy game by Paradox Interactive. You don't play a country — you play a **person**: a ruler with a personality, a family, allies and rivals, and a normal human lifespan. When that character dies, you continue as their heir. The game is a character-driven simulation of dynastic politics played out across roughly seven centuries of history.

The game ships with a scripting layer that lets players add their own content — new character traits, decisions, events and rules — without access to the game's source code. Content built that way is called a **mod**.

TVampireD is one of those. It adds vampirism as a fully playable condition: a character can be turned into a vampire, stops ageing, gains supernatural abilities, and can turn others — founding a bloodline that outlives everyone around it.

That last part matters more than it sounds. The entire game is built on the assumption that your character dies every few decades and you inherit the consequences. **Removing death changes the shape of play**: instead of a relay of heirs, you steer one character across centuries, accumulating the people you have turned, fed on, healed, or bent to your will. The mod is designed around that shift rather than treating immortality as a simple bonus.

---

## What it adds

**Three tiers of vampire**, each a distinct character trait with its own abilities and restrictions:

- **Vampire** — the base condition, gained by being turned by another vampire. Levels up along an experience track (*Vampiric Mastery*) as the character uses their abilities.
- **Original Vampire** — the progenitor tier, reached through a dedicated ritual decision rather than by being turned.
- **Original Hybrid** — the vampire/werewolf crossbreed tier.

**Compulsion** — mind control exerted on another character, which forces loyalty and overrides their own preferences. Originals can compel other vampires; ordinary vampires cannot.

**Feeding** — vampires must feed on the living. Includes feeding on members of your court and draining prisoners to death, with a custom death reason recorded in the victim's history and a lingering weakness on survivors.

**Turning and sire lines** — vampires can offer the gift, and mortals can request it and bring leverage to the negotiation. Every turned character permanently records who sired them, building a traceable bloodline.

**Blood healing** — a vampire's blood cures the sick and injured, and the vampire chooses what to demand in return.

**Vampire houses** — invite outsiders into your house, or adopt them outright with full inheritance rights, allowing a supernatural dynasty assembled by choice rather than by birth.

**Vampire men-at-arms** — a custom military regiment recruited from the turned.

**Visual age maintenance** — an immortal character's portrait is frozen at the age they were turned, and kept there as the centuries pass.

---

## How it's built

Paradox games are not modded in a general-purpose programming language. They use a bespoke **declarative scripting language**: you describe game objects as nested key–value blocks and the engine's runtime evaluates them. There are no return values, no conventional loops, and no debugger. Logic is expressed through three primitives:

- **Triggers** — conditions that evaluate to true or false
- **Effects** — changes to game state
- **Scopes** — which character or object the current block is talking about

Practical consequences that shape the whole codebase:

- **Everything is data.** A trait, an interaction, and an event are all declarations the engine reads at load time. Adding behaviour means adding a definition the engine already knows how to interpret, not calling into it.
- **Persistent state lives on characters.** There is no database. Facts like *who sired this vampire* or *who compelled them* are stored as flags and variables attached to individual characters, and read back by name elsewhere.
- **Text is fully separated from logic.** All player-facing writing lives in localization files keyed by identifier, which is what makes multi-language support possible — this mod ships **English and Russian**.
- **Verification is empirical.** The scripting language is under-documented, so correctness is established by reading the game's own script files, dumping the engine's runtime documentation, and checking the error log the game emits on each launch.

```
TVampireD/
├── common/
│   ├── traits/                  the vampire trait tiers and their effects
│   ├── character_interactions/  the player-facing actions (feed, compel, turn, heal)
│   ├── decisions/               the Original Vampire ritual
│   ├── scripted_effects/        reusable state changes shared across systems
│   ├── scripted_triggers/       reusable conditions shared across systems
│   ├── on_action/               hooks into engine lifecycle events
│   ├── modifiers/               timed and permanent character modifiers
│   ├── opinion_modifiers/       how characters regard each other afterwards
│   ├── men_at_arms_types/       the vampire regiment
│   ├── deathreasons/            custom death entry for drained victims
│   └── trigger_localization/    readable explanations of failed conditions
├── events/                      the narrative events and their branching options
├── gfx/                         trait icons and interaction art
├── localization/                all player-facing text (English, Russian)
├── descriptor.mod               mod metadata read by the launcher
└── thumbnail.png
```

---

## Installing

You need a copy of *Crusader Kings III* on PC.

**From the Steam Workshop (recommended)** — [subscribe here](https://steamcommunity.com/sharedfiles/filedetails/?id=3542088672). Steam downloads and updates it automatically; enable it in the game launcher's mod list.

**From this repository** — copy the `TVampireD` folder into your CK3 mod directory:

```
Documents/Paradox Interactive/Crusader Kings III/mod/
```

Then edit `descriptor.mod` so the `path` line points at where you actually put it, and enable the mod in the launcher.

---

## Status

This repository is a **public snapshot of the 1.0.0 release**, targeting CK3 version 1.16. Development has continued well beyond it — the live version of the mod is the one on the Steam Workshop.

Ongoing work is focused on giving each system a genuine mechanical layer rather than passive stat bonuses: compulsion with distinct kinds and consequences, blood debts that can be called in and refused, and authored narrative events for acts that currently resolve silently.
