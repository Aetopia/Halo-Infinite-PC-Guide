# Visual Quality Tweaks
This section is concerned with providing tweaks that can help to enhance the graphical fidelity of the game.
> [!IMPORTANT]
> **Supported Platforms**
> - [x] Steam
> - [x] Microsoft Store

### NVIDIA
If you are having an NVIDIA GPU, you may avail the following techonlogies to improve visual quality:<br>

- **NVIDIA Image Sharpening**
    > Applies a sharpness filter to the target application to improve visual quality.
    
    To use image sharpening do the following:<br>

    1. Make sure NVIDIA Image Scaling is disabled for Halo Infinite.
    2. Open the Windows Registry Editor:<br>
    
        1. Go to the following key:<br>
        
            ```
            HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\nvlddmkm\FTS
            ```
        2. Find a value called `EnableGR535` and set its value to `0`.<br>
        
            This will replace Image Scaling with Image Sharpening in the NVIDIA Control Panel.
    3. Open up the NVIDIA Control Panel → Manage 3D Settings → Program Settings → Halo Infinite.
    4. Configure the sharpness and film grain values for the game.

- **NVIDIA Image Scaling**
    > This is a driver upscaling technology shipped with the NVIDIA Display Driver supposed to be a successor to NVIDIA Image Sharpening.
    
    1. Open up the NVIDIA Control Panel, under Manage 3D Settings, Program Settings, select Halo Infinite.
    2. Enable NVIDIA Image Scaling and also **enable** the overlay indicator.
    3. Configure the sharpness values for the game.

- **Negative LOD Bias**
    > This option improves texture quality and **not** image quality, lower the value, the more sharper.

    This requires [NVIDIA Profile Inspector](https://github.com/Orbmu2k/nvidiaProfileInspector/releases/latest) to be downloaded.

    **Make sure, Negative LOD Bias is enabled for Halo Infinite in the NVIDIA Control Panel!**

    1. Open NVIDIA Profile Inspector.
    2. Navigate to Halo Infinite's profile and set the following to:

        |Setting|Value|
        |-|-|
        |Antialiasing - Transparency Supersampling|`AA_MODE_REPLAY_MODE_ALL`|
        |Texture Filtering - LOD Bias (DX)| Anywhere between `0` ~ `-3`|
    3. Save the profile.


### AMD
One may leverage the following technologies to improve visual quality:<br>

- AMD Radeon™ Image Sharpening
- AMD Radeon™ Super Resolution

Each feature may be enabled globally or for the game specifically.
