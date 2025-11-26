# USB Video Auto-Download & Conversion Tool
## Design Document v2.0

---

## 1. Project Overview

This application automatically detects registered USB devices (cameras, drones, etc.), copies video files from them to a predefined location on your computer, and converts high-resolution videos to more manageable sizes for easier sharing and storage.

### 1.1 Key Features
- **Smart Device Recognition**: Register your USB devices once, and the app will recognize them automatically
- **Automatic File Transfer**: When you connect a registered device, the app starts copying video files immediately
- **Video Conversion**: Automatically converts high-resolution videos (4K) to 1080p Full HD
- **Progress Tracking**: Real-time progress display with detailed information
- **Multi-Device Support**: Handle multiple USB devices simultaneously
- **Organized Storage**: Automatically organizes files in structured folders

---

## 2. How It Works

### 2.1 First-Time Setup
1. Launch the application manually when you want to use it
2. Connect your USB device (camera, drone, etc.)
3. Register the device by giving it a friendly name (e.g., "GoPro Hero 10")
4. Configure your preferences for this device
5. The app remembers this device for future connections

### 2.2 Daily Use
1. Launch the application
2. Connect your registered USB device
3. The app automatically starts copying video files
4. Watch the progress in real-time
5. Receive a notification when everything is complete
6. Your videos are organized and ready to use

---

## 3. Device Management

### 3.1 Device Identification
**How devices are recognized:**
- Each USB device has a unique hardware serial number (similar to a fingerprint)
- The app uses this hardware ID to identify your devices
- This ID remains the same even if you reformat the device
- Devices are tracked by their Vendor ID, Product ID, and Serial Number combination

### 3.2 Registering New Devices
**When you connect an unregistered USB device:**
- The app will show a prompt asking "Would you like to register this device?"
- You can choose to register it or ignore it
- Only registered devices will trigger automatic operations

### 3.3 Device Settings
**For each registered device, you can configure:**
- Friendly name (e.g., "Wedding Camera", "Drone", "Action Cam")
- Which folders to scan for videos
- Whether to convert videos to lower resolution
- Conversion quality preferences
- Whether to automatically start operations when connected

---

## 4. File Detection & Organization

### 4.1 Video File Scanning
**Where the app looks for videos:**
- You can choose to scan the entire device
- Or specify specific folders (e.g., /DCIM/, /Videos/)
- This setting is customizable per device

**Supported video formats:**
- .mp4, .mov, .avi, .mkv, .m4v, .wmv
- Most common camera and drone video formats

### 4.2 Duplicate File Prevention
**How the app avoids copying the same file twice:**
- Creates a unique "fingerprint" (hash) for each file
- Compares against previously downloaded files
- Skips files that have already been copied
- This works even if you rename files later

### 4.3 File Size Filters
**Optional filters to avoid processing unwanted files:**
- Set a minimum file size (e.g., skip clips under 10 MB)
- Set a maximum file size if needed
- Helps avoid copying test clips or corrupted files

### 4.4 Folder Organization
**Your videos are organized as:**
```
Download Folder/
├── [Device Name]/
│   ├── Original/
│   │   └── [Date]/
│   │       ├── video1.mp4
│   │       └── video2.mp4
│   └── Converted/
│       └── [Date]/
│           ├── video1_1080p.mp4
│           └── video2_1080p.mp4
```

**Example:**
```
Videos/USB_Downloads/
├── GoPro_Hero_10/
│   ├── Original/
│   │   └── 2025-11-26/
│   │       ├── GOPR0042.mp4 (4K)
│   │       └── GOPR0043.mp4 (4K)
│   └── Converted/
│       └── 2025-11-26/
│           ├── GOPR0042_1080p.mp4
│           └── GOPR0043_1080p.mp4
```

This structure keeps originals and converted versions separate and organized by date.

### 4.5 File Deletion from USB
**After copying files:**
- Files are **NEVER** deleted from your USB device
- Original files remain on the device for safety
- You can manually delete them later if desired

### 4.6 Insufficient Disk Space
**If your computer doesn't have enough space:**
- The app checks available space before starting
- Shows an error message if insufficient space
- Operation is aborted to prevent partial copies
- You'll need to free up space or choose a different destination

