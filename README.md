<div align="center">

<img src="memclean.ico" width="120" height="120" alt="MD Mem Clean Icon"/>

# MD Mem Clean

### ⚡ Advanced Windows Memory Optimizer

[![Platform](https://img.shields.io/badge/Platform-Windows%207%2B-0078D4?style=for-the-badge&logo=windows&logoColor=white)](https://github.com)
[![Language](https://img.shields.io/badge/Language-C%20%28C11%29-00D2FF?style=for-the-badge&logo=c&logoColor=white)](https://github.com)
[![Architecture](https://img.shields.io/badge/Arch-x64-8A2BE2?style=for-the-badge)](https://github.com)
[![License](https://img.shields.io/badge/License-MIT-00E676?style=for-the-badge)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.0.0-FF6B35?style=for-the-badge)](https://github.com)
[![Made By](https://img.shields.io/badge/Made%20by-Mohamed%20Elba-00D2FF?style=for-the-badge)](https://github.com)

<br/>

> **Free your RAM. Instantly.**
> A powerful, portable Windows memory cleaner built in pure C — with deep NT kernel cleaning, a live usage graph, auto-clean, and a sleek dark UI. No bloat. No install. Just results.

<br/>

---

</div>

<br/>

## 🖥️ What is MD Mem Clean?

MD Mem Clean is a **free, open-source Windows memory optimizer** that goes deeper than Task Manager ever could. Instead of just killing processes, it communicates directly with the **Windows NT kernel** to purge standby memory lists, flush modified pages, trim process working sets, and clear the system file cache — recovering gigabytes of RAM that Windows silently holds onto.

It was built as a **better alternative to MemReduct** — with more cleaning modes, a live graph, smarter auto-clean, and a fully custom dark UI.

<br/>

## ✨ Features

<table>
<tr>
<td width="50%">

### 🧹 4 Cleaning Modes
| Mode | What it does |
|------|-------------|
| **QUICK** | Trims working sets of all processes |
| **NORMAL** | + Flushes modified pages + purges standby |
| **DEEP** | + Clears system file cache + low-priority standby |
| **FULL** | Everything, twice, with a recovery pause |

</td>
<td width="50%">

### 📊 Live RAM Dashboard
- Real-time used / free / total RAM
- Page file usage and load percentage
- System file cache size
- Color-coded load indicator
- 2-minute scrolling usage graph

</td>
</tr>
<tr>
<td>

### ⚙️ Smart Auto-Clean
- Configurable RAM threshold (10–95%)
- Configurable interval (30s → 10 min)
- Runs silently in the background
- Threshold line shown on live graph
- Desktop toast notification after each clean

</td>
<td>

### 🎨 Premium Dark UI
- Fully custom Win32 dark theme
- Hover-animated clean buttons
- Live `CLEANING...` badge while working
- Admin / No-Admin status badge
- Native title bar + system tray
- DPI-aware — sharp on 4K screens

</td>
</tr>
</table>

<br/>

## 🆚 MD Mem Clean vs MemReduct

| Feature | MemReduct | **MD Mem Clean** |
|---|:---:|:---:|
| Working Set trim | ✅ | ✅ |
| Standby List purge | ✅ | ✅ |
| Modified Page List flush | ✅ | ✅ |
| **Low-Priority Standby purge** | ❌ | ✅ |
| **Double-pass Full clean** | ❌ | ✅ |
| **4 distinct cleaning modes** | ❌ | ✅ |
| **Live cleaning indicator** | ❌ | ✅ |
| **Admin status badge** | ❌ | ✅ |
| **Page file stats** | ❌ | ✅ |
| **Per-mode toggle switches** | ❌ | ✅ |
| Dark theme | ❌ | ✅ |
| System tray | ✅ | ✅ |
| Auto-clean | ✅ | ✅ |
| Portable single EXE | ✅ | ✅ |
| Open source | ✅ | ✅ |

<br/>

## 🔬 How It Works

MD Mem Clean uses **undocumented NT kernel APIs** that Windows exposes through `ntdll.dll` — the same low-level path used by Microsoft's own tools like RAMMap and Process Explorer.

```c
// Direct kernel call to purge the Standby List
SYSTEM_MEMORY_LIST_COMMAND cmd = MemoryPurgeStandbyList;
NtSetSystemInformation(
    SystemMemoryListInformation,  // class 80
    &cmd,
    sizeof(cmd)
);
```

This bypasses all the usual Win32 abstraction layers and talks directly to the kernel memory manager — which is why it recovers far more RAM than standard approaches.

**Cleaning pipeline (FULL mode):**
```
Pass 1:
  [1] Trim working sets of all running processes
  [2] Flush Modified Page List  →  write dirty pages to disk
  [3] Purge Low-Priority Standby List
  [4] Purge Standby List
  [5] Clear System File Cache

        ↓  200ms recovery pause  ↓

Pass 2:
  [6] Trim working sets again (catches re-allocations)
  [7] Flush Modified Page List again
  [8] Purge Standby List again
```

<br/>

## 🚀 Getting Started

### Prerequisites

- Windows 7 or later (x64 recommended)
- [MinGW-w64](https://www.mingw-w64.org/) — install via:
  ```bat
  winget install mingw
  ```

### Build

```bat
git clone https://github.com/YOUR_USERNAME/md-mem-clean.git
cd md-mem-clean
build.bat
```

Output: `build\memclean.exe` *(also copied to root as `memclean.exe`)*

### Or compile manually

```bat
gcc memclean.c -o memclean.exe -mwindows -O2 ^
    -lpsapi -lgdi32 -lshell32 -lcomctl32 -luser32 -lwinmm -ladvapi32
```

<br/>

## 🛡️ Why Run as Administrator?

To use DEEP and FULL cleaning modes, MD Mem Clean needs these Windows privileges:

| Privilege | Used for |
|-----------|----------|
| `SeIncreaseQuotaPrivilege` | Trim process working sets |
| `SeDebugPrivilege` | Open all processes for trimming |
| `SeProfileSingleProcessPrivilege` | Access system memory info |
| `SeIncreaseWorkingSetPrivilege` | Modify working set limits |

> Without admin, only **QUICK mode** (working set trim) will work.
> The app will show a **`NO ADMIN`** badge in the UI if privileges are missing.

The included manifest requests UAC elevation automatically on launch — no manual right-click needed.

<br/>

## 🗂️ Project Structure

```
md-mem-clean/
│
├── memclean.c          ←  Full source code (~1,350 lines of C)
├── memclean.ico        ←  Application icon (256/128/64/48/32/16px)
├── memclean.manifest   ←  UAC elevation + DPI awareness + visual styles
├── build.bat           ←  One-click build script (MinGW/GCC)
├── run_as_admin.bat    ←  Launch with UAC prompt
├── quick_clean.bat     ←  Shortcut for fast cleaning
├── CHANGELOG.md        ←  Version history
└── README.md           ←  You are here
```

<br/>

## 🔧 Settings

Open Settings from the main window to configure:

- **Toggle each cleaning operation** — enable/disable any of the 5 cleaning types individually
- **Auto-Clean threshold** — set the RAM % that triggers an automatic clean (drag slider)
- **Auto-Clean interval** — how often to check (30 seconds to 10 minutes)
- **Minimize to tray** — hide to system tray instead of taskbar on minimize

<br/>

## 📋 System Requirements

| Component | Requirement |
|-----------|------------|
| OS | Windows 7 / 8 / 10 / 11 (x64) |
| Privileges | Administrator (for NORMAL / DEEP / FULL modes) |
| Runtime | None — no dependencies |
| Install | Not required — single portable EXE |
| Size | ~200 KB |

<br/>

## 🗺️ Roadmap

- [ ] Save settings to registry / INI file
- [ ] Per-process memory detail view
- [ ] Scheduled clean tasks (Windows Task Scheduler integration)
- [ ] Command-line mode (`memclean.exe /clean:deep /silent`)
- [ ] Startup with Windows option
- [ ] Clean history log
- [ ] Multiple language support

<br/>

## 📄 License

This project is licensed under the **MIT License** — free to use, modify, and distribute.

See [LICENSE](LICENSE) for details.

<br/>

---

<div align="center">

**MD Mem Clean** — Built with 💙 in pure C

Made by **Mohamed Elba**

*If this saved your RAM, give it a ⭐*

</div>
