# 🗂️ Backup Software

An automated, scheduled file backup tool for Windows. It copies modified files from a source directory to a timestamped backup folder at a user-defined time every day — running silently in the background.

---

## 📋 Features

- **Scheduled daily backups** — set a specific time (HH:MM:SS) for the backup to run automatically
- **Incremental backup logic** — on subsequent runs, only files modified since the last backup are copied
- **Recursive directory traversal** — handles nested folders, preserving the full directory structure
- **Folder timestamp preservation** — copied folders retain their original creation/modification timestamps
- **GUI configuration panel** — a Python/Tkinter interface to set source path, destination path, and backup time
- **Auto-restart on config change** — if parameters change, the backup process is automatically restarted
- **Zero CPU usage while waiting** — sleeps for the exact remaining seconds until backup time using `sleep()`
- **Shutdown/crash recovery** — if the system shuts down mid-backup, the next run resumes from the incomplete backup

---

## 🗃️ Project Structure

```
Backup-Software/
│
├── Backup Software.c    # Core backup engine (C) — handles scheduling, file copying, and incremental logic
├── set parameters.py    # GUI tool (Python/Tkinter) to configure backup settings
├── .gitignore           # Excludes binaries and runtime files
└── LICENSE              # MIT License
```

> To use the software, build from source using the instructions below.

---

## ⚙️ How It Works

1. **Configuration** — Run `set parameters.py` to set:
   - Source folder to back up
   - Destination folder for backups
   - Daily backup time (hour : minute : second)

   Settings are saved to a local `info.txt` file used by the backup engine.

2. **Backup Engine** — compile and run `Backup Software.c`:
   - Calculates the exact number of seconds until the configured backup time and calls `sleep()` for precisely that duration — **zero CPU usage while waiting**
   - On the first run: performs a **full backup** of the source folder into a new timestamped subfolder (`/CFR DD-MM-YYYY`)
   - On subsequent runs: performs an **incremental backup**, copying only files modified since the last backup date
   - **Shutdown/crash recovery** — if the system shuts down or the process is killed mid-backup, the next run resumes from the incomplete backup folder rather than starting over, ensuring no work is lost
   - After each backup cycle, sleeps again for the next 24 hours
   - Empty backup folders are automatically cleaned up

---

## 🚀 Getting Started

### Prerequisites

- **Windows OS** (the backup engine uses Windows APIs — `CreateFile`, `SetFileTime`, `windows.h`)
- **GCC with MinGW** — to compile the backup engine (e.g., via [MSYS2](https://www.msys2.org/))
- **Python 3.x** — to run the configuration GUI
  - Required library: `tkinter` (included with standard Python on Windows)

### Build & Run

**Step 1 — Compile the backup engine:**

```bash
gcc "Backup Software.c" -o "Backup Software.exe" -lwindows
```

**Step 2 — Run the configuration GUI:**

```bash
python "set parameters.py"
```

**Step 3 — Configure your backup:**

1. Select your **Source Path** and **Destination Path** using the folder browser.
2. Set your preferred **backup time** using the dropdowns.
3. Click **Set** — the backup engine will launch automatically.
4. Keep **`Backup Software.exe`** running in the background. It will back up your files daily at the configured time.

> **Note:** Both files must be in the same folder. `info.txt` is auto-created there on first run.

---

## 📝 Configuration File (`info.txt`)

The `info.txt` file is auto-generated and stores the backup state:

```
HH:MM:SS           ← Scheduled backup time
<prev_backup_path> ← Path of the last backup folder (for incremental logic)
<source_path>      ← Source directory
<destination_path> ← Destination directory
```

> Do not manually edit this file unless you know what you're doing.

---

## ⚠️ Limitations

- Windows only (uses `windows.h`, `conio.h`, and Windows shell commands internally)
- Paths are limited to 200–300 characters due to fixed buffer sizes in the C code
- The backup engine must remain running for scheduled backups to trigger

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you'd like to change.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).