### 4.7 USB Disconnection During Copy
**If the USB device is disconnected while copying:**
- The operation is marked as failed
- Incomplete files are handled safely
- You'll need to reconnect and manually retry
- The app will remember which files were already completed

---

## 5. Video Conversion Details

### 5.1 Target Resolution
**Videos are converted to:**
- 1080p Full HD (1920x1080)
- This reduces file size significantly while maintaining good quality
- Perfect for sharing and everyday viewing

### 5.2 Original File Handling
**After conversion, you can choose:**
- **Keep both**: Original 4K files AND converted 1080p versions
- **Keep only converted**: Delete 4K originals, save only 1080p
- This is configurable per device

### 5.3 Output Format & Codec
**Converted videos are saved as:**
- Same format as the original (if source is .mp4, output is .mp4)
- This maintains compatibility with your existing workflow

### 5.4 Conversion Timing
**When conversion happens:**
- Copying and converting run in parallel
- Files start converting as soon as they're copied
- This saves overall processing time
- Multiple files can be converted simultaneously

### 5.5 Quality vs Speed
**Conversion quality settings:**
- **Balanced** (default): Good quality with reasonable speed
- This setting provides the best compromise for most users
- Other options can be configured in settings

### 5.6 Hardware Acceleration
**GPU-accelerated encoding:**
- The app automatically detects if your computer has a compatible GPU
- Uses GPU acceleration when available for faster conversion
- Falls back to CPU encoding if GPU is not available
- This significantly speeds up the conversion process

---

## 6. Performance & Queue Management

### 6.1 Concurrent Video Conversions
**How many videos convert at once:**
- **1 video at a time** by default (safest option)
- This prevents system overload
- Ensures stable performance
- Can be adjusted based on your computer's capabilities

### 6.2 File Copy Operations
**Simultaneous device transfers:**
- Up to 2-3 devices can transfer files at the same time
- This prevents overloading the USB bus
- Ensures reliable transfer speeds
- Additional devices wait in queue

### 6.3 Device Priority
**When multiple devices are connected:**
- First connected, first processed (FIFO - First In, First Out)
- Simple and predictable behavior
- Each device completes before the next starts

---

## 7. User Interface & Experience

### 7.1 Application Launch
**How you start the app:**
- Manual launch when you need it
- Does NOT start automatically with Windows
- Runs in a full window (not hidden in system tray)

### 7.2 Automatic Operations
**When a registered device is detected:**
- Operations start immediately (fully automatic)
- No need to click "Start" or confirm
- Just connect and go

### 7.3 Progress Information
**What you see during operations:**
- Overall completion percentage
- Current file being processed
- Transfer speed (MB/s)
- Estimated time remaining
- Files completed out of total files
- Separate progress for copying vs converting

**Example display:**
```
Copying: [■■■■■■■■░░░░] 65%
File: GOPR0042.mp4 (3.2GB / 5.0GB)
Speed: 87 MB/s
ETA: 00:02:15
Files: 12 / 30

Converting: [■■■░░░░░░░░░] 25%
File: GOPR0038.mp4
Output: 1080p
ETA: 00:08:45
```

### 7.4 Pause & Cancel Operations
**User control during processing:**
- You can cancel operations mid-process
- However, canceling requires restarting from the beginning
- Partially copied files are removed

### 7.5 Completion Notifications
**When operations finish:**
- Windows notification appears
- Click notification to open the output folder
- Or click to view a detailed log
- This provides interactive access to your files

### 7.6 Error Handling
**When conversion fails:**
- The app logs the error with details
- Continues processing remaining files
- Shows a summary of failures at the end
- You can review the log to see what went wrong

**For network or temporary issues:**
- Failed files are retried automatically (with a limit)
- This handles temporary glitches
- Persistent failures are logged and skipped

---

## 8. Application Settings

### 8.1 General Settings
- **Download Location**: Choose where videos are saved
- **Folder Organization**: Select folder structure pattern
- **Duplicate Detection**: Enable/disable hash-based duplicate checking
- **File Size Filters**: Set minimum/maximum file sizes

