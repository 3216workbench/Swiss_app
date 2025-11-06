# USB Video Auto-Download & Conversion Tool
## Design Document v1.0

---

## 1. Core Requirements (Confirmed)

### 1.1 USB Device Management
- **Device Registration**: Users connect USB devices and register them by saving their unique identifier
- **Whitelist Approach**: Only pre-registered devices will trigger automatic operations
- **Device Memory**: Persistent storage of registered device identifiers and preferences

### 1.2 Application Behavior
- **Launch**: Manual start (user-initiated, not automatic with Windows)
- **Interface**: Full window application (not system tray minimized)
- **Progress**: Real-time progress bars during operations
- **Notifications**: System notification when operations complete

### 1.3 Processing Queue
- **Multiple Devices**: Support for multiple simultaneously connected USB devices
- **Queue Management**: Process devices in queue order if multiple connected
- **Conversion Limiting**: Throttle video conversion based on system resources
- **Concurrent Operations**: Balance between file copying and video conversion

### 1.4 File Organization
- **Base Folder**: User-defined predefined download location
- **Subfolder Structure**: Automatic creation of organized subdirectories

---

## 2. Outstanding Questions

### 2.1 USB DEVICE IDENTIFICATION

**Q1: What identifier should we use for device registration?**
Options:
- B) Hardware Serial Number (USB device hardware ID)
- C) Combination of Vendor ID, Product ID, Serial
- D) User-assigned friendly name + drive letter tracking

**RECOMMENDATION:** Option B or C for true device tracking (remains same even after reformatting)

**Q2: When a non-registered USB device is connected, should:**
- A) Show a prompt asking "Register this device?"

### 2.2 VIDEO DETECTION & SELECTION

**Q3: Should the app scan:**
- B) Only specific folders (e.g., /DCIM/, /Videos/)
- C) User-configurable per device

**Q4: Video formats to support?**
Common: .mp4, .mov, .avi, .mkv, .m4v, .wmv
Should we support ALL or specific formats?

**Q5: Duplicate file handling:**
- A) Skip files already downloaded (check by hash)
- B) Skip files with same name in destination
- C) Always copy, append number to duplicates
! D) Ask user each time

**RECOMMENDATION:** Option A (hash-based) for reliability

**Q6: File size filters needed?**
- Minimum size to avoid copying tiny clips?
- Maximum size limit?

### 2.3 FOLDER STRUCTURE & FILE ORGANIZATION

**Q7: Subfolder organization pattern?**
- A) [Device_Name]/[Date]/files


**RECOMMENDATION:** Option D - separates originals & converted

**Q8: Should files be deleted from USB after successful copy?**
- A) Never delete (keep originals on device)


**Q9: What happens if destination disk space is insufficient?**
- A) Show error and abort


**Q10: If USB disconnects during copy, should we:**

- C) Mark as failed, require manual retry

### 2.4 VIDEO CONVERSION SPECIFICATIONS

**Q11: Target resolution(s) for conversion?**
- A) 1080p (1920x1080) Full HD


**Q12: File handling after conversion:**
X A) Keep BOTH original 4K + converted version(s)
X B) Keep ONLY converted version(s), delete 4K original
- C) User choice per device configuration

**Q13: Output video format & codec?**
- A) Same as source (maintain format)


**RECOMMENDATION:** Option B for compatibility, or C if file size is critical (note: H.265 slower to encode)

**Q14: Conversion timing:**

- C) Parallel: copy + convert simultaneously

**RECOMMENDATION:** Option B or C for efficiency

**Q15: Encoding quality vs speed preference?**
- A) Fast encoding (lower quality, quick completion)
X B) Balanced (moderate quality & speed)
X C) High quality (slower, best output)
- D) User configurable per device

**Q16: Use hardware acceleration (GPU encoding)?**

- C) Auto-detect and prefer GPU

**RECOMMENDATION:** Option C - GPU when available for speed


--------------------------------------------------------

### 2.5 RESOURCE MANAGEMENT & PERFORMANCE

**Q17: Maximum concurrent video conversions?**
- A) 1 at a time (safest, slowest)
- B) 2 concurrent conversions
- C) Based on CPU cores (e.g., cores/2)
- D) User configurable limit

**RECOMMENDATION:** Option C with max cap of 4

**Q18: File copy operations - simultaneous transfers?**
- A) One device at a time (sequential)
- B) All devices simultaneously
- C) Limit to 2-3 concurrent transfers

**RECOMMENDATION:** Option C to avoid USB bus saturation

**Q19: Priority system when queue is full?**
- A) First connected, first processed (FIFO)
- B) User can manually set priority
- C) Smaller jobs first (shortest job first)

### 2.6 USER INTERFACE & INTERACTION

**Q20: When registered USB device is detected:**
- A) Start operations immediately (fully automatic)
- B) Show preview of files, wait for user confirmation
- C) Configurable per device

**Q21: Progress indication - what details to show?**
- A) Overall completion percentage
- B) Current file being processed
- C) Transfer speed (MB/s)
- D) Estimated time remaining
- E) Files completed / total files
- F) Separate progress for copy vs convert
- G) All of the above (recommended)

**Q22: Can user cancel/pause operations mid-process?**
- A) Yes, with option to resume later
- B) Yes, but must restart from beginning
- C) No, must complete once started

**Q23: Completion notification content?**
- A) Simple: "USB device processing complete"
- B) Detailed: file count, sizes, time taken
- C) Interactive: click to open folder or view log

**RECOMMENDATION:** Option C for best user experience

