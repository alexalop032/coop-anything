# Enter the Gungeon — session notes

## Game identity (Phase 0, recorded 2026-10-18)

- Title: Enter the Gungeon (Dodge Roll / Devolver Digital)
- Store: Steam, appid 311690
- Steam buildid: 3829397 (appmanifest LastUpdated 1750969237 = 2025-06-27)
- Depots: 311691 manifest 8878936682682257329, 311694 manifest 1630144318006415956
- SHA-256 of EtG.exe: 220E76C1263559BB38DA6C1ED8B0F473F2FF67E5234A6CCC93ECAB98725B5B61
- EtG.exe size: 650752 bytes; FileVersionRaw 2017.4.27.19333
- Engine: Unity 2017.4.27f1 (from EtG_Data/globalgamemanagers)
- OS: Windows (win32)
- Install path: see `local/install-path.txt` (private, git-ignored)
- Game language config: russian
- Architecture (32/64-bit): unknown — not yet checked

## Phase 0 — triage result

- No entry for this game in DIRECTORY.md or data/directory.yaml (grep "gungeon"/"gundion" = no matches).
- No prior work under games/ in this hub.
- Game natively has local couch co-op (up to 4) but no official online co-op (dev statement, Steam discussions 2015).
- User request: online co-op with friends. Steam copy, default install path.
- -> Path candidate: Path 1 (revive/extend existing attempts) — several exist, see Phase 1.

## Phase 1 — research (done 2026-10-18, sources = web + GitHub API this session)