### 8.2 Conversion Settings
- **Default Resolution**: 1080p (or custom)
- **Output Format**: MP4, maintain source format, etc.
- **Quality Level**: Fast, Balanced, or High Quality
- **GPU Acceleration**: Auto-detect and use when available
- **Keep Originals**: Choose to keep or delete 4K files after conversion
- **Max Concurrent Conversions**: How many videos to convert at once

### 8.3 Performance Settings
- **Max Simultaneous Transfers**: 2-3 devices at once
- **Queue Priority**: First connected, first processed
- **Disk Space Warning**: Pause if space is low

### 8.4 Notification Settings
- **Completion Notifications**: Enable/disable
- **Sound Alerts**: Play sound when finished
- **Per-File Notifications**: Optional per-file alerts

---

## 9. Device Registration Process

### Step-by-Step Registration

**When you connect a new USB device:**

1. **Detection**
   - App detects the new device
   - Reads device information
   - Shows registration dialog

2. **Device Information Display**
   - Hardware ID
   - Vendor name
   - Model name
   - Storage capacity

3. **Configure Device Settings**
   - Enter a friendly name (e.g., "GoPro Hero 10")
   - Choose scan location:
     - Entire device
     - Specific folder (e.g., /DCIM/100GOPRO/)

4. **Operation Preferences**
   - Auto-start operations when connected
   - Delete files from device after copy (optional)
   - Convert videos to lower resolution

5. **Conversion Preferences** (if enabled)
   - Target resolution (1080p, 720p, etc.)
   - Quality setting (Fast, Balanced, High)

6. **Save & Complete**
   - Device is registered
   - Settings are saved
   - Ready for automatic operations

---

## 10. Operational Workflow Summary

### Complete Process Flow

1. **Application Launch**
   - User starts the application manually
   - System initializes and checks for connected devices

2. **Device Connection**
   - User connects a registered USB device
   - App recognizes the device by its hardware ID

3. **File Scanning**
   - App scans configured folders for video files
   - Checks each file against download history
   - Filters out duplicates and files outside size limits

4. **File Copying**
   - New files are queued for copying
   - Files transfer to the designated folder
   - Progress is displayed in real-time
   - Each file is verified after copying

5. **Video Conversion** (if enabled)
   - Copied files are added to conversion queue
   - Videos are converted based on device settings
   - Progress shown separately from copying
   - Original files kept or deleted based on preference

6. **Completion**
   - All operations finish
   - Windows notification appears
   - Activity is logged
   - User can access files immediately

### Multiple Devices
- When multiple registered devices are connected
- Each device is processed in the order connected
- Up to 3 devices can copy files simultaneously
- Conversions are managed based on system resources
- Progress shown for each device separately

---

## 11. System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Main Window (WPF)                       │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────┐        │
│  │  Device     │  │   Progress   │  │   Settings   │        │
│  │  List Panel │  │   Dashboard  │  │     Panel    │        │
│  └─────────────┘  └──────────────┘  └──────────────┘        │
└─────────────────┬───────────────────────────────────────────┘
                  │
    ┌─────────────┴─────────────┬─────────────────┬───────────────┐
    │                           │                 │               │
┌───▼──────────────┐   ┌────────▼────────┐   ┌────▼──────────┐    │
│  USB Monitor     │   │  File Copy      │   │  Conversion   │    │
│  Service         │   │  Queue Manager  │   │  Engine       │    │
│  (Background)    │   │  (Thread Pool)  │   │  (Thread Pool)│    │
│                  │   │                 │   │               │    │
│  - WMI Events    │   │  - Copy Jobs    │   │  - FFmpeg     │    │
│  - Device DB     │   │  - Hash Check   │   │  - GPU Accel  │    │
│  - Registration  │   │  - Retry Logic  │   │  - Queue Mgmt │    │
└──────────────────┘   └─────────────────┘   └───────────────┘    │
                                                                  │
    ┌─────────────────────────────────────────────────────────────┘
    │
