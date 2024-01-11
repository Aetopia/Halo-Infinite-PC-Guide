#  Modifications
This section covers about modifications for the game that can potentially either help to fix certain technical issues, improve performance or provide QOL features.

## [Special K](https://github.com/SpecialKO/SpecialK)
Special K is an extension game modifying framework for indepth tweaking of games.<br>
This provides a feature rich toolset which can cater in accordance of your needs.

### Installation
There are 2 installation methods:

#### 1. [SKIF (Special K Injection Frontend)](https://wiki.special-k.info/SpecialK/Global#technical-info)
> [!IMPORTANT]
> **Supported Platforms**
> - [x] Steam
> - [x] Microsoft Store

#### 2. [Local Install](https://wiki.special-k.info/en/SpecialK/Local) **(⭐ Recommended)**
> [!IMPORTANT]
> **Supported Platforms**
> - [x] Steam
> - [ ] Microsoft Store

> [!CAUTION]
> SKIF's doesn't exclusively inject into games but rather into any process that uses a graphics API and deals with input which means it could potentially trigger anticheats in other games.<br>
> Because of this it is recommended to use a Local Install of Special K exclusively for instances of Halo Infinite.<br>
> Read More: https://wiki.special-k.info/SpecialK/Global#technical-info

### Features
Since Special K has an expansive feature set, this section will only be concerned with useful features for Halo Infinite.

> If you want to check or mess with settings within Special-K check out its [wiki](https://wiki.special-k.info).

> [!WARNING]
> Changing certain settings within Special K could potentially impact performance or cause undefined behavior in the game.<br>
> Because of this, consider referring to Special K's wiki beforehand on a specific feature.

<img src="../Images/SK.png">

#### NVIDIA Reflex
With Special K, it is possible to use NVIDIA Reflex with Halo Infinite.<br>
Refer to [Special K's wiki page on its NVIDIA Reflex implementation](https://wiki.special-k.info/en/Advanced/Video#nvidia-reflex) for more information.

#### Framerate Limiter
Special K provides a superior framelimiter which provides optimal frame pacing and timing consistency.<br>
Refer to [Special K's wiki page on its Framerate Limiter](https://wiki.special-k.info/en/Advanced/Video#frame-rate-limiter) for more information.

#### Latent Sync
Latent Sync provides a tear free experience without the need of G-Sync, FreeSync or even V-Sync.
What you are getting here is essentially software based adaptive sync.

Refer to [Special K's wiki page on Latent Sync](https://wiki.special-k.info/en/Advanced/Video#latent-sync).

## [ZetaLoader](https://github.com/Aetopia/Zetaloader)
> [!IMPORTANT]
> **Supported Platforms**
> - [x] Steam
> - [ ] Microsoft Store

### Features

A tiny and open source modification/patch for Halo Infinite that aims to fix and improve some aspects of the game which include:<br>

#### 1. Borderless Fullscreen:<br>
Fixes issues with the game's borderless fullscreen implementation not filling the screen correctly at certain resolutions.

> [!WARNING]
> **Due to the way, how ZetaLoader implements Borderless Fullscreen, the following options will not work properly**:
>
> |Setting|
> |-|
> |Display Monitor|
> |Limit Inactive Framerate|
> |Inactive Mute|
> |V-Sync|
> |Maximum Framerate|
>
> Because of this, consider using alternatives for the options listed here:
>
> |Setting|Alternative|
> |-|-|
> |V-Sync|Driver Based V-Sync|
> |Maximum Framerate| External Framerate Limiter|
> |Limit Inactive Framerate| Background Application Max Frame Rate (**NVIDIA Only!**)|

> [!IMPORTANT]
> - ZetaLoader's Borderless Fullscreen forces the game's window to be always on the primary monitor.<br>
> - To activate the game's window either click on its taskbar button or use <kbd>Alt</kbd> + <kbd>Tab</kbd>.
> - ZetaLoader's Borderless Fullscreen changes the following game settings at startup:
>   - Resolution Scale: 100%
>   - Borderless Fullscreen: Off
>   - Display Monitor: Primary Monitor

#### 2. Jittery Mouse Input Fix:

Fixes jittery mouse input when an external framerate limiter is used.

> [!NOTE]
> Setting the game's process priority to `HIGH` will negate this fix.   
>     
> Reference: https://learn.microsoft.com/en-us/windows/win32/procthread/scheduling-priorities
> 
> Issue + Fix Demonstration: https://www.youtube.com/watch?v=4pJd-dKW7WY  



#### 3. User Specified Display Mode:
> [!NOTE]
> User Specified Display Mode in Action: https://www.youtube.com/watch?v=FnzN4xTO6UA

User Specified Display Mode provides Halo Infinite with the facility to have the game's window run at any arbitrary display mode of the user's choice as long as it is valid.

> [!TIP]
> - ZetaLoader's Borderless Fullscreen must be enabled to use this feature.
> - Adjust Halo Infinite's UI's **`Text Size`**.
> - In your GPU's Control Panel set the Scaling Mode to **`Fullscreen`**│**`Stretched`**.
> - User Specified Display Mode handles display modes as follows:<br>
>   -  Only Landscape orientation based display modes can be used.
>   -  If no display mode is specified, the display mode stored in the Windows Registry will be used.

- **1024x768 with ZetaLoader along with Aggressive Dynamic Resolution Scaling.**

    <img  src="../Images/1024x768-Min-FPS.PNG">

- **1024x768 with Visual Quality Tweaks and ZetaLoader along with Aggressive Dynamic Resolution Scaling.**

    <img  src="../Images/Image-Sharpening-Tweaks.PNG">



> [!NOTE]
> For more information on the project, view ZetaLoader's GitHub repository.<br>
    **GitHub Repository: [https://github.com/Aetopia/Zetaloader](https://github.com/Aetopia/Zetaloader)**

### Installation 
1. Download the latest version of ZetaLoader from [GitHub Releases](https://github.com/Aetopia/ZetaLoader/releases/latest).
2. Open Halo Infinite's installation directory.
3. To install ZetaLoader for the campaign and multiplayer, place the dynamic link library in the following folders:<br>
    - Multiplayer: `"<Installation Directory>\game"`
    - Campaign: `"<Installation Directory>\subgames\CampaignS1"`

### Uninstallation
Simply remove `dpapi.dll` from Halo Infinite's Installation directory for the campaign and multiplayer.

## Configuration

> [!IMPORTANT] 
> You must restart the game for any configuration file changes to reflect.

To configure ZetaLoader, do the following:
1. Go to the following directory: 
    - `%LOCALAPPDATA%\HaloInfinite\Settings`
2. Create a new file called `ZetaLoader.ini`, this is ZetaLoader's configuration file.
3. Add the following contents into the file:
    ```ini
    [Settings]
    Fullscreen = 0 
    Width = 0
    Height = 0
    Frequency = 0
    ```

    |Key|Value|
    |-|-|
    |`Fullscreen`|<ul><li>`0` &rarr; Halo Infinite's Window Mode</li><li>`1` &rarr; ZetaLoader's Borderless Fullscreen</li></ol>|
    |`Width`|Display Resolution Width|
    |`Height`|Display Resolution Height|
    |`Frequency`|Display Refresh Rate|

This will make Halo Infinite run `1360`x`768` @ `60` Hz with ZetaLoader's Borderless Fullscreen:<br>
```ini
[Settings]
Fullscreen = 1
Width = 1360
Height = 768
Frequency = 60
``` 