**Q24: Error handling - what if conversion fails?**
- A) Skip failed files, continue with others
- B) Retry failed files (with limit)
- C) Pause and ask user how to proceed
- D) Log errors, continue, show summary at end

**RECOMMENDATION:** Option D with option B for network issues

---

## 3. System Architecture

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

### 3.1 Component Responsibilities

**USB Monitor Service:**
- Continuously monitors for USB device connections/disconnections
- Queries device hardware identifiers
- Checks against registered device database
- Triggers file scanning for registered devices
- Handles device queue management

**File Copy Queue Manager:**
- Manages copying operations for multiple devices
- Implements hash-based duplicate detection
- Handles concurrent transfer limits
- Provides progress tracking callbacks
- Implements retry logic for failed copies

**Conversion Engine:**
- Manages FFmpeg conversion queue
- Limits concurrent conversions based on system resources
- Monitors CPU/GPU usage
- Provides progress tracking per file
- Handles conversion failure recovery

**Database Layer:**
- Stores registered device identifiers and preferences
- Tracks file download history (hashes, dates)
- Logs all operations for troubleshooting
- Maintains device-specific settings

---

## 4. User Interface Design

### 4.1 Main Window Layout

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

### 4.2 Device Registration Dialog

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

### 4.3 Settings Panel

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

## 5. Operational Workflow

### 5.1 Device Registration Flow

```
1. User connects new USB device
   ↓
2. App detects connection (WMI event)
   ↓
3. App reads hardware identifier
   ↓
4. Check if device is registered
   ↓
5. If NOT registered → Show registration dialog
   ↓
6. User enters friendly name and preferences
   ↓
7. Save to database with unique ID
   ↓
8. Device now ready for automatic operations
```

### 5.2 Automatic Processing Flow

```
1. Registered USB device connected
   ↓
2. USB Monitor detects and identifies device
   ↓
3. Query device settings from database
   ↓
4. Scan device for video files (based on scan path setting)
   ↓
5. Check each file hash against FileHistory table
   ↓
6. Filter out duplicates
   ↓
7. Add to Copy Queue with priority
   ↓
8. Copy Queue Manager starts transfer
   │
   ├─→ Progress updates to UI
   │
   └─→ On completion: Calculate file hash, save to history
   ↓
9. If conversion enabled for device:
   │
   ├─→ Add copied files to Conversion Queue
   │
   └─→ Conversion Engine processes with set limits
       │
       ├─→ FFmpeg transcodes with specified settings
       │
       ├─→ Progress updates to UI
       │
       └─→ Update FileHistory with conversion status
   ↓
10. All operations complete
    ↓
11. Show Windows notification with summary
    ↓
12. Log completion in database
```

### 5.3 Multiple Device Handling

```
Device A (Priority 1) ─┐
Device B (Priority 2) ─┼─→ Device Queue
Device C (Priority 3) ─┘
                        ↓
                   Copy Manager
                   (Max 3 concurrent)
                        ↓
         ┌──────────────┼──────────────┐
    Device A        Device B        Device C
    Copying         Copying         Waiting
         │              │                │
         └──────────────┴────────────────┘
                        ↓
                 Conversion Queue
                 (Max 2 concurrent)
                        ↓
                ┌───────┴────────┐
           Converting        Converting
           (Device A)        (Device B)
```

---

## 6. Error Handling & Edge Cases

### 6.1 Error Scenarios

| Scenario | Detection | Handling |
|----------|-----------|----------|
| Insufficient disk space | Pre-flight check before copy | Alert user, offer to free space or change destination |
| USB disconnection mid-copy | File operation exception | Mark as incomplete, offer resume when reconnected |
| Video file corruption | FFmpeg conversion failure | Log error, skip file, continue with others |
| Duplicate hardware ID | Device registration | Warn user, prevent registration or overwrite existing |
| FFmpeg not found | Application startup | Show error, prompt to download/install |
| Database locked | Any DB operation | Retry with exponential backoff |
| No video files found | Scan completion | Show info message, log event |
| Conversion exceeds time limit | Timeout monitor | Cancel conversion, log error, mark as failed |

### 6.2 Safety Features

1. **Pre-flight Checks:**
   - Verify destination folder exists and is writable
   - Check available disk space (warn if < 2x largest file)
   - Verify FFmpeg installation and functionality
   - Validate device hardware ID format

2. **Operation Safeguards:**
   - Hash-based duplicate detection prevents re-processing
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

## 7. Technical Requirements

### 7.1 System Requirements

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

## 8. Security & Privacy Considerations

1. **Local Data Only:** All operations and data remain on local machine
2. **No Network Transmission:** No data sent to external servers
3. **Database Encryption (Optional):** Encrypt device database if sensitive
4. **Secure File Deletion:** Overwrite files if deletion is enabled
5. **Permission Management:** Request only necessary Windows permissions
6. **USB Autorun Protection:** Does not execute any files from USB devices

---

## 9. Future Enhancement Ideas

- Cloud backup integration (OneDrive, Google Drive)
- Network-attached storage (NAS) support
- Video metadata editing
- Thumbnail generation and preview
- Batch rename operations
- Automatic video categorization (by date, location)
- Multi-language support
- Portable app version (no installation required)
- Command-line interface for automation
- Integration with video editing software

---

## 10. Document Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-05 | 3216 | Initial design document |

---

## 11. Contact & Support

**For clarifications on this design document, please provide answers to the questions in Section 2.**

Once all requirements are clarified, development can begin with a clear roadmap and specifications.

What's your preferred compensation arrangement for this project? What timeframe?

---

**END OF DESIGN DOCUMENT**
