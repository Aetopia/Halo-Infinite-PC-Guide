# Fixes
This section is concerned with addressing certain technical issues affecting Halo Infinite.

## Multiplayer Startup Crashing Fix
> [!IMPORTANT]
> **Supported Platforms**
> - [x] Steam
> - [ ] Microsoft Store


If you are crashing continuously when starting up Halo Infinite then the following fix might help.

### Why is the game crashing?
The reason seems to be [access violations](# "An access violation simply means the game was trying to access an invalid memory address.") causing the game to crash.

The issue also seems to lie within the application manifest (An application manifest is additional metadata that dictates how Windows should handle an application's startup.).

In particular the [**`<heapType>`**](https://learn.microsoft.com/en-us/windows/win32/sbscs/application-manifests#heaptype) attribute seems to be causing this issue.

[You may check this reddit post for more details.](https://www.reddit.com/r/halo/comments/17ff7dc/potential_fix_for_crashing_upon_halo_infinite/)

### Fix
Run the following commands in a PowerShell window to patch the Multiplayer's Instance's Application Manifest.

Here the following is happening:
1. Instead of just removing the **`<heapType>`** attribute the entire application is removed to just play it safe.
2. Without the application manifest, the game's window is now DPI Unaware which may cause issues with window sizing on High DPI displays.<br>
    To fix this, ZetaLoader is installed which makes a call to [**`SetProcessDpiAwarenessContext(DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE)`**](https://learn.microsoft.com/en-us/windows/win32/hidpi/dpi-awareness-context#constants) which applies Per Monitor DPI Awareness which Halo Infinite's Multiplayer uses through its application manifest.


```powershell
$ProgressPreference = $ErrorActionPreference = "SilentlyContinue"
Add-Type -TypeDefinition @"
using System;
using System.Runtime.InteropServices;
public class Kernel32
{
    [DllImport("Kernel32", CharSet = CharSet.Unicode)]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern IntPtr BeginUpdateResource(string pFileName, bool bDeleteExistingResources);
    [DllImport("Kernel32", CharSet = CharSet.Unicode)]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern bool UpdateResource(IntPtr hUpdate, int lpType, int lpName, int wLanguage, IntPtr lpData, uint cb);
    [DllImport("Kernel32", CharSet = CharSet.Unicode)]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern bool EndUpdateResource(IntPtr hUpdate, bool fDiscard);
}
"@
Get-Content  "$(Split-Path $(([string]((Get-ItemPropertyValue `
-Path "Registry::HKEY_CLASSES_ROOT\steam\Shell\Open\Command" `
-Name "(Default)") `
-Split "-", 2, "SimpleMatch")[0]).Trim().Trim('"')))\config\libraryfolders.vdf" | 
ForEach-Object { 
    if ($_ -like '*"path"*') {
        [string]$Path = "$(([string]$_).Trim().Trim('"path"').Trim().Trim('"').Replace("\\", "\"))\steamapps\common\Halo Infinite" 
        if (Test-Path $Path) {
            [IntPtr]$hUpdate = [Kernel32]::BeginUpdateResource("$Path\game\HaloInfinite.exe", $false)
            [void][Kernel32]::UpdateResource($hUpdate, 24, 1, 1033, [IntPtr]::Zero, 0)
            [void][Kernel32]::EndUpdateResource($hUpdate, $false)
            Invoke-RestMethod `
                -Uri  "$((Invoke-RestMethod "https://api.github.com/repos/Aetopia/ZetaLoader/releases/latest").assets[0].browser_download_url)" `
                -OutFile "$Path\game\dpapi.dll"
        }
    } 
}
$ProgressPreference = $ErrorActionPreference = "SilentlyContinue"
```

## Skipping Intro Videos
> [!IMPORTANT]
> **Supported Platforms**
> - [x] Steam
> - [ ] Microsoft Store

This fix will allow you to skip any intro videos, the game plays at startup.

### Fix
To skip the intro videos, we simply replace them with empty files resulting in the videos to be skipped.

```powershell
$ProgressPreference = $ErrorActionPreference = "SilentlyContinue"
Get-Content  "$(Split-Path $(([string]((Get-ItemPropertyValue `
-Path "Registry::HKEY_CLASSES_ROOT\steam\Shell\Open\Command" `
-Name "(Default)") `
-Split "-", 2, "SimpleMatch")[0]).Trim().Trim('"')))\config\libraryfolders.vdf" | 
ForEach-Object { 
    @("intro.mp4" , "Startup_Sequence_Loading.mp4") |
    ForEach-Object { [void] (New-Item "$Path\videos\$_" -Force) }
}
$ProgressPreference = $ErrorActionPreference = "Continue"
```