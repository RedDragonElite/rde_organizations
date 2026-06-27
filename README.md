# 🏢 rde_organizations

🔥 ULTIMATE MULTI-ORG SYSTEM V2.0.3 — Unlimited Ranks, 27 Permission Flags, Per-Member Overrides, Realtime Admin Panel, RDE Ecosystem Bridge! 🐉

[![Version](https://img.shields.io/badge/version-2.0.3-red?style=for-the-badge)](https://github.com/RedDragonElite/rde_organizations)
[![License](https://img.shields.io/badge/license-RDE%20Black%20Flag%20v6.66-black?style=for-the-badge)](https://github.com/RedDragonElite/rde_organizations/blob/main/LICENSE)
[![FiveM](https://img.shields.io/badge/FiveM-Compatible-blue?style=for-the-badge)](https://fivem.net)
[![ox_core](https://img.shields.io/badge/Framework-ox__core-blue?style=for-the-badge)](https://github.com/overextended/ox_core)
[![Price](https://img.shields.io/badge/price-FREE%20FOREVER-brightgreen?style=for-the-badge)](https://github.com/RedDragonElite/rde_organizations)
[![RDE Ecosystem](https://img.shields.io/badge/RDE-ECOSYSTEM-f59e0b?style=for-the-badge)](https://github.com/RedDragonElite)
[![rde_crew](https://img.shields.io/badge/integrates-rde__crew-orange?style=for-the-badge)](https://github.com/RedDragonElite/rde_crew)

**One system for every faction on your server. Legal businesses, criminal syndicates, street gangs, motorcycle clubs, corporations, government departments — each with unlimited custom ranks, 27 granular permission flags, per-member overrides, a full bank with audit log, a configurable boss terminal, and a realtime admin panel. Free. No Tebex. No limits.**

**Pair with [`rde_crew`](https://github.com/RedDragonElite/rde_crew) and gang-type orgs automatically become territory-holding crews — the OrgBridge makes both systems feel like one.**

> Built on ox_core · ox_lib · ox_target · oxmysql
>
> Built by Red Dragon Elite | SerpentsByte

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Why RDE Organizations?](#-why-rde-organizations)
- [Features](#-features)
- [Org Types](#-org-types)
- [Permission System](#-permission-system)
- [OrgBridge — rde_crew Territory Integration](#-orgbridge--rde_crew-territory-integration)
- [Dependencies](#-dependencies)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Usage](#-usage)
- [Architecture](#-architecture)
- [Security](#-security)
- [Commands](#-commands)
- [Database](#-database)
- [Performance](#-performance)
- [Troubleshooting](#-troubleshooting)
- [Changelog](#-changelog)
- [License](#-license)

---

## 🎯 Overview

RDE Organizations is a multi-organization management system built exclusively on ox_core. It handles the human side of every faction structure on your server — who belongs to what, who outranks whom, who is allowed to do what, and who gets paid how much.

A player can be in up to 5 organizations simultaneously. Each organization has unlimited custom ranks, and each rank has up to 27 granular permission flags that govern exactly what that rank can do. Individual members can receive custom permission overrides on top of their rank — granting or restricting access without touching the rank itself.

Every change publishes immediately to a prefixed statebag. No polling. No callback chains. Any resource that knows the player's identifier can read their org memberships, rank, permissions, and salary from `GlobalState['rde_orgs_<identifier>']` without needing a callback.

**Pair with [`rde_crew`](https://github.com/RedDragonElite/rde_crew):** gang-type organizations (gang, MC, criminal) automatically link to the territory system via OrgBridge. The org panel becomes the crew management interface — rde_crew handles the map, the graffiti, and the wars.

---

## 🔥 Why RDE Organizations?

| Typical Job/Gang Scripts | ✅ RDE Organizations |
|---|---|
| Fixed 4 grades: Rookie/Officer/Boss/Owner | **Unlimited custom ranks** — create as many as the org needs |
| Permissions hardcoded per grade | **27 permission flags** individually toggled per rank |
| Permissions apply equally to all members of a rank | **Per-member overrides** — delegate or restrict without changing the rank |
| One org type: "job" with boss flag | **8 org types** with distinct default templates and UI presentation |
| Bank is a number in a config, touched only by admins | **Full bank system**: deposit, withdraw, manual salary, auto-payout, full audit log |
| Org management: `/setjob id rank` | **Realtime NUI panel** — invite, promote, demote, kick, salary, custom perms, all in-game |
| Admin panel: the database | **In-game admin hub** — create, edit, delete orgs, manage members, set bank balance, wipe all |
| One player, one job | **Up to 5 simultaneous org memberships** |
| Gang scripts don't know jobs exist | **OrgBridge**: gang orgs ARE territory crews in rde_crew, automatically |
| ESX / QBCore only | ox_core only — the future, not the past |
| Paid | 100% free — RDE Black Flag |

---

## ✨ Features

### 🏢 Organization Lifecycle

- Create organizations with a name, label, type, description, color, and icon
- 8 distinct org types, each with its own default rank template and UI color
- Leader can rename the org label, update description, color, and icon
- Full org dissolution (cascade-deletes all members, ranks, and transactions)
- Transfer leadership to any existing member
- Configurable maximum members per org (0 = unlimited) and maximum orgs per player

### 🏅 Rank System

- **Unlimited custom ranks** per org (configurable max, default 20)
- Each rank has: name, label, display order, color, icon, permission set, default salary
- Leader rank is locked (cannot be deleted, cannot have `is_leader` removed)
- New members join on the lowest-order non-leader rank automatically
- Rank templates per org type applied at creation — a Gang gets Lookout/Soldier/Captain/Boss, an MC gets Prospect/Hang-Around/Member/Officer/VP/President. All fully editable after creation
- Leader title is separately renamable (call your top rank "Don", "President", "CEO" — your choice)

### 🔐 Permission System — 27 Flags

Permissions are grouped into six categories and individually toggled per rank:

**Members:** invite · kick · promote · demote · set_salary · view_members

**Bank:** bank_deposit · bank_withdraw · bank_view · send_salary

**Ranks:** manage_ranks · assign_ranks

**Settings:** edit_org · manage_blip · manage_terminal

**High Clearance:** transfer_leader · disband

**Access Control:** access_level_1 · access_level_2 · access_level_3 · access_level_4 · use_vehicles · use_armory · use_stash · dispatch_access · intel_access

**Territory** *(OrgBridge, rde_crew)*: spray_territory · declare_war

> The access_level and job-specific flags are intentionally generic — adapt their labels and descriptions per org type. A cop org uses `dispatch_access` differently than a cartel. Both use the same flag, with descriptions they set themselves.

**Per-member overrides:** individual members can have specific permissions granted or restricted on top of their rank. A Captain who shouldn't have withdraw access, or a Rookie who's been trusted with invite rights — handled without touching the rank definition.

### 💰 Bank & Salary

- Per-org bank balance with configurable maximum (`Config.MaxOrgBank`)
- Deposit and withdraw with permission gating
- **Manual salary payments** — `send_salary` permission lets authorized ranks pay individual members on demand
- **Auto-salary payout** — configurable interval (default: 60 minutes), pays all online members their per-member salary automatically
- Full transaction history per org: deposit / withdraw / salary / admin / transfer entries
- Bank history UI accessible to members with `bank_view` permission
- Admin can set bank balance directly

### 🖥️ Boss Terminal

- Configurable NPC anchor point (model, coords, scenario, blip)
- ox_target interaction — no key prompts, no zones
- Opens the full NUI org panel for members of any org linked to that terminal
- Terminal location manageable in-game by members with `manage_terminal` permission
- Configurable entry animation

### 🎛️ NUI Panels

Three separate NUI interfaces, each scoped to its audience:

**Member Panel** (`web/panel.html`)
Full org management for members: overview, member list, rank management, bank, salary, invite, kick, promote, demote, custom perms, settings, announcements — all permission-gated so members only see what their rank allows.

**Boss Terminal** (`web/boss_terminal.html`)
Polished org hub accessible from the NPC anchor — designed for in-world use rather than a detached admin context.

**Admin Panel** (`web/admin.html`)
Server admin tooling: create/edit/delete any org, add/remove/promote any member, set bank balances, view audit log, wipe all. Accessible only to players in `Config.AdminGroups` or with the configured ace permission.

### 🛠️ Admin Tools

- Create orgs with full parameter control and direct owner assignment
- Edit any org's label, type, description, bank, color, icon
- Add and remove members regardless of invite/capacity limits
- Set any member's rank and custom permissions
- View the full audit log across all orgs
- Wipe all organizations in one operation (requires explicit confirmation string)
- Full statebag sync after every admin action

---

## 🗂️ Org Types

| Type | Label | Default Use |
|---|---|---|
| `gang` | Street Gang | Territorial street crews — pairs with rde_crew OrgBridge |
| `mc` | Motorcycle Club | MC hierarchy (Prospect → VP → President) |
| `criminal` | Criminal | Underground syndicates, mafias, cartels |
| `legal` | Legal Business | Law firms, shops, taxi companies |
| `corp` | Corporation | Megacorps, PMCs, hedge funds |
| `gov` | Government | Police, EMS, city council |
| `neutral` | Neutral | No affiliation — general use |
| `custom` | Custom | Fully custom — no type restrictions |

Each type ships with a default rank template applied at org creation. All ranks are fully editable, renamable, and deletable (except the leader rank) after creation.

---

## 🔐 Permission System

Every permission has a key, icon, label, category, and description. They're rendered in the rank editor grouped by category. Toggle them per rank. Override them per member.

```lua
Config.Permissions = {
    -- Members
    invite          = { icon='user-plus',   label='Invite Members',      cat='members'   },
    kick            = { icon='user-minus',  label='Kick Members',        cat='members'   },
    promote         = { icon='arrow-up',    label='Promote',             cat='members'   },
    demote          = { icon='arrow-down',  label='Demote',              cat='members'   },
    set_salary      = { icon='wallet',      label='Set Member Salary',   cat='members'   },
    view_members    = { icon='users',       label='View Members',        cat='members'   },
    -- Bank
    bank_deposit    = { icon='piggy-bank',  label='Bank Deposit',        cat='bank'      },
    bank_withdraw   = { icon='banknote',    label='Bank Withdraw',       cat='bank'      },
    bank_view       = { icon='eye',         label='View Bank Balance',   cat='bank'      },
    send_salary     = { icon='send',        label='Send Manual Salary',  cat='bank'      },
    -- Ranks
    manage_ranks    = { icon='shield',      label='Manage Ranks',        cat='ranks'     },
    assign_ranks    = { icon='tag',         label='Assign Ranks',        cat='ranks'     },
    -- Settings
    edit_org        = { icon='pencil',      label='Edit Org Info',       cat='settings'  },
    manage_blip     = { icon='map-pin',     label='Manage Map Blip',     cat='settings'  },
    manage_terminal = { icon='monitor',     label='Manage Boss Terminal',cat='settings'  },
    -- High Clearance
    transfer_leader = { icon='crown',       label='Transfer Leadership', cat='danger'    },
    disband         = { icon='trash-2',     label='Disband Org',         cat='danger'    },
    -- Access Control
    access_level_1  = { icon='lock-open',   label='Access Level 1',      cat='access'    },
    access_level_2  = { icon='shield-check',label='Access Level 2',      cat='access'    },
    access_level_3  = { icon='key',         label='Access Level 3',      cat='access'    },
    access_level_4  = { icon='badge',       label='Access Level 4',      cat='access'    },
    use_vehicles    = { icon='car',         label='Use Org Vehicles',    cat='access'    },
    use_armory      = { icon='gun',         label='Use Org Armory',      cat='access'    },
    use_stash       = { icon='archive',     label='Use Org Stash',       cat='access'    },
    dispatch_access = { icon='radio',       label='Dispatch Access',     cat='access'    },
    intel_access    = { icon='file-search', label='Intel Board Access',  cat='access'    },
    -- Territory (OrgBridge — rde_crew)
    spray_territory = { icon='spray-can',   label='Spray Territory',     cat='territory' },
    declare_war     = { icon='swords',      label='Declare War',         cat='territory' },
}
```

---

## 🔗 OrgBridge — rde_crew Territory Integration

rde_organizations can act as the HR layer for [`rde_crew`](https://github.com/RedDragonElite/rde_crew)'s territory system. When both resources are installed and the bridge is enabled on the rde_crew side, gang-type orgs become territory-holding crews automatically.

**You manage the org. rde_crew manages the map.**

### How it works

rde_organizations fires server-side events at every mutation point:

```
rde_orgs:server:orgCreated       — org created → rde_crew creates linked crew
rde_orgs:server:orgDisbanded     — org disbanded → rde_crew dissolves crew + cascade-deletes graffiti
rde_orgs:server:orgUpdated       — settings changed → crew name/color updated
rde_orgs:server:memberAdded      — member joined → added to crew
rde_orgs:server:memberRemoved    — member kicked/left → removed from crew
rde_orgs:server:memberRankChanged — rank changed → crew grade updated
rde_orgs:server:leaderTransferred — new leader → crew leader updated
rde_orgs:server:ranksUpdated     — rank created/edited/deleted → crew grades rebuilt
rde_orgs:server:fullSync         — full resync on org type change or restart recovery
```

rde_organizations knows nothing about rde_crew. The bridge lives entirely in rde_crew and listens to these events. Both resources remain fully independent.

### Territory permissions

Two territory-specific permissions (`spray_territory`, `declare_war`) are defined in `Config.Permissions` under the `territory` category. They appear in the rank permission editor for gang-type orgs and map to rde_crew's permission system via the bridge configuration.

### Setup

1. Install `rde_crew` with OrgBridge support (v2.0.1+)
2. Ensure `rde_organizations` starts **before** `rde_crew` in `server.cfg`
3. Set `Config.OrgBridge.enabled = true` in rde_crew's `config.lua`
4. Create a gang, MC, or criminal org — the territory crew is created automatically

No additional configuration required. See [rde_crew](https://github.com/RedDragonElite/rde_crew) for full OrgBridge documentation.

---

## 📦 Dependencies

| Resource | Required | Notes |
|---|---|---|
| `oxmysql` | ✅ Required | Database layer |
| `ox_core` | ✅ Required | Player/character framework |
| `ox_lib` | ✅ Required | Callbacks, notifications, UI components |
| `ox_target` | ✅ Required | Boss terminal interaction |
| `rde_crew` | ⚙️ Optional | Enables OrgBridge territory integration |

---

## 🚀 Installation

### 1. Clone the repository

```bash
cd resources
git clone https://github.com/RedDragonElite/rde_organizations.git
```

### 2. Add to server.cfg

```
ensure oxmysql
ensure ox_lib
ensure ox_core
ensure ox_target
ensure rde_organizations
ensure rde_crew        # after rde_organizations if using OrgBridge
```

> ⚠️ `rde_organizations` must start **before** `rde_crew` when using OrgBridge. ox_core must be fully loaded before rde_organizations starts.

### 3. Database

All four tables are created automatically on first start. No manual SQL import needed.

### 4. Configure (optional)

Edit `config.lua` to set org limits, salary intervals, permission definitions, rank templates, boss terminal settings, and org types.

### 5. Restart

```
restart rde_organizations
```

---

## ⚙️ Configuration

### Core

```lua
Config.Debug              = false
Config.DefaultLanguage    = GetConvar('ox:locale', 'en')
Config.StatebagPrefix     = 'rde_orgs_'
Config.AllowAcePermissions = true
Config.AdminGroups        = { ['admin']=true, ['superadmin']=true, ['owner']=true }
```

### Limits

```lua
Config.MaxOrgsPerPlayer   = 5       -- max orgs a player can be in simultaneously
Config.MaxMembersPerOrg   = 100     -- 0 = unlimited
Config.MaxRanksPerOrg     = 20
Config.OrgNameMinLength   = 3
Config.OrgNameMaxLength   = 32
Config.MinSalary          = 0
Config.MaxSalary          = 500000
Config.MaxOrgBank         = 999999999
Config.SalaryIntervalMin  = 60      -- auto-salary payout interval in minutes
Config.InviteExpirySec    = 120
```

### Boss Terminal

```lua
Config.BossTerminal = {
    interactDistance = 2.5,
    enterAnim = {
        dict = 'amb@prop_human_atm@male@idle_a',
        anim = 'idle_a',
        flag = 49,
        dur  = 2500,
    },
}
```

### Admin

```lua
Config.Admin = {
    aceGroup = 'rde.admin',
}
```

### Adding Custom Org Types

```lua
Config.OrgTypes[#Config.OrgTypes + 1] = {
    value   = 'mercenary',
    label   = 'Mercenary Group',
    icon    = 'crosshair',
    color   = '#dc2626',
    desc    = 'PMC and hired gun outfits.',
}
```

Then add a matching entry to `Config.DefaultRankTemplates` with your desired default ranks.

---

## 🎮 Usage

### Creating an Organization

1. Open the org panel via the boss terminal NPC or `/org`
2. Select **Create Organization**
3. Enter name, label, type, and description
4. You're automatically added as the leader on the highest rank

### Managing Members

From the member panel, the leader (and ranks with appropriate permissions) can:
- **Invite** any online player by server ID
- **Promote / Demote** members within the rank ladder
- **Kick** members (limited to members below your own rank)
- **Set Salary** per member
- **Grant Custom Permissions** on top of the member's rank

### Bank

Members with `bank_deposit` can add funds. Members with `bank_withdraw` can take funds. `bank_view` shows the balance and full transaction history. `send_salary` allows manually paying any member's configured salary immediately.

### Rank Management

Members with `manage_ranks` can create, edit, and delete ranks. Cannot delete the leader rank. Cannot promote a member to the leader rank (use Transfer Leadership for that). Cannot grant permissions you don't hold yourself.

### Boss Terminal

The terminal NPC location is set per-org and stored in the database. Members with `manage_terminal` can move it in-game.

---

## 🏗️ Architecture

```
rde_organizations/
├── fxmanifest.lua
├── config.lua              ← all config + locales + permission definitions
├── server/
│   └── server.lua          ← complete server authority layer (single file)
│                              events, callbacks, DB, statebag sync, admin, audit
├── client/
│   └── client.lua          ← NUI management, boss terminal, statebag listener
└── web/
    ├── index.html           ← NUI router / entry point
    ├── panel.html           ← member panel (org management)
    ├── boss_terminal.html   ← boss terminal interface
    └── admin.html           ← server admin hub
```

### Single-file server architecture

`server/server.lua` is intentionally a single file. Everything in one place means one audit trail — no hidden cross-module state, no implicit load-order dependencies, no "which file handles this event?" archaeology. For a resource of this scope, that's a feature, not a compromise.

### Statebag layout

The player statebag key is `rde_orgs_<identifier>` and contains an array of all org memberships:

```lua
{
    {
        orgId       = 42,
        orgName     = 'los_santos_ballas',
        orgLabel    = 'Los Santos Ballas',
        orgType     = 'gang',
        orgColor    = '#f59e0b',
        rankId      = 7,
        rankLabel   = 'Captain',
        rankOrder   = 3,
        isLeader    = false,
        permissions = { invite=true, kick=true, spray_territory=true },
        salary      = 2500,
        customPerms = {},
    },
    -- ... up to MaxOrgsPerPlayer entries
}
```

Any resource that reads this key has the full picture of a player's org memberships, ranks, and effective permissions — no callbacks needed for read access.

---

## 🛡️ Security

**Permission enforcement is server-authoritative at every layer:**

- `HasPerm(identifier, orgId, permKey)` is called before every mutation event. Clients can claim anything — the server checks the DB.
- Permission escalation is blocked: a rank editor cannot grant permissions the editor doesn't hold. The server strips any excess permissions silently before writing to DB.
- Rank deletion is blocked if any member currently holds that rank — prevents orphaned memberships.
- Leader rank cannot be deleted or demoted via the standard rank flow — only `transferLeader` moves the leader flag.
- Admin routes check `IsAdmin(source)` independently of the org permission system. A regular member cannot reach admin endpoints even with crafted events.
- Invite expiry is enforced server-side at acceptance time — stale invites are rejected regardless of client state.
- Custom permissions passed by the client are sanitized server-side: only permissions the authorizing member actually holds can be delegated.

---

## 📋 Commands

| Command | Restricted | Description |
|---|---|---|
| `/org` | No | Open the org panel |
| `/org_admin` | Admin | Open the admin hub |
| `/crew_admin` | Admin | Open the RDE crew admin hub (if rde_crew installed) |

---

## 🗄️ Database

| Table | Purpose |
|---|---|
| `rde_organizations` | Core org row — name, label, type, color, icon, description, bank, owner |
| `rde_org_ranks` | Rank definitions per org — label, order, color, icon, is_leader, permissions JSON |
| `rde_org_members` | Membership rows — org_id, identifier, rank_id, salary, custom_permissions JSON |
| `rde_org_transactions` | Bank ledger — type, amount, by_name, by_id, note, timestamp |
| `rde_org_audit_log` | Full action audit trail — action, by_name, by_id, target_id, details, is_admin |

All tables auto-create on first start via `CREATE TABLE IF NOT EXISTS`. The `rde_organizations` table has an `updated_at TIMESTAMP ... ON UPDATE CURRENT_TIMESTAMP` column updated on every settings change.

**Foreign key cascade:** deleting an org cascades to ranks, members, and transactions. Rank deletion is blocked by the application layer (not the DB constraint) if members hold that rank — giving a useful error instead of a silent constraint violation.

---

## ⚡ Performance

**Statebag-driven reads:** the client statebag is the single source of truth for membership and permissions. Any resource that needs to check "is this player in org X with permission Y?" reads `LocalPlayer.state['rde_orgs_<id>']` directly — zero callbacks, zero server round-trips.

**Event-driven mutations:** every write fires one DB query and one statebag update. No polling. No periodic sync threads for state that hasn't changed.

**Salary thread:** runs on a configurable interval (default 60 minutes) and only iterates online players. Offline member balances are not touched by the auto-payout — salary is a live-player event.

**Statebag key per player, not per org:** `rde_orgs_<identifier>` contains all of a player's memberships as a single array. One key, one write, one statebag update covers all of a player's org data simultaneously — no per-org per-player key proliferation.

---

## 🐛 Troubleshooting

**Org panel doesn't open?**
Check `ui_page` in `fxmanifest.lua` points to `web/index.html`. Confirm `client.lua` is loaded and the NUI callback `rde_orgs:openPanel` is firing in F8.

**Statebag not updating after join/kick?**
Check server console for `[RDE-ORGS]` log lines. Set `Config.Debug = true` for verbose identifier resolution logs. The most common cause is a mismatch between `player.stateId` and `player.charId` — confirm `GetIdentifier()` is resolving to the expected value for your ox_core version.

**Auto-salary not paying?**
Check `Config.SalaryIntervalMin` — minimum is intentionally floored at 1 minute. Confirm members have a non-zero salary set via the member panel or admin hub.

**OrgBridge: crew not appearing in rde_crew after org creation?**
Confirm `rde_organizations` starts before `rde_crew` in `server.cfg`. Confirm the org type is in `Config.OrgBridge.crewOrgTypes` (rde_crew side). Check rde_crew server console for `[OrgBridge]` log lines.

**Rank editor shows no permissions?**
Confirm `Config.Permissions` is populated in `config.lua`. The permission list is read dynamically — an empty or malformed table silently produces an empty editor.

**"No permission" on an action a rank should have?**
Check if the member has a `custom_permissions` override that restricts the key. Custom overrides have priority over rank permissions. Check via admin panel → member → custom perms.

---

## 📝 Changelog

### v2.0.3 — RDE Ecosystem Bridge + Territory Permissions

**OrgBridge Events**
Added outbound inter-resource events at every mutation point in `server/server.lua`. rde_crew's OrgBridge module listens to these events to keep territory crew data in sync with org membership changes. Zero coupling — rde_organizations never imports or calls rde_crew directly. Events fire after every successful mutation: org created/disbanded/updated, member added/removed/rank-changed, leader transferred, ranks created/updated/deleted. Admin mutations fire the same events as player mutations.

**Territory Permissions**
Added `spray_territory` and `declare_war` to `Config.Permissions` under a new `territory` category. These permissions appear in the rank editor for gang/mc/criminal org types and map to rde_crew's permission system via OrgBridge. A gang rank with `spray_territory = true` can spray graffiti in rde_crew. A rank with `declare_war = true` can declare territory wars.

**requestFullSyncFor handler**
Added `AddEventHandler('rde_orgs:server:requestFullSyncFor', ...)` — responds to rde_crew's bridge when an org changes type TO a gang type and needs a full data snapshot to create a linked crew.

### v2.0.2 — Rank ID Bug Fix

**`m.rank_id` / `m.rankId` normalization**
Raw MySQL rows from `rde_org_members` use snake_case (`rank_id`) but `BuildPlayerStatebag` and `GetMembership` were reading camelCase (`rankId`). The mismatch caused `GetMembership` to return a nil rank for all members, silently breaking permission checks and causing statebag entries to omit rank data.

Fix: normalize `m.rankId = m.rank_id` once in `LoadMembers()` so every downstream read works regardless of which field name it uses. The `m.rank_id or m.rankId` fallback pattern was removed from all call sites and replaced with the guaranteed `m.rankId` field.

### v2.0.1 — Rank System Rewrite

**Unlimited custom ranks** per org (configurable max, default 20). Replaced the old hardcoded 4-rank system. Rank creation, editing, deletion, and permission management moved to the in-game panel. Leader rank is locked by the application layer.

**25 granular permission flags** per rank (up from 12 in v1.x). New categories: access control (4 levels + vehicles/armory/stash/dispatch/intel) and high clearance (transfer_leader, disband).

**Per-member permission overrides.** Individual members can have specific permissions granted or restricted on top of their rank without changing the rank definition. Overrides are sanitized server-side to prevent privilege escalation.

**Leader title renamable.** Call your top rank anything you want — the `is_leader` flag tracks which rank holds leader-level power, not the name.

**Org types** expanded to 8 with distinct default rank templates per type. Templates are applied only at creation and are fully editable afterward.

### v2.0.0 — Complete Rewrite

Rebuilt from scratch on ox_core with a single-file server architecture, statebag-driven sync, NUI management panel, boss terminal integration, full bank system with transaction history, auto-salary payout thread, and a complete admin hub. ESX and QBCore support removed.

---

## 🙏 Contributors & Credits

| | |
|---|---|
| 🐉 **SerpentsByte** | Architect & developer |

---

## 📜 License

```
###################################################################################
# .:: RED DRAGON ELITE (RDE) - BLACK FLAG SOURCE LICENSE v6.66 ::.
# PROJECT: RDE_ORGANIZATIONS v2.0.3 (MULTI-ORG SYSTEM)
# ARCHITECT: .:: RDE ⧌ Shin [△ ᛋᛅᚱᛒᛅᚾᛏᛋ ᛒᛁᛏᛅ ▽] ::. | https://rd-elite.com
# ORIGIN: https://github.com/RedDragonElite
#
# [ THE RULES OF THE GAME ]
#
# 1. // THE "FUCK GREED" PROTOCOL (FREE USE)
#    You are free to use, edit, and abuse this code on your server.
#    Learn from it. Break it. Fix it. That is the hacker way.
#    Cost: 0.00€. If you paid for this, you got scammed by a rat.
#
# 2. // THE TEBEX KILL SWITCH (COMMERCIAL SUICIDE)
#    If I find this script on Tebex, Patreon, or in a paid "Premium Pack":
#    > I will DMCA your store into oblivion.
#    > I will publicly shame your community.
#    SELLING FREE WORK IS THEFT. AND I AM THE JUDGE.
#
# 3. // THE CREDIT OATH
#    Keep this header. If you remove my name, you admit you have no skill.
#
# 4. // THE CURSE OF THE COPY-PASTE
#    This code uses a single-file server architecture, statebag-driven sync,
#    and a permission system with 27 individual flags. If you copy-paste without
#    understanding the identifier normalization bug in v2.0.2, you will ship
#    broken permission checks. RTFM first. DMs second.
#
# "We build the future on the graves of paid resources."
# "REJECT MODERN MEDIOCRITY. EMBRACE RDE SUPERIORITY."
###################################################################################
```

---

## 🌐 Community & Support

| | |
|---|---|
| 🐙 GitHub | [RedDragonElite](https://github.com/RedDragonElite) |
| 🌍 Website | [rd-elite.com](https://rd-elite.com) |
| 🎯 RDE Crew | [rde_crew](https://github.com/RedDragonElite/rde_crew) |
| 🚗 RDE Parking | [rde_parking](https://github.com/RedDragonElite/rde_parking) |
| 🚪 RDE Doors | [rde_doors](https://github.com/RedDragonElite/rde_doors) |
| 🚨 RDE AIPD | [rde_aipd](https://github.com/RedDragonElite/rde_aipd) |
| 🏥 RDE AIMD | [rde_aimd](https://github.com/RedDragonElite/rde_aimd) |

When asking for help, always include:
- Full error from server console or txAdmin
- Your `server.cfg` resource start order
- ox_core / ox_lib versions in use
- `Config.Debug = true` output if the issue is permission or statebag related

---

> *"One system for every faction. Every rank, every permission, every member.*
> *Managed in-game. Audited. Free."*
>
> **REJECT MODERN MEDIOCRITY. EMBRACE RDE SUPERIORITY.**

[![Website](https://img.shields.io/badge/Website-Visit-red?style=for-the-badge&logo=google-chrome)](https://rd-elite.com)
[![Nostr](https://img.shields.io/badge/Nostr-Follow-purple?style=for-the-badge&logo=rss)](https://primal.net/p/npub1wr4e24zn6zzjqx8kvnelfvktf0pu6l2gx4gvw06zead2eqyn23sq9tsd94)
[![RDE Ecosystem](https://img.shields.io/badge/RDE-ECOSYSTEM-f59e0b?style=for-the-badge)](https://github.com/RedDragonElite)

🐉 *Made with 🔥 by Red Dragon Elite*

[⬆ Back to Top](#-rde_organizations)
