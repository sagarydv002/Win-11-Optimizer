# Windows Optimizer-QA.bat

Windows debloat/optimization script customized for a laptop QA Test Engineer/Devops workflow

## Usage
Run as Administrator. Menu:
1. System + user tweaks (default, 10s timeout)
2. User tweaks only
3. Exit

Log: `optimus-<computername>.log` (script folder)

## OS Support
- Windows 11 (build ≥22000) — full support
- Windows 10 (build ≥10240) — full support
- Windows 7 / older — warns and prompts Y/N; AppX/Edge-policy/WindowsAI sections silently no-op (keys/packages don't exist), core service/registry/explorer tweaks still apply

## What it does
- Creates a System Restore point (falls back to HKLM/HKCU `.reg` export if restore fails)
- Chassis-aware: enables hibernation on laptops, disables on desktops
- Restores legacy F8 boot menu
- Tunes pagefile based on installed RAM (skips if ≥32GB, uses auto-managed)
- SvcHost split threshold set per installed RAM
- Sets ~150 non-essential services to manual/disabled, core services forced to auto
- Registry: network throttling off, IRP stack size, shutdown timeout, long path support, telemetry/DiagTrack off, Edge background/ads/telemetry off, Recall off, Office telemetry off
- Removes Copilot, Bing Search app, Widgets (AppX + provisioning, all users)
- Per-user (HKCU + all local profiles + Default profile via hive load/unload): Explorer tweaks, ads/suggestions off, sticky keys off, Office logging off

## QA-specific deviations from stock TBOK script
| Item | Stock | This build | Why |
|---|---|---|---|
| `ssh-agent` | disabled | **left alone** | needed for git/test-server SSH |
| `XblAuthManager/XblGameSave/XboxNetApiSvc` | manual | **disabled** | not gaming-focused |
| Gaming Tweaks menu (option 3, desktop-only HAGS/power-plan/GPU P-state) | present | **removed** | irrelevant on this laptop; chassis check already no-ops desktop tweaks |
| WinRM, Hyper-V `vmic*` services | manual | **unchanged** | required for automation/VM-based testing |
| Windows Defender | untouched | **untouched** | unchanged |

## Safety
- Admin elevation is auto-requested (UAC prompt)
- Restore point created before any changes; if it fails, registry hives are exported to script folder as `.reg` backups
- Reboot prompted at end (Y/N, defaults to N after 10s)

## Rollback
Import the `.reg` backups (if created) or use System Restore to the pre-script checkpoint.