| Project | Where | License | Last confirmed activity | What it really syncs (per author) | Bug reports |
|---|---|---|---|---|---|
| **Gungeon Online** v0.101.0 (GoTeamAlex) | thunderstore.io/c/enter-the-gungeon/p/GoTeamAlex/Gungeon_Online | MIT | Thunderstore (date not scraped — unknown) | Steam lobby invites + LAN UDP 27460; shared seed run, player char/anim/gun/pet/status, shared enemy HP/deaths, bullets/throws/blanks/floor effects, tables/breakables/barrels, ghost+revive from chest, shared pause, optional PvP. NOT synced: enemy attack simulation (each client simulates own), death-dropped items | Thunderstore wiki/changelog |
| **GungeonTogether** (llamerrr) | github.com/llamerrr/GungeonTogether | MIT | last commit 2026-09-28 (PR #12 build scripts); README still says "ON HOLD" | Steam lobby/invites + P2P done; player pos/anim sync working; enemies/dungeon planned. **No released build** — issues #7/#11/#13 all "can't find the DLL" | GitHub issues (9 open); #2 "Mega Issue of Bugs": clients not teleported to run, invisible clients, client→client sync missing, player switch not propagated, UI bugs, no outlines/shadows on remote players, name tags over UI, cleanup broken |
| **GungeonTogether LAN** v0.3.6 (mamonts) | thunderstore.io/.../mamonts/GungeonTogether_LAN | (unknown) | v0.3.6 (Thunderstore) | 2p LAN/TCP 27631; host runs native co-op, client drives player 2, own camera/HUD, secret-room + equipment book sync. **Known blocker: 4th-floor Sell Creep map mismatch stops runs**; full campaign unverified; built for Steam EtG 2.1.9 | Thunderstore page + VALIDATION.md in its source |
| **Jolly_Coop** v1.3.7 (kleirof) | github.com/kleirof/Jolly_Coop | (none stated) | release 2026-01-02 (zip, SHA-256 e72c3616..., 4 downloads) | Unknown — plugin boots, Harmony patches 53KB, OutlineColorManager (player outlines), depends on ModTheGungeon API + Gunfig. No network code seen in file listing, but not read in full. Likely local-coop enhancement | none (no issues) |
| Gunnect+ (The_Rot_Bot) | modworkshop.net/mod/34633 | (unknown) | 2021 | NOT a co-op mod — cross-run interaction framework, needs Hamachi | modworkshop comments |

Also seen (not evaluated): CoopBot (ItsYuri, Thunderstore) — "bot buddy simulating co-op" — candidate for QA bot (playbook/automated-qa). GungeonCraft, Ammonomicon API (Thunderstore) — modding infrastructure.

### Ecosystem facts (Phase 3 shortcuts)

- Mod loaders: ModTheGungeon (classic, MTG API 1.6.1) and BepInExPack_ETG (Thunderstore). Both used by the candidates.
- **EtG.GameLibs NuGet package exists** (v2.1.9.1): decompiled Assembly-CSharp.dll (3.5 MB), Assembly-CSharp-firstpass, PlayMaker, full UnityEngine modules — seen vendored in Jolly_Coop repo. No need to decompile ourselves for v2.1.9.
- Game = Unity 2017.4.27f1, C# managed code, HarmonyX/Harmony patching = standard approach (playbook engine-families: Unity → BepInEx/Harmony).
- Game version across sources: 2.1.9 (matches our install? — buildid 3829397, "unknown" link between 2.1.9 and buildid; verify from EtG_Data or game changelog later).

### Observations / derived / guesses / unknowns (rule 4)

- Observation: only "Enter the Gundion" web hit is a Reddit joke misspelling; user confirmed Enter the Gungeon.
- Observation: no entry for this game in hub DIRECTORY.md / games/ — game is uncovered; AGENTS.md wants a "claim a game" issue (blocked: gh CLI not installed).
- Observation: GungeonTogether has NO published builds (3 open issues ask for the DLL).
- Derived: Path 1 (finish what exists) is the right path; two viable tracks — Gungeon Online (most complete, playable) and GungeonTogether (most active, not yet playable).
- Derived: EtG.GameLibs NuGet removes the heaviest Phase 3 step (decompilation).
- Guess: buildid 3829397 ↔ game version 2.1.9 (both candidates target 2.1.9; acf LastUpdated 2025-06-27).
- Unknown: Jolly_Coop full feature set; Gungeon Online last-uploaded date; whether Gungeon Online works on current build 3829397 (its page doesn't state game version).

## Environment notes (private)

- `gh` CLI: installed 2026-10-18 (v2.102.0) — not yet authenticated (needs owner's GitHub login).
- `python`: installed 2026-10-18 (3.12.10) at `%LOCALAPPDATA%\Programs\Python\Python312\python.exe`. `.NET SDK` 8.0.426 installed. PATH may need refresh in new shells.

## Phase 2 — workspace (done 2026-10-18, owner-approved)

- Test install: `<workspace>/test-install/Enter the Gungeon` (robocopy of real install, 202 files / 318.4 MB, 0 failures).
- Mod loader: BepInEx-BepInExPack_EtG 5.4.2101 (pinned, official Thunderstore).
  - ZIP SHA-256: 6B97EDB311E961E78CA1CD913A886FD72B1EE936C73300C8992B8BF86FFDE7BA (720997 bytes)
- Mod: GoTeamAlex-Gungeon_Online 0.101.0 (pinned, official Thunderstore; uploaded 2026-10-17).
  - ZIP SHA-256: EF8E276651A089CA48F6DA5714E053C8715E0AAD88046F90CE7E2ED51A2D5546 (111855 bytes)
  - Declared dependency: BepInEx-BepInExPack_EtG-5.4.2101 (satisfied).
- Layout: doorstop (winhttp.dll) + BepInEx at test root; `BepInEx/plugins/GungeonOnline.dll`.

## Phase 3 — boot test (2026-10-18)

- Test: launched `<test>/EtG.exe` directly while Steam running; observed 50s; read `<test>/BepInEx/LogOutput.log`.
- Result: **PASS on plugin load.** BepInEx 5.4.21.0 under Unity 2017.4.27.805765, Bits64 Windows; "Loading [Gungeon Online 0.101.0]"; save-sync hooks active; "Gungeon Online 0.101.0 loaded. Press F8 in game to open the multiplayer window."; chainloader startup complete. No errors from the mod.
- Unity player log (`%USERPROFILE%\AppData\LocalLow\Dodge Roll\Enter the Gungeon\output_log.txt`): game reached foyer/character-select (select_idle clip warnings only, benign). XInputInterface32 x86/x64 warnings are stock game noise.

### Incident (rule 2) — disclosed to owner

- Direct-launching the test exe triggered `SteamAPI_RestartAppIfNecessary`: test process quit (~30s in), Steam auto-launched the REAL install (appmanifest LastPlayed updated to 2026-10-18 20:17). Agent killed the real instance ~30s later at the menu.
- Damage check: `SlotA.save` untouched (mtime 2026-05-15). No run started. `SlotA.options` mtime 8:16:31 (written during test run; options only), `steam_autocloud.vdf` and output_log.txt touched.
- Mitigation: full backup of `%USERPROFILE%\AppData\LocalLow\Dodge Roll\Enter the Gungeon\` at `local/locallow-backup-20261018/` BEFORE further tests. Lesson: Unity LocalLow dir is shared by every game copy — always back up before tests and restore after.
- **QA setup:** созданы два тестовых слота `test-install-host\Enter the Gungeon` и `test-install-client\Enter the Gungeon`. В обеих копиях заменён `steam_api.dll` на реальный (правильной версии x86) из Steamworks.NET Standalone 2025.164.1 (SHA-256: FDAFB7C65E...47EB65DA) и добавлен `steam_appid.txt` = 480. Это позволит Gungeon Online использовать Steam API (лобби/инвайты/релей P2P). Так как на одном ПК нельзя быть залогиненным в двух аккаунтах Steam, полноценный мультиплеер через реальные аккаунты придётся тестить на двух ПК или через конкретный mock Steamworks; текущий шаг — проверка запуска с подменённым DLL.
- **Быстрый smoke-тест хоста:** запустил хост-копию с восстановленным LocalLow. Мод загрузился (`Gungeon Online 0.101.0 loaded`), добавил кнопку Multiplayer в паузу, провёл self-test синхронизации сохранений (434/434 совпало). Steam API успешно инициализирован (`Starting Steam platform interface`, получены статы). Агент корректно остановил процесс без повреждения `SlotA.save` (оригинал сохранён в бэкапе). 

## Phase 6 — двухклиентный QA (LAN, 2026-10-18) — PASS

Ключевое открытие: **Gungeon Online имеет встроенный автоматизированный тест-харнесс** (декомпилированы из `BepInEx/plugins/GungeonOnline.dll`, см. `local/decomp/GungeonOnline.decompiled.cs`):

- `--gungeon-online-name <name>` — задать имя игрока.
- `--gungeon-online-test host|join` — автоматически занять хост/LAN на титульном экране (хост — `HostLan(27460)`, клиент — `JoinLan(127.0.0.1:27460)`), затем войти в Breach.
- `--gungeon-online-test-run <CharacterPrefabSuffix>` — автоматически выбрать персонажа (`CharacterPrefabPath.EndsWith(suffix)`) и (хост) стартовать забег после подключения партнёра.

Известные `CharacterPrefabPath` в ассетах игры: `PlayerMarine`, `PlayerGuide`, `Player_Rogue`/`Player_Rogue_`, `Player_Convict`(?), `PlayerRobot`, `PlayerBullet`, `PlayerGunslinger`, `PlayerEevee`, `PlayerCoopCultist`, `PlayerCosmonaut`.

### Изоляция сейвов (правило 2)

Перед тестом папка `%USERPROFILE%\AppData\LocalLow\Dodge Roll\Enter the Gungeon` переименована в `...REAL-<ts>` (полная копия сейва владельца сохранена), создана пустая тестовая папка. После теста реальная папка восстановлена (подтверждено: `SlotA.save` mtime 5/15/2026 не изменён). Тестовые копии используют `steam_api.dll` (x86) из Steamworks.NET + `steam_appid.txt=480` (Spacewar), поэтому Steam Cloud EtG (appid 311690) не синхронизируется.

### Прогон 1 — связь (2 клиента, локальная UDP-петля)

Команды:
```
host : EtG.exe --gungeon-online-name Hosty   --gungeon-online-test host -screen-fullscreen 0 -screen-width 960 -screen-height 540
client: EtG.exe --gungeon-online-name Clienty --gungeon-online-test join -screen-fullscreen 0 -screen-width 960 -screen-height 540
```
Результат: оба процесса работают одновременно (нет single-instance mutex). Хост: `Hosting LAN game on port 27460` → `Connected to Clienty over LAN!`. Клиент: `Joining LAN game at 127.0.0.1:27460` → `Connected to Hosty over LAN!` → `Joined from the title screen: entering the Breach.` Стабильно ~55 с без таймаутов. Логи: `local/qa-run1/`.

### Прогон 2 — полный забег (PASS)

Команды (добавлен `--gungeon-online-test-run`):
```
host : EtG.exe --gungeon-online-name Hosty   --gungeon-online-test host --gungeon-online-test-run Marine ...
client: EtG.exe --gungeon-online-name Clienty --gungeon-online-test join --gungeon-online-test-run Guide  ...
```
Результат (логи `local/qa-run2-fullrun/`):
- `Test run: picking PlayerMarine` (хост), `Test run: picking PlayerGuide` (клиент).
- Хост: `Test run: host starting the run.` → `SETTING GLOBAL RUN SEED TO: 542264008` → `Floor fingerprint: 20 rooms, 2A7BE448 (LoadingDungeon|Base_Castle(Clone)|542264008)`.
- Клиент: тот же seed `542264008`, и **`Floor matches the host's (2A7BE448).`** — процедурная генерация этажа совпала.
- Синхронизация: `Partner map marker`, `Created the partner's target body for enemies`, `Showing partner's pet Dog`, `Indexed 427 projectile types from 379 guns`, shared pause (`Clienty paused/unpaused`), SaveSync (`got the host's progress`).
- ~4 мин аптайма: ни `timed out`, ни `Disconnected`, ни `Exception`, ни `mismatch` в логах.

Итог: **два клиента, реальный мод, LAN, подключение, выбор персонажей, общий сид забега, идентичный этаж, маркеры/питомцы/пауза/сейвы — всё PASS.** Мод работает на текущем билде 3829397 (game version 2.1.9).

### Что НЕ проверено

- Путь через Steam-лобби/инвайты (нужен второй Steam-аккаунт или второй ПК) — тестили только LAN.
- Стресс/долгие сессии, бой с боссами, 4-й этаж (Sell Creep) — по опыту GungeonTogether LAN там были баги; у Gungeon Online не проверялось.
- Десинки при рассинхроне симуляции атак врагов (автор сам пишет, что симуляция каждой стороны независима).

### Известные ограничения мода (по декомпиляции/описанию)

- Не синхронизируются: симуляция атак врагов (каждый клиент считает сам → возможны расхождения), предметы, выпавшие после смерти.
- Требуется один и тот же набор модов/версия у всех (протокол 32; иначе `version mismatch`).

## Next

- Решение с владельцем (фаза 7): ставить ли BepInEx + Gungeon Online в РЕАЛЬНУЮ установку Steam, чтобы играть с друзьями. Друзьям нужна та же версия мода/BepInEx.
- Опционально: проверить Steam-лобби-путь на двух ПК; длительный стресс-тест с боем и 4-м этажом.
- Фаза 8: запись в `data/directory.yaml` → `python tools/build_directory.py` → `games/enter-the-gungeon/README.md` (по шаблону) → draft PR (`gh` пока не авторизован).

## Next

- Two-client QA: LAN mode (UDP 27460) on 127.0.0.1 between two test slots; needs LocalLow backup+restore discipline; check single-instance mutex first.
- Steam-lobby path: needs mock steam_api.dll (one Steam account per PC) or a second physical PC.
- Phase 7 decision pending with owner: install into REAL install so they can play with friends.
