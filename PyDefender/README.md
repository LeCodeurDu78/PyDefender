# PyDefender (Python Antivirus Software)

## File Information

PyDefender.py -> Main PyDefender Program (including animation, scanning, protection functions, etc.)

PyDefender_Engine.py -> Conversion Database (used to convert database dict and list to and from each other)

PyDefender_Extension.py -> File Extension (including scanned file extensions and common file extensions)

PyDefender_Model.* -> Virus Database (the virus database must be placed in the same directory)

PyDefender_Interface.py -> PyQt5 Interface (designed by QT designer, must be matched with the main program)

PyDefender_Resource.py -> PyQt5 Resource (status pictures, icon packages, button icons, etc.)

PyDefender_Language.py -> Translate Dict (Traditional Chinese, Simplified Chinese, English)

PyDefender_Version.py -> Pyinstaller Info (file information, version information, original name, etc.)

## Dir Information

```
PyDefender/
├── Driver/
│   ├── PyDefender_Driver.sys
│   └── ...
│
├── Model/
│   ├── PyDefender_Model.json
│   ├── PyDefender_Model.txt
│   └── ...
│
├── Rules/
│   ├── Yara_Rules.yar
│   ├── Compile_Rules.yrc
│   └── ...
│
├── PyDefender.py
├── PyDefender_Engine.py
└── ...
```

## Requirements

Use pip install requirements to install import module

```
psutil==5.9.5
pefile==2023.2.7
requests==2.31.0
pyperclip==1.8.2
pywin32==306
PyQt5==5.15.9
```

## Pefile Scanning

Get the pefile file function import table for scanning

```
import pefile

def pe_scan(file):
    try:
        fn = []
        with pefile.PE(file) as pe:
            for entry in pe.DIRECTORY_ENTRY_IMPORT:
                for func in entry.imports:
                    try:
                        fn.append(str(func.name, "utf-8"))
                    except:
                        pass
            if fn in known_malicious_list:
                return "Virus"
            return "Safe"
        return "Unknown"
    except Exception as e:
        return f"Error: {e}"

info = pe_scan("path to file")
print(info)
```

## Process Detect

Show the new process name, file path, cmd line, pid

```
import psutil, time

def proc_detect():
    existing_process = set()
    for p in psutil.process_iter():
        existing_process.add(p.pid)
    while True:
        time.sleep(0.1)
        new_process = set()
        for p in psutil.process_iter():
            new_process.add(p.pid)
        for pid in new_process - existing_process:
            try:
                p = psutil.Process(pid)
                name, file, cmd = p.name(), p.exe(), p.cmdline()
                print(f"Name: {name}")
                print(f"File: {file}")
                print(f"Pid: {p.pid}")
                print(f"Cmd: {cmd}")
            except Exception as e:
                print(f"Error: {e}")
        existing_process = new_process

proc_detect()
```

## File Detect

Monitor file changes under the specified path

```
import os, win32file, win32con

def file_detect(path):
    hDir = win32file.CreateFile(path,win32con.GENERIC_READ,win32con.FILE_SHARE_READ|win32con.FILE_SHARE_WRITE|win32con.FILE_SHARE_DELETE,None,win32con.OPEN_EXISTING,win32con.FILE_FLAG_BACKUP_SEMANTICS,None)
    while True:
        for action, file in win32file.ReadDirectoryChangesW(hDir,10485760,True,win32con.FILE_NOTIFY_CHANGE_FILE_NAME|win32con.FILE_NOTIFY_CHANGE_DIR_NAME|win32con.FILE_NOTIFY_CHANGE_ATTRIBUTES|win32con.FILE_NOTIFY_CHANGE_SIZE|win32con.FILE_NOTIFY_CHANGE_LAST_WRITE|win32con.FILE_NOTIFY_CHANGE_SECURITY,None,None):
            try:
                fpath = os.path.join(path, file)
                if action == 1:
                    print(f"File Create: {fpath}")
                elif action == 2:
                    print(f"File Delete: {fpath}")
                elif action == 3:
                    print(f"File Modify: {fpath}")
                elif action == 4:
                    print(f"File Rename: {fpath}")
                elif action == 5:
                    print(f"File Rename: {fpath}")
            except:
                pass

file_detect("path")
```
## Support System
Windows 8.1, 10, 11 (64-bit), Ram 500MB, Rom 200MB or higher
Other systems may experience software malfunctions or crash