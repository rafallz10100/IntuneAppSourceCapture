# 🧰 Intune App Source Capture


<p align="center">
  <img alt="PowerShell" src="https://img.shields.io/badge/PowerShell-5.1%2B-5391FE?logo=powershell&logoColor=white">
  <img alt="Windows" src="https://img.shields.io/badge/Windows-10%2F11-0078D4?logo=windows&logoColor=white">
  <img alt="Microsoft Intune" src="https://img.shields.io/badge/Microsoft%20Intune-Win32%20Apps-2560E0?logo=microsoft&logoColor=white">
  <img alt="GUI" src="https://img.shields.io/badge/GUI-Windows%20Forms-6A5ACD">
  <img alt="Admin Required" src="https://img.shields.io/badge/Admin-required-red">
  <img alt="License" src="https://img.shields.io/badge/License-MIT-green">
</p>

PowerShell GUI tool for recovering Win32 app payloads temporarily staged by the Microsoft Intune Management Extension during installation from Company Portal.

The script monitors the Intune Management Extension staging directory, copies detected ZIP payloads before they are removed, extracts them, and optionally copies the matching `IMECache` folder.

> Intended for administrators, troubleshooting, migration, recovery, lab testing, and authorized internal use only.

## ✨ Features

- 🖥️ Windows Forms graphical interface
- 📦 Captures staged Intune Win32 app ZIP payloads
- 🗂️ Extracts captured ZIP files automatically
- 💾 Copies related `C:\Windows\IMECache` content when available
- ⏱️ Configurable monitoring duration, polling interval, copy retry time, and IMECache wait time
- 📊 Progress bar and live log output
- ▶️ Start / Stop controls
- 📁 Output folder picker
- 🧰 Optional fallback to 7-Zip when `Expand-Archive` fails

## ⚙️ How it works

When a Win32 app is installed from Company Portal, Intune Management Extension temporarily stages installation content under:

```text
C:\Program Files (x86)\Microsoft Intune Management Extension\Content\Staging
```

This tool watches that location for `.zip` files. When a ZIP appears, it attempts to copy it quickly to the selected output directory, extracts it, and then checks for a related folder under:

```text
C:\Windows\IMECache
```

The captured data is written into three subfolders:

```text
<OutputDirectory>\StagingZip
<OutputDirectory>\Extracted
<OutputDirectory>\IMECache
```

## 📋 Requirements

- Windows
- PowerShell 5.1 or newer
- Microsoft Intune Management Extension installed
- Administrator privileges
- Company Portal access to trigger the Win32 app installation
- Optional: 7-Zip installed in one of the default locations:
  - `C:\Program Files\7-Zip\7z.exe`
  - `C:\Program Files (x86)\7-Zip\7z.exe`

## 🚀 Usage

1. Run PowerShell as Administrator.
2. Start the script:

```powershell
.\IntuneAppSourceCapture.ps1
```

3. In the GUI, select an output directory.
4. Adjust the timing values if needed.
5. Click **Start**.
6. Open Company Portal and click **Install** for the target Win32 app.
7. Wait until the tool detects and copies the staged ZIP file.
8. Review the output folders:

```text
StagingZip   - raw captured ZIP files
Extracted    - extracted ZIP contents
IMECache     - copied IMECache folders, when available
```

## 🖥️ GUI options

| Option | Default | Description |
| --- | ---: | --- |
| Seconds | `300` | Total monitoring time. |
| Pool (ms) | `100` | Polling interval in milliseconds. |
| Copy Retry (sec) | `10` | Time window used to retry copying a detected ZIP. |
| Wait IME Cache (sec) | `30` | Time to wait for a matching IMECache folder after a ZIP is detected. |

> Note: The GUI label says `Pool (ms)`, but it behaves as a polling interval.

## 📁 Output structure

Example output after a successful capture:

```text
C:\Temp\IntuneCapture
├── StagingZip
│   └── <captured-file>.zip
├── Extracted
│   └── <captured-file>
│       └── <extracted app content>
└── IMECache
    └── <matching-cache-folder>
```

## 🛠️ Troubleshooting

### `Run app as admin`

The tool must be started with administrative privileges because it reads protected Intune and Windows cache locations.

### `Staging folder not found`

The Intune Management Extension may not be installed, or the device may not be managed by Intune. Confirm that this path exists:

```text
C:\Program Files (x86)\Microsoft Intune Management Extension\Content\Staging
```

### ZIP was not captured

The staging file may have been removed before it could be copied. Try increasing:

- `Seconds`
- `Copy Retry (sec)`
- lowering `Pool (ms)`

Then start capture before clicking **Install** in Company Portal.

### ZIP captured but not extracted

The script first uses PowerShell `Expand-Archive`. If that fails, it tries 7-Zip from the default installation paths. Install 7-Zip or manually extract the file from `StagingZip`.

### IMECache was not copied

This is not always a failure. The script logs that the ZIP is usually enough. IMECache may appear later, may be locked, or may not be created for every package in the same way.

## 🔐 Security and legal notice

Use this tool only on devices and applications you are authorized to manage or troubleshoot. Captured payloads may contain proprietary software, credentials, scripts, configuration files, certificates, or other sensitive data. Store output securely and delete it when no longer needed.

## ⚠️ Limitations

- Works only while Intune Management Extension is actively staging content.
- Timing matters; some payloads may be removed very quickly.
- Requires local administrator rights.
- Designed for Windows desktop usage with a GUI.
- Does not download content from Intune directly; it only captures local staged files.

## 📄 License

MIT License

Copyright (c) 2026 Rafal Zimonczyk

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files, to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