┌───▼────────────────────────────────────────┐
│      SQLite Database                       │
│  ┌──────────────┬──────────────┬─────────┐ │
│  │   Devices    │   History    │  Logs   │ │
│  │  Table       │   Table      │  Table  │ │
│  └──────────────┴──────────────┴─────────┘ │
└────────────────────────────────────────────┘
```

### 11.1 Component Responsibilities

**USB Monitor Service:**
- Continuously monitors for USB device connections/disconnections using WMI events
- Queries device hardware identifiers (Vendor ID, Product ID, Serial Number)
- Checks against registered device database
- Triggers file scanning for registered devices
- Handles device queue management

**File Copy Queue Manager:**
- Manages copying operations for multiple devices using thread pool
- Implements SHA-256 hash-based duplicate detection
- Handles concurrent transfer limits (2-3 simultaneous transfers)
- Provides progress tracking callbacks to UI
- Implements retry logic for failed copies
- Performs atomic file operations (temp file → rename on success)

**Conversion Engine:**
- Manages FFmpeg conversion queue with thread pool
- Limits concurrent conversions based on system resources (default: 1)
- Monitors CPU/GPU usage
- Provides progress tracking per file
- Handles conversion failure recovery
- Supports GPU hardware acceleration (auto-detect)

**Database Layer (SQLite):**
- Stores registered device identifiers and preferences
- Tracks file download history (SHA-256 hashes, dates, file paths)
- Logs all operations for troubleshooting
- Maintains device-specific settings
- Transaction-based updates for data integrity

---

## 12. User Interface Design

### 12.1 Main Window Layout

```
╔═══════════════════════════════════════════════════════════════════╗
║  USB Video Auto-Download Tool                        [_] [□] [X]  ║
╠═══════════════════════════════════════════════════════════════════╣
║  ┌────────────────────┐  ┌─────────────────────────────────────┐  ║
║  │ REGISTERED DEVICES │  │      ACTIVE OPERATIONS              │  ║
║  ├────────────────────┤  ├─────────────────────────────────────┤  ║
║  │ ☑ GoPro Hero 10    │  │ [■■■■■■■■░░░░░░░░] 45% - Copying    │  ║
║  │   Status: Active   │  │  Device: GoPro Hero 10              │  ║
║  │   Files: 12/30     │  │  File: vid_0042.mp4 (2.4GB/5.3GB)   │  ║
║  │                    │  │  Speed: 87 MB/s  ETA: 00:03:42      │  ║
║  │ ☐ DJI Drone        │  ├─────────────────────────────────────┤  ║
║  │   Status: Idle     │  │ [■■░░░░░░░░░░░░░░] 12% - Converting │  ║
║  │   Last: 2 days ago │  │  Device: DJI Drone                  │  ║
║  │                    │  │  Output: 1080p H.264                │  ║
║  │ ☐ Canon EOS R5     │  │  File: DJI_0023.mp4  ETA: 00:12:15  │  ║
║  │   Status: Idle     │  ├─────────────────────────────────────┤  ║
║  │                    │  │ Queue: 2 devices waiting            │  ║
║  │ [+ Register New]   │  │                                     │  ║
║  │ [  Edit Selected]  │  │ [⏸ Pause All] [⏹ Stop All]          │  ║
║  │ [  Remove Device]  │  │                                     │  ║
║  └────────────────────┘  └─────────────────────────────────────┘  ║
║                                                                   ║
║  ┌──────────────────────────────────────────────────────────────┐ ║
║  │ RECENT ACTIVITY LOG                                          │ ║
║  ├──────────────────────────────────────────────────────────────┤ ║
║  │ 14:23:45 - GoPro Hero 10: Started copying 30 files           │ ║
║  │ 14:22:10 - DJI Drone: Conversion completed (15 files)        │ ║
║  │ 14:18:33 - Canon EOS R5: Device disconnected                 │ ║
║  │ 14:15:02 - System: Application started                       │ ║
║  └──────────────────────────────────────────────────────────────┘ ║
║                                                                   ║
║  [⚙ Settings] [📊 Statistics] [📁 Open Output Folder] [❓ Help]  ║
╚═══════════════════════════════════════════════════════════════════╝
```

### 12.2 Device Registration Dialog

```
╔═══════════════════════════════════════════════════╗
║  Register New USB Device                   [X]    ║
╠═══════════════════════════════════════════════════╣
║                                                   ║
║  Device Detected:                                 ║
║  ┌─────────────────────────────────────────────┐  ║
║  │ Hardware ID: 058F:6387:1234567890ABCD       │  ║
║  │ Vendor: Generic USB Storage                 │  ║
║  │ Model: Mass Storage Device                  │  ║
║  │ Capacity: 128 GB                            │  ║
║  └─────────────────────────────────────────────┘  ║
║                                                   ║
║  Friendly Name:                                   ║
║  ┌─────────────────────────────────────────────┐  ║
║  │ GoPro Hero 10                               │  ║
║  └─────────────────────────────────────────────┘  ║
║                                                   ║
║  Scan Location:                                   ║
║  ┌─────────────────────────────────────────────┐  ║
║  │ ● Entire Device                             │  ║
║  │ ○ Specific Folder: [/DCIM/100GOPRO/    📁]  │  ║
║  └─────────────────────────────────────────────┘  ║
║                                                   ║
║  Options:                                         ║
║  ☑ Auto-start operations when connected           ║
║  ☐ Delete files from device after copy            ║
║  ☑ Convert videos to lower resolution             ║
║                                                   ║
║  Conversion Settings:                             ║
║  Target Resolution: [1080p      ▼]                ║
║  Quality:          [Balanced    ▼]                ║
║                                                   ║
║         [  Cancel  ]        [  Register  ]        ║
╚═══════════════════════════════════════════════════╝
```

### 12.3 Settings Panel

```
╔═══════════════════════════════════════════════════════════╗
║  Application Settings                            [X]      ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║  📁 File Management                                       ║
║  ┌───────────────────────────────────────────────────┐    ║
║  │ Default Download Folder:                          │    ║
║  │ [C:\Users\User\Videos\USB_Downloads        ] [📁] │    ║
║  │                                                   │    ║
║  │ Folder Organization:                              │    ║
║  │ [[DeviceName]/Original/[Date]+Converted/[Date]▼]  │    ║
║  │                                                   │    ║
║  │ ☑ Check for duplicate files (hash-based)          │    ║
║  │ ☑ Skip files under 10 MB                          │    ║
║  └───────────────────────────────────────────────────┘    ║
║                                                           ║
║  🎬 Video Conversion                                      ║
║  ┌───────────────────────────────────────────────────┐    ║
║  │ Default Target Resolution: [1080p          ▼]     │    ║
║  │ Output Format:             [MP4 (H.264)    ▼]     │    ║
║  │ Encoding Speed:            [Balanced       ▼]     │    ║
║  │                                                   │    ║
║  │ ☑ Use GPU acceleration when available             │    ║
║  │ ☑ Keep original 4K files                          │    ║
║  │                                                   │    ║
║  │ Max Concurrent Conversions: [2              ▼]    │    ║
║  └───────────────────────────────────────────────────┘    ║
║                                                           ║
║  ⚙️ Performance                                           ║
║  ┌───────────────────────────────────────────────────┐    ║
║  │ Max Simultaneous Transfers:    [3           ▼]    │    ║
║  │ Device Queue Priority:  [First Connected   ▼]     │    ║
║  │                                                   │    ║
║  │ ☑ Pause operations if disk space < 50 GB          │    ║
║  └───────────────────────────────────────────────────┘    ║
║                                                           ║
║  🔔 Notifications                                         ║
║  ┌───────────────────────────────────────────────────┐    ║
║  │ ☑ Show notification when operations complete      │    ║
║  │ ☑ Play sound on completion                        │    ║
║  │ ☐ Show notification for each file converted       │    ║
║  └───────────────────────────────────────────────────┘    ║
║                                                           ║
║              [  Restore Defaults  ]  [  Save  ]           ║
╚═══════════════════════════════════════════════════════════╝
```

---

## 13. Technical Workflows

### 13.1 Device Registration Flow

```
1. User connects new USB device
   ↓
