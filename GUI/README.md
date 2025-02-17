# GUI
![GUI](https://user-images.githubusercontent.com/76865553/136703745-7ec35d11-5458-482c-a8c8-ccaf48d9650d.jpeg)

<details><summary><strong>TABLE of CONTENTS</strong> (click to reveal)</summary>

- [CustomIcons](#customicons)
- [Theme](#theme)
  - [EmbeddedThemeType](#embeddedthemetype)
- [Timezone](#timezone)
- [KbdPrevLang](#kbdprevlang)
- [Language](#language)
- [ScreenResolution](#screenresolution)
- [Console Mode](#console-mode)
- [PlayAsync](#playasync)
- [ProvideConsoleGop](#provideconsolegop)
- [ShowOptimus](#showoptimus)
- [TextOnly](#textonly)
- [Mouse](#mouse)
  - [Enabled](#enabled)
  - [Speed](#speed)
  - [Mirror](#mirror)
- [Scan](#scan)
  - [Entries](#entries)
  - [Legacy](#legacy)
  - [Tool](#tool)
  - [Linux](#linux)
  - [Kernel](#kernel)
- [Custom Entries](#custom-entries)
- [Hide Volume](#hide-volume)
</details>

This section is for configuring the look and behavior of Clover's Boot menu GUI as well as defining which Volumes are displayed. To find out which options are accessible from the actual Clover Bootloader GUI, click [here](https://github.com/5T33Z0/Clover-Crate/blob/main/GUI/Boot_Menu_Options.md)

## CustomIcons
Enabling this key will use custom disk icons (`.VolumeIcon.icns`) stored in in the root folder of the volumes themselves, instead of using the disk icon from a theme. Since this is a hidden file, you have to press `Cmd–Shift–.` in Finder to reveal/hide it.

## Theme
Enter the name of a theme installed in `EFI\Clover\themes\ThemeXYZ` for example. Use the folder name for reference. In this example, enter "ThemeXYZ".

A collection of themes can be found on the [Clover Github Repo](https://github.com/CloverHackyColor/CloverThemes). Some of them are really old and might not be compatible with the newest version of Clover which results in a broken boot menu GUI. So make sure to test them first by booting from a FAT32 formatted USB flash drive with the EFI folder first,

### EmbeddedThemeType
Select between `Dark` and `Light` variant of an embedded theme. If left unset, the variants are picked based by the real time clock – light during the day, dark at night (if it's supported by the theme). Introduced in r4773.

## Timezone
Clover r4773 introduced the concept of changing theme styles of embedded themes depending on the time of day. From 8:00 AM to 8:00 PM a light variant will be used and from 8:00 PM to 8:00 AM the dark variant will be used. It's based on GMT timing, so add or subtract the number of hours for your location.

## KbdPrevLang
Select your preferred Keyboard Layout for macOS if you want to save the system language when upgrading macOS with native NVRAM.

In combination with `Language`, this can used to fix an issue when installing beta versions of macOS. These sometimes do not include other languages besides english. In this case you will just see a gray screen where there the install assistant should be. Just set `Language` to `en-US:0` and you will be fine. 

## Language
Currently, setting the language only makes sense for the "Help" menu called by the F1 Key. However, this value is sent to the system and can affect the default language.

## ScreenResolution
Here you can change the default resolution of the Clover GUI. The default/fallback is 1024x768 px. Clover tries to detect and set the highest possible resolution supported by your screen and graphics card automatically. But you can pick the correct or desired resolution from the dropdown menu yourself if auto-detection fails. You'll find the list of supported video modes in the boot-log.

If `PatchVBios` is enabled in the `Graphics` section, you will see the maximum resolution available for your monitor. In this case, the `ScreenResolution` parameter may be superfluous. With some configurations though, enabling the `PatchVBios` will result in a black screen. In this case, Clover requires the `EDID` of the monitor. Legacy Clover tries to get it through BIOS calls, often successfully, sometimes not. UEFI Clover queries the UEFI BIOS, which probably has the `EDID` for iGPUs and probably doesn't for the discrete GPUs. Check the preboot.log – if no `EDID` is listed there, you need to enter it manually.

## Console Mode
The correct value can be found in your `boot.log`, or set it to `Max` and the maximum possible resolution will be used for console mode.

## PlayAsync
Clover r4833 added audio support for the Boot menu via the `AudioDxe.efi` driver. `PlayAsync` determines the playback mode of the boot chime: either sequential or simultaneously. With synchronous playback (default), the boot process is sequential: chime first, then the bootloader kicks in. If `PlayAsync` is enabled, the boot process will run parallel or simultaneously to the audio playback. The chime has to be named `sound.wav` for day themes and `sound_night.wav` for night mode and needs to be placed in the root folder of the used theme.

**NOTES**: 

- If the file is too long it will be cut off when macOS takes control of the audio driver. 
- In my opinion (5T33Z0), this feature should have been called `PlaySimult` instead, because that what it does. Using the term `asynchronous` in the context of audio evokes a feeling of audio issues rather than a feature.

## ProvideConsoleGop
Creates a GOP protocol for console mode, i.e. for text output not in text mode, as you are used to doing in PC BIOS, but in graphical mode, as Apple does.

In revisions prior to r5128, this setting was also present in Quirks as `ProvideConsoleGopEnable` but has since been removed to avoid duplicate parameters.

`ProvideConsoleGop` from GUI will override `ProvideConsoleGopEnable` from the `Quirks` section  – just in case you forgot to remove this parameter from the config when updating the Clover.

## ShowOptimus
Enables an on-screen notification in the Boot menu about which GPUs are enabled, so you can disabled discrete Optimus GPUs on the fly since they are not supported by macOS. If on-board and discrete GPU are enabled, the notification `Intel Discrete` is displayed.

## TextOnly
Text-only menu mode for a minimal GUI and faster loading times (like it was 1984 all over again).

## Mouse
### Enabled
`Enabled` - self explanatory. Disable the mouse if it causes issues in the Boot menu

### Speed
`Speed` – Sets the speed of the cursor, reasonable values are 2 to 8. Some mice require negative speed, moving in the opposite direction. A value of 0 means that the mouse is disabled.

### Mirror
Inverts the mouse speed to correct inverted mouse movement.

## Scan
![GUI_Scan](https://user-images.githubusercontent.com/76865553/136699885-281deddd-0151-4e06-a489-4299669fe4d3.png)

In this sub-section, you can modify Clover's scan features for disk. In general, the less options you choose, the faster the UI appears. When set to `Auto`, Clover decided what to do. If you set it to `Custom` you have control over the following features:

### Entries
This option enables or disables scanning for UEFI records on each disk on Boot. It also applies the rules set in the "Hide Volume" and "Custom Entries" sections.

### Legacy
This parameter enables or disables the search for legacy bootloaders launched from PBR. This can be refined by selecting additional criteria from a dropdown menu: "First" places legacy sections at the beginning of the list, "Last" places them at the end of the list. No one ever uses this, so lets move on.

### Tool
This setting enables or disables the search for UEFI tools on each partition when Clover boots.

### Linux
This option enables or disables the search for Linux bootloaders on each partition when Clover boots. Use in conjunction with "Kernel" options. Do not scan for Linux bootloaders on each partition, it takes a lot of time!

### Kernel
This parameter enables or disables the search for Linux kernels on each partition. This can be refined by selecting additional criteria from a dropdown menu (see screenshot): "First" places legacy sections at the beginning of the list, "Last" places legacy sections at the end of the list and so on. No one ever uses this, so lets move on.

This parameter enables or disables the search for legacy bootloaders launched from PBR based on the kernel version. 

## Custom Entries
In this sub-section you can add your own entries to the Boot menu GUI. This is useful if an entry is missing or if you want to customize it. Click on the "+" symbol to add an entry to the list. After double Clicking the entry, a sub-menu is opened: 

![Custom_Entry](https://user-images.githubusercontent.com/76865553/136745225-5c06bd82-b7a8-4459-b29d-52870742941e.png)

Here you can change a lot of things. The most important one being the dropdown menu for selecting the "Volume" the custom entry should represent:

![GUID](https://user-images.githubusercontent.com/76865553/136699942-79efe2a9-2995-48fe-a6fd-aad342ae259a.png)

Enter a custom name in the `Title` field, set `Type` and `Volume Type` and it should work.

## Hide Volume
Here you can use enter names of partitions/volumes which shall be a hidden from the Bootloader GUI by default. For certain Windows volumes you need to enter the correct UUID to hide them. Open Terminal and enter:

1. `diskutil list` and hit enter
2. Find the partition you want to hide
3. Copy its Identifier ("diskXsY") to clipboard or store it in a text file temporarily.
4. Next, enter `diskutil info diskXsY | grep -i "Partition UUID" | rev | cut -d' ' -f 1 | rev`. Replace diskXsY with your actual identifier and hit enter
5. Copy the `UUID`
6. In the "Hide Volume" section, click on `+` 
7. Enter the UUID

On next reboot, this volume should be hidden.

**Tip**: An easier method to find out the UUID is to using the custom entries section, since it displays the UUIDs of all volumes.

## Clover Boot Menu Options

&rarr; [Options accessible from the Boot Menu](https://github.com/5T33Z0/Clover-Crate/blob/main/GUI/Boot_Menu_Options.md)
