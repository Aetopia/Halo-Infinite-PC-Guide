# Fixes
This section is concerned with addressing certain technical issues affecting Halo Infinite.

## Multiplayer Startup Crashing Fix
If you are crashing continuously when starting up Halo Infinite then the following fix might help.

### Why is the game crashing?
The reason seems to be [access violations](# "An access violation simply means the game was trying to access an invalid memory address.") causing the game to crash.

The issue also seems to lie within the application manifest (An application manifest is additional metadata that dictates how Windows should handle an application's startup.).

In particular the [**`<heapType>`**](https://learn.microsoft.com/en-us/windows/win32/sbscs/application-manifests#heaptype) attribute seems to be causing this issue.

[You may check this reddit post for more details.](https://www.reddit.com/r/halo/comments/17ff7dc/potential_fix_for_crashing_upon_halo_infinite/)

### Fix
Run the following commands in a PowerShell window to patch the Multiplayer's Instance's Application Manifest.

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