2. App detects connection (WMI event)
   ↓
3. App reads hardware identifier (VID:PID:Serial)
   ↓
4. Check if device is registered in SQLite database
   ↓
5. If NOT registered → Show registration dialog
   ↓
6. User enters friendly name and preferences
   ↓
7. Save to Devices table with unique ID
   ↓
8. Device now ready for automatic operations
```

### 13.2 Automatic Processing Flow

```
1. Registered USB device connected
   ↓
2. USB Monitor detects via WMI and identifies device
   ↓
3. Query device settings from Devices table
   ↓
4. Scan device for video files (based on scan path setting)
   ↓
5. Calculate SHA-256 hash for each file
   ↓
6. Check each file hash against FileHistory table
   ↓
7. Filter out duplicates
   ↓
8. Add new files to Copy Queue with priority
   ↓
9. Copy Queue Manager starts transfer (thread pool)
   │
   ├─→ Progress updates to UI via callbacks
   │
   ├─→ Atomic file operation (copy to temp → rename)
   │
   └─→ On completion: Save hash to FileHistory table
   ↓
10. If conversion enabled for device:
   │
   ├─→ Add copied files to Conversion Queue
   │
   └─→ Conversion Engine processes with FFmpeg
       │
       ├─→ Use GPU acceleration if available
       │
       ├─→ Apply quality settings (Balanced default)
       │
       ├─→ Progress updates to UI
       │
       └─→ Update FileHistory with conversion status
   ↓
11. All operations complete
    ↓
12. Show Windows notification (interactive)
    ↓
13. Log completion in Logs table
```

### 13.3 Multiple Device Handling

```
Device A (Priority 1) ─┐
Device B (Priority 2) ─┼─→ Device Queue (FIFO)
Device C (Priority 3) ─┘
                        ↓
                   Copy Manager
                   (Max 3 concurrent - thread pool)
                        ↓
         ┌──────────────┼──────────────┐
    Device A        Device B        Device C
    Copying         Copying         Waiting
         │              │                │
         └──────────────┴────────────────┘
                        ↓
                 Conversion Queue
                 (Max 1 concurrent - configurable)
                        ↓
                   Converting
                   (Device A)
```

---

## 14. Error Handling & Edge Cases

### 14.1 Error Scenarios

| Scenario | Detection | Handling |
|----------|-----------|----------|
| Insufficient disk space | Pre-flight check before copy | Alert user, abort operation |
| USB disconnection mid-copy | File operation exception | Mark as incomplete, cleanup temp files |
| Video file corruption | FFmpeg conversion failure | Log error, skip file, continue with others |
| Duplicate hardware ID | Device registration | Warn user, prevent registration or overwrite existing |
| FFmpeg not found | Application startup | Show error, prompt to download/install |
| Database locked | Any DB operation | Retry with exponential backoff (3 attempts) |
| No video files found | Scan completion | Show info message, log event |
| Conversion exceeds time limit | Timeout monitor | Cancel conversion, log error, mark as failed |

### 14.2 Safety Features

1. **Pre-flight Checks:**
   - Verify destination folder exists and is writable
   - Check available disk space (warn if < 2x largest file)
   - Verify FFmpeg installation and functionality
   - Validate device hardware ID format

2. **Operation Safeguards:**
   - SHA-256 hash-based duplicate detection prevents re-processing
   - Atomic file operations (temp file → rename on success)
   - Transaction-based database updates
   - Graceful cancellation (cleanup temp files)

3. **Data Integrity:**
   - Calculate SHA-256 hash before and after copy
   - Verify file size matches before processing
   - Keep original files until conversion succeeds
   - Maintain operation logs for auditing

4. **Resource Protection:**
   - Monitor CPU/GPU temperature (optional)
   - Limit concurrent operations based on system resources
   - Implement operation timeouts
   - Queue overflow protection (max 100 pending items)

---

## 15. Database Schema

### 15.1 Devices Table
```sql
CREATE TABLE Devices (
    DeviceID INTEGER PRIMARY KEY AUTOINCREMENT,
    HardwareID TEXT UNIQUE NOT NULL,        -- VID:PID:Serial
    FriendlyName TEXT NOT NULL,
    VendorName TEXT,
    ModelName TEXT,
    ScanPath TEXT,                          -- NULL = entire device
    AutoStart BOOLEAN DEFAULT 1,
    DeleteAfterCopy BOOLEAN DEFAULT 0,
    ConversionEnabled BOOLEAN DEFAULT 1,
    TargetResolution TEXT DEFAULT '1080p',
    Quality TEXT DEFAULT 'Balanced',
    KeepOriginals BOOLEAN DEFAULT 1,
    DateRegistered DATETIME DEFAULT CURRENT_TIMESTAMP,
    LastConnected DATETIME
);
```

### 15.2 FileHistory Table
```sql
CREATE TABLE FileHistory (
    FileID INTEGER PRIMARY KEY AUTOINCREMENT,
    DeviceID INTEGER NOT NULL,
    FileName TEXT NOT NULL,
    FileHash TEXT NOT NULL,                 -- SHA-256
    FileSize INTEGER,
    SourcePath TEXT,
    DestinationPath TEXT,
    ConversionStatus TEXT,                  -- None, InProgress, Completed, Failed
    ConvertedPath TEXT,
    DateCopied DATETIME DEFAULT CURRENT_TIMESTAMP,
    DateConverted DATETIME,
    FOREIGN KEY (DeviceID) REFERENCES Devices(DeviceID),
    UNIQUE(FileHash)                        -- Prevent duplicate processing
);
```

### 15.3 Logs Table
```sql
CREATE TABLE Logs (
    LogID INTEGER PRIMARY KEY AUTOINCREMENT,
    Timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    Level TEXT,                             -- Info, Warning, Error
    Component TEXT,                         -- USBMonitor, CopyManager, ConversionEngine
    DeviceID INTEGER,
    Message TEXT,
    Details TEXT,
    FOREIGN KEY (DeviceID) REFERENCES Devices(DeviceID)
);
```

---

## 16. Technical Requirements

### 16.1 Technology Stack

**Framework & Runtime:**
- .NET 6.0 or higher
- WPF (Windows Presentation Foundation) for UI
- C# programming language

**Core Libraries:**
- System.Management (WMI for USB device monitoring)
- System.IO for file operations
- System.Security.Cryptography (SHA-256 hashing)
- System.Data.SQLite for database
- System.Threading.Tasks (async/await patterns)

**External Dependencies:**
- FFmpeg (video conversion engine)
- FFmpeg.NET wrapper or direct process invocation
- GPU acceleration: NVENC (NVIDIA), QuickSync (Intel), AMF (AMD)

**Database:**
- SQLite 3.x (embedded, serverless)

### 16.2 System Requirements

**Minimum:**
- Windows 10 (64-bit) or later
- 4 GB RAM
- 2 GB free disk space (for application + temporary files)
- USB 2.0 ports
- .NET 6.0 Runtime

**Recommended:**
- Windows 11 (64-bit)
- 8 GB+ RAM
- 50 GB+ free disk space (for video storage)
- USB 3.0+ ports
- Dedicated GPU with hardware encoding support

---

## 17. Safety & Reliability Features

### 11.1 Data Protection
- **Duplicate Prevention**: Hash-based checking prevents re-copying files
- **File Verification**: Files are checked after copying to ensure integrity
- **No Automatic Deletion**: Original files remain on your device
- **Organized Backups**: Both original and converted versions are kept (if configured)

### 11.2 Error Recovery
- **Disk Space Checks**: Verifies sufficient space before starting
- **Connection Monitoring**: Detects if device is disconnected
- **Graceful Failures**: Failed operations don't affect other files
- **Detailed Logging**: All operations are logged for troubleshooting

### 11.3 System Protection
- **Resource Management**: Limits concurrent operations to prevent overload
- **Queue Limits**: Prevents excessive memory usage
- **Timeout Protection**: Long-running operations have time limits
- **Safe Cancellation**: User can stop operations cleanly

---

## 12. System Requirements

### Minimum Requirements
- **Operating System**: Windows 10 (64-bit) or later
- **Memory**: 4 GB RAM
- **Storage**: 2 GB free disk space for application
- **USB Ports**: USB 2.0 or higher
- **Additional**: .NET 6.0 Runtime (installed automatically if needed)

### Recommended for Best Performance
- **Operating System**: Windows 11 (64-bit)
- **Memory**: 8 GB+ RAM
- **Storage**: 50 GB+ free disk space for video storage
- **USB Ports**: USB 3.0 or higher for faster transfers
- **Graphics**: Dedicated GPU with hardware encoding support for faster conversion

---

## 13. Privacy & Security

### Data Privacy
- **Local Only**: All operations happen on your computer
- **No Internet Required**: No data is sent to external servers
- **No Cloud Upload**: Files stay on your local storage
- **No Telemetry**: The app doesn't track or report usage

### Security
- **Safe Operations**: No files from USB devices are executed
- **Permission Control**: Only requests necessary system permissions
- **Device Security**: Database can be optionally encrypted
- **Secure Deletion**: If deletion is enabled, files are securely overwritten

---

## 14. Common Scenarios

### Scenario 1: Vacation Videos
**Situation**: You return from vacation with your GoPro full of 4K videos
**Workflow**:
1. Launch the app
2. Connect your GoPro
3. App automatically copies all new videos
4. Converts them to 1080p for easy sharing
5. Notification when complete
6. Share the 1080p versions with family while keeping 4K originals

### Scenario 2: Drone Footage
**Situation**: Regular drone operator with frequent video downloads
**Workflow**:
1. Register your drone once
2. Each time you connect, videos copy automatically
3. Files organized by date
4. Easy to find recent flights
5. Converted versions ready for social media

### Scenario 3: Multiple Cameras
**Situation**: Event videographer with multiple cameras
**Workflow**:
1. Register all camera devices
2. Connect all cameras after an event
3. App processes each camera in order
4. All footage organized by device and date
5. Converted versions ready for editing

### Scenario 4: Storage Management
**Situation**: Limited storage space on computer
**Workflow**:
1. Configure app to delete 4K originals after conversion
2. Keep only 1080p versions
3. Saves significant disk space
4. Original 4K files remain safe on USB device until you're ready to delete

---

## 15. Frequently Asked Questions

**Q: What happens if I disconnect the USB device during copying?**
A: The operation stops and is marked as failed. When you reconnect, you'll need to restart, but already-completed files won't be copied again.

**Q: Can I use the app while it's processing videos?**
A: Yes, the app continues working in the background. You can minimize it and do other work.

**Q: How much disk space do I need?**
A: At minimum, you need enough space for the videos you're copying plus the converted versions. The app checks before starting and warns you if space is insufficient.

**Q: Will this work with SD card readers?**
A: Yes, USB card readers are treated as USB devices and work the same way.

**Q: Can I change settings for a registered device later?**
A: Yes, you can edit any registered device's settings at any time.

**Q: What if I reformat my USB device?**
A: The app will still recognize it by hardware ID. Previously downloaded files won't be copied again.

**Q: Does the app run in the background all the time?**
A: No, you launch it manually when needed. It doesn't start with Windows.

**Q: Can I process videos without conversion?**
A: Yes, conversion is optional. You can disable it per device or globally.

**Q: What happens to corrupted video files?**
A: Corrupted files that fail conversion are logged and skipped. Other files continue processing normally.

**Q: Can I change where videos are saved?**
A: Yes, you can change the default download folder in settings at any time.

---

## 16. Future Possibilities

The following features could be considered for future versions:

- **Cloud Integration**: Direct upload to OneDrive, Google Drive, or Dropbox
- **Network Storage**: Support for NAS (Network Attached Storage) devices
- **Video Editing**: Basic editing features like trimming and merging
- **Thumbnail Generation**: Preview images for quick video identification
- **Batch Rename**: Rename multiple files based on patterns
- **Smart Organization**: Automatic categorization by date, location (GPS data)
- **Mobile Notifications**: Notify your phone when operations complete
- **Multi-Language**: Support for different languages
- **Portable Version**: Run without installation from USB drive
- **Command-Line Mode**: Automation for advanced users
- **Video Editor Integration**: Direct export to editing software

---

## 17. Document Information

### Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-11-05 | Initial design document with outstanding questions |
| 2.0 | 2025-11-26 | Refined requirements based on confirmed answers |

### Document Purpose

This design document outlines the functional requirements and user experience for the USB Video Auto-Download & Conversion Tool. It serves as a communication tool between the client and development team to ensure clear understanding of project scope and features.

### Next Steps

With requirements now clarified, the next phases are:

1. **Technical Design**: Create detailed technical architecture
2. **Development Planning**: Break down features into development milestones
3. **UI/UX Design**: Create detailed interface mockups
4. **Development**: Begin implementation
5. **Testing**: Quality assurance and user acceptance testing
6. **Deployment**: Final release and documentation

---

**END OF DESIGN DOCUMENT v2.0**
