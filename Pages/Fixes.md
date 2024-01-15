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

The issue also seems to lie within the [application manifest](https://learn.microsoft.com/en-us/windows/win32/sbscs/application-manifests, "An application manifest is additional metadata that dictates how Windows should handle an application's startup.").

In particular the [**`<heapType>`**](https://learn.microsoft.com/en-us/windows/win32/sbscs/application-manifests#heaptype) attribute seems to be causing this issue.

[You may check this reddit post for more details.](https://www.reddit.com/r/halo/comments/17ff7dc/potential_fix_for_crashing_upon_halo_infinite/)

### Fix
Run the following commands in a PowerShell window to patch the Multiplayer Executable's Application Manifest.

Here the following is happening:
1. The original application manifest is obtained from the multiplayer instance.
2. The `<heapType>` attribute is removed from the manifest.
3. The manifest is rewritten to the multiplayer instance.


```powershell
$ProgressPreference = $ErrorActionPreference = "SilentlyContinue"
Add-Type -AssemblyName System.Xml.Linq
Add-Type -TypeDefinition @"
using System;
using System.Runtime.InteropServices;
public class Kernel32
{
    [DllImport("Kernel32")]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern IntPtr LockResource(IntPtr hResData);
    [DllImport("Kernel32")]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern bool FreeLibrary(IntPtr hLibModule);
    [DllImport("Kernel32")]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern uint SizeofResource(IntPtr hModule, IntPtr hResInfo);
    [DllImport("Kernel32", CharSet = CharSet.Unicode)]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern IntPtr LoadResource(IntPtr hModule, IntPtr hResInfo);
    [DllImport("Kernel32", CharSet = CharSet.Unicode)]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern IntPtr FindResourceEx(IntPtr hModule, int lpName, int lpType, ushort wLanguage);
    [DllImport("Kernel32", CharSet = CharSet.Unicode)]
    [DefaultDllImportSearchPaths(DllImportSearchPath.System32)]
    public static extern IntPtr LoadLibraryEx(string lpLibFileName, IntPtr hFile, uint dwFlags);
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
Get-Content "$(Split-Path $(((Get-ItemPropertyValue -Path "Registry::HKEY_CLASSES_ROOT\steam\Shell\Open\Command" -Name "(Default)") -Split "-", 2, "SimpleMatch")[0].Trim().Trim('"')))\config\libraryfolders.vdf" | 
ForEach-Object { 
    if ($_ -like '*"path"*') {
        $Path = "$($_.Trim().Trim('"path"').Trim().Trim('"').Replace("\\", "\"))\steamapps\common\Halo Infinite\game\HaloInfinite.exe" 
        if (Test-Path $Path -ErrorAction SilentlyContinue -PathType Leaf) {
            $Text = ""
            $hModule = [Kernel32]::LoadLibraryEx($Path , [IntPtr]::Zero, 0x00000002)
            $hUpdate = [Kernel32]::BeginUpdateResource($Path, $false)
            $hResInfo = [Kernel32]::FindResourceEx($hModule, 24, 1, 1033)
            $hResData = [Kernel32]::LoadResource($hModule, $hResInfo)
            $Source = [Kernel32]::LockResource($hResData)
            $Length = [Kernel32]::SizeofResource($hModule, $hResInfo)
            $Destination = [byte[]]::new($Length)

            [System.Runtime.InteropServices.Marshal]::Copy($Source, $Destination, 0, $Length)
            for ($Index = 0; $Index -le $Destination.Length; $Index++) { $Text += [char]$Destination[$Index] }
            $Text = [System.Text.RegularExpressions.Regex]::Replace($Text, "[^(\x09\x0A\x0D\x20-\xD7FF\xE000-\xFFFD\x10000-x10FFFF)]", '') -Replace '^\xEF\xBB\xBF', ''
            [void][Kernel32]::FreeLibrary($hModule)

            $XDocument = [System.Xml.Linq.XDocument]::Parse($Text)
            $XElement = ($XDocument.Descendants() |  Where-Object { $_.Name.LocalName -eq "heapType" })
            if ($XElement) {
                $XElement.Remove()
                $Text = "<?xml version=`"1.0`" encoding=`"UTF-8`" standalone=`"yes`"?>`n$($XDocument.ToString())"
                [void][Kernel32]::UpdateResource($hUpdate, 24, 1, 1033, [System.Runtime.InteropServices.Marshal]::StringToHGlobalAnsi($Text), $Text.Length);
                [void][Kernel32]::EndUpdateResource($hUpdate, $false);
                Write-Host "Halo Infinite's Multiplayer's Application Manifest has been patched!" -ForegroundColor Green
            }
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
Get-Content  "$(Split-Path $(([string]((Get-ItemPropertyValue -Path "Registry::HKEY_CLASSES_ROOT\steam\Shell\Open\Command" -Name "(Default)") -Split "-", 2, "SimpleMatch")[0]).Trim().Trim('"')))\config\libraryfolders.vdf" | 
ForEach-Object {
    $Path = "$($_.Trim().Trim('"path"').Trim().Trim('"').Replace("\\", "\"))\steamapps\common\Halo Infinite\videos" 
    if (Test-Path $Path -ErrorAction SilentlyContinue) { 
        @("intro.mp4" , "Startup_Sequence_Loading.mp4") |
        ForEach-Object { [void](New-Item "$Path\$_" -Force) }
    }
}
$ProgressPreference = $ErrorActionPreference = "Continue"
```
