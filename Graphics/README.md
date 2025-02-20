# Graphics
![Graphics](https://user-images.githubusercontent.com/76865553/136713622-7300a5e5-de05-413a-b748-579b95a36d58.jpeg)

<details><summary><strong>TABLE of CONTENTS</strong> (click to reveal)</summary>

- [Inject](#inject)
- [Inject Intel](#inject-intel)
	- [ig-platform-id](#ig-platform-id)
- [Inject ATI](#inject-ati)
	- [FB Name](#fb-name)
		- [`Inject ATI` and `FB Name` in macOS Monterey](#inject-ati-and-fb-name-in-macos-monterey)
		- [AMD Radeon Performance Tweaks](#amd-radeon-performance-tweaks)
		- [Patching ATI/AMD Connectors](#patching-atiamd-connectors)
	- [RadeonDeInit](#radeondeinit)
- [Inject Nvidia](#inject-nvidia)
	- [NVCAP](#nvcap)
	- [NvidiaGeneric](#nvidiageneric)
	- [NvidiaSingle](#nvidiasingle)
	- [NvidiaNoEFI](#nvidianoefi)
- [Dual Link](#dual-link)
- [EDID](#edid)
- [Load VBios](#load-vbios)
- [PatchVbios](#patchvbios)
- [VRAM](#vram)
</details>

This group of parameters serves to inject Properties for various graphics cards, on-board and discrete. There are many parameters that are actually injected, but they are mostly constants, some are calculated, some are defined in the internal table and only very few parameters are entered via config.

Since this menu is very convoluted I will group parameters together by vendor (Intel, ATI/AMD and NVIDIA)

**ATTENTION!** Nowadays, framebuffer patches are entered primarily via `Devices/Properties`. Settings which are still useful today are: `ig-platform-id` for Intel iGPUs (up to Intel Coffee Lake) and `Inject ATI` in combination with `FB Name` for improving performance of current AMD cards in macOS Monterey.

## Inject
The `Inject` feature is the successor of Chameleon's `GraphicsEnabler`. It can inject about two dozen parameters into macOS, not only based on the GPU model, but also on its internal characteristics after analyzing its VBIOS.

For NVIDIA cards, `NVCAP` is calculated, for Intel on-board graphics dozens of parameters are applied, for ATI/AMD cards frambuffers and connectors are injected. To list all of them would exceed the scope of this guide. Moreover, for most modern GPUs, you don't even need to the inject features in most cases, since Apple ensured that they work out of the box.

You can enable the injection of parameters based on vendors:

- `Inject Intel` &rarr; For Intel on-board graphics. See "[**ig-platform-id**](https://github.com/5T33Z0/Clover-Crate/tree/main/Graphics#ig-platform-id)" for details.
- `Inject ATI` &rarr; For ATI/AMD GPUs. See "[**FB Name**](https://github.com/5T33Z0/Clover-Crate/tree/main/Graphics#fb-name)" for details.
- `Inject NVidia` &rarr; For older NVIDIA Cards, **not** requiring on NVIDIA WebDrivers.</br> Kepler cards and newer (e.g. Maxwell or Pascal) rely on WebDrivers. These can only be installed in post-install. Therefore, you need to do the following:
	- Use boot-arg `nv_disable=1` to disable the card and use the software renderer to display an image during the macOS installation instead. 
	- Once macOS is running, [**download**](https://www.tonymacx86.com/nvidia-drivers/) and install the appropriate WebDriver for your macOS build (supported up to macOS High Sierra only).[^1] 
	- Next, delete the `nv_disable=1` boot-arg 
	- In the **System Parameters** Section, enable **NvidiaWeb**
	- Save your `config.plist` and reboot. Graphics acceleration should work now.</br>
	For more details about using NVIDIA cards, please refer to the [**Nvidia GeForce FAQs**](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.GeForce.en.md).

[^1]: Although NVIDIA Cards ***were*** officially supported up to macOS High Sierra, you can no longer install Nvidia Web Drivers since [Nvidia revoked the certificates](https://twitter.com/khronokernel/status/1532545973372588033) shortly after Khronokernel figured out how to enable Nvidia Web Drivers in macOS Monterey. I don't know if this also affects previously installed Web Drivers (I guess you have to stay offline to not lose the Certs), but at this stage my advice would be to just move on and switch to AMD.

**NOTES**: 

- The `Inject` feature is a remnant of the era before `Whatevergreen.kext` (WEG) existed. Since WEG handles most of the necessary adjustments to get graphics cards working with macOS nowadays, it is recommended to disable them (except for `Inject ATI` which is still useful in macOS 12 to address performance issues).
- When using Intel on-board graphics on modern systems, using `Inject Intel` is not recommended. Instead, follow Whatevergreen's [Intel HD Graphics FAQ](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md) to configure the framebuffer for your CPU in the `Devices/Properties` section manually. 
- **Hint**: The `config.plists` included in the [Desktop Configs](https://github.com/5T33Z0/Clover-Crate/tree/main/Desktop_Configs) and [Laptop Configs](https://github.com/5T33Z0/Clover-Crate/tree/main/Laptop_Configs) sections contain a lot of Framebuffer patches for various Intel CPU families already which might be useful to get your Intel HD/UHD graphics up and running.

## Inject Intel
Listed below are all parameters related to the `Inject Intel` feature. For an in-depth guide on older Intel HD Graphics (HD 3000, 4000/46000 and Iris), check Rampage Dev's [Intel Graphics Guide](https://web.archive.org/web/20170816122747/http://www.rampagedev.com/guides/intel-hd-graphics-guide/).

### ig-platform-id
Property used by macOS to determine the framebuffer profile for Ivy Bridge and newer Intel CPUs with integrated graphics, aka "Intel (U)HD Graphics xxxx". Select the corresponding framebuffer from the `ig-platform-id` dropdown menu in Clover Configurator (supported up Intel UHD Graphics 630 for Coffee Lake).

Nowadays, using Device Properties is the recommended method for configuring integrated graphics, including type of connectors (e.g. if you want to connect an external monitor to a Laptop), setting thr allocated/stolen memory, etc.

For further instruction on how to configure on-board graphics for supported Intel CPUs, please refer to Whatevergreen's extensive [**Intel HD Grpahics FAQ**](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md).

**NOTES**

- For 10th Gen Intel Core CPUs and newer, it's recommended to configure the framebuffer using the `Devices/Properties` section instead, entering the correct `ig-platform-id` amongst additional parameters to configure connectors, amount of VRam, etc.
- Sandy Bridge CPUs require `AAPL,snb-platform-id` and have to be configured via `Devices/Properties` as well.
- If Clover detects an Intel iGPU, it _automatically_ enables Intel Injection if the `Graphics` section doesn't exist in the `config.plist`. To prevent this, you can explicitly disable it by clicking the "Inject Intel" button once to enable it and again to disable it which sets the `Inject` key to `false`:</br>
	```
	<key>Graphics</key>
    <dict>
        <key>Inject</key>
        <false/>
    </dict>
    ```

## Inject ATI
Listed below are all parameters related to the `Inject ATI` feature. For more insight check out Rampage Dev's in-depth [AMD Graphics Guide](https://web.archive.org/web/20170814210930/http://www.rampagedev.com/guides/graphic-cards-injection/) for old AMD GPUs.

### FB Name
**Framebuffer Name** – specific to ATI/AMD GPUs. More than 70 different framebuffers exist for various AMD Video Controller kexts. Each Framebuffer has a unique name to identify it (check the extensive list in Clover Configurator) containing different video output mappings as defined in the `IOKitPersonalities` of the respective kexts' `info.plist`. Here is an example from `AMD9500Controller.kext` located in `S/L/E`:

![FB_name](https://user-images.githubusercontent.com/76865553/166189455-06fe0e53-3f3b-4675-9972-d828872b5d57.png)

To find the Controller kext used by your AMD/ATI card, run Hackintool, click on the "PCIe" Tab and look for it ("Class" is "Display Controller"). Once you find it, click on the magnifying class icon at the beginning of the entry. This takes you straight to the kext in Finder. Right-click it and select "Show Package Contents". Inside you will find the `info.plist`.

Usually, Clover automatically picks an appropriate Framebuffer for common cards it detects. However, you can choose a custom one from the dropdown menu or enter a name manually if the detection fails or the default Framebuffer causes issues. Just make sure it matches the Controller used in your ATI/AMD Card (Hackintool is your friend).

#### `Inject ATI` and `FB Name` in macOS Monterey
Since Clover r5145, commit 89658955f, the Framebuffer Patches for ATI/AMD were updated for better performance under macOS Monterey 12.3+ with newer GPUs ([**Source**](https://www.insanelymac.com/forum/topic/304530-clover-change-explanations/?do=findComment&comment=2778575)). 
Do the following to enable the correct framebuffer for your AMD GPU:

1. Enable `Inject ATI`
2. Under `FB Name`, enter the name of the Framebuffer Patch matching the Controller in your GPU or select one from the dropdown menu:
	- **RX6900** &rarr; `Carswell`
	- **RX6800** &rarr; `Belknap`
	- **RX6600/XT** &rarr; `Henbury`
	- **Radeon 7** &rarr; `Donguil`
	- **RX5700** &rarr; `Adder`
	- **RX5500** &rarr; `Python`
	- **RX570** &rarr; `Orinoco`
3. Add `SSDT-NAVI.aml` to `EFI/CLOVER/ACPI/patched` (contains the necessary device renames)
4. Disable `Whatevergreen.kext` (move to `/kexts/off`)
5. Reboot and check if the performance has improved

#### AMD Radeon Performance Tweaks
You can follow [this guide](https://github.com/5T33Z0/OC-Little-Translated/tree/main/11_Graphics/GPU/AMD_Radeon_Tweaks) to tweak the Performance of Polaris and Navi Cards.

**NOTES**:

- :warning: Make sure to have a working backup of your EFI folder on a FAT32 formated flash drive.
- Injecting "FB name" might cause conflicts if WhateverGreen is active, because WEG uses the default `AMDRadeonFramebuffer` to do its magic.
- You only need to enter something in "FB Name" if you don't use `Whatevergreen.kext`, which handles all this stuff nowadays.

#### Patching ATI/AMD Connectors
ATI/AMD framebuffers can be patched to assign the video output to different connectors. You can follow [this guide](https://www.insanelymac.com/forum/topic/282787-clover-v2-instructions/#comment-1853099) if you have need to re-assign the output. But keep in mind that this guide is from 2012 so I don't know if it is still working today. 

OpenCore users need to use `DeviceProperties`to do this. Check the [**Clover Conversion Guide**](https://github.com/dortania/OpenCore-Install-Guide/blob/master/clover-conversion/Clover-config.md#graphics) for details.

### RadeonDeInit
This key works with ATI/AMD Radeon GPUs (6xxx and higher, possibly 5xxx). It fixes the contents of GPU registers so that the card becomes properly initialized so macOS drivers work as intended.

## Inject Nvidia
Listed below are all parameters related to the `Inject Nvidia` feature. For an in-depth guide on older NVIDIA cards (up to Geforce 900 series) check Rampage Dev's [NVIDIA Graphics Guide](https://web.archive.org/web/20170814211046/http://www.rampagedev.com/guides/nvidia-graphics/)

### NVCAP
This parameter is for legacy NVIDIA video cards and configures types and usage of video ports. The value consists of 40 hexadecimal digits which have to be calculated based on  your video card's VBIOS which has to be analyzed to do so. You can follow [**this guide**](https://dortania.github.io/OpenCore-Post-Install/gpu-patching/nvidia-patching/#nvcap) and use [**this tool**](https://github.com/1Revenger1/NVCAP-Calculator) to calculate the correct NVCAP value for your card.  

**Examples**:

![NVCAP](https://user-images.githubusercontent.com/76865553/162608389-85c00cf3-2b10-49fe-8a0e-7f63c7f3f646.png)

### NvidiaGeneric
If enabled, then instead of the name "Gigabyte Geforce 7300LE", "NVIDIA Geforce 7300LE" will be used.

### NvidiaSingle
For systems with more than one Nvidia graphics cards. If enabled, only one Nvidia GPU is injected.

### NvidiaNoEFI
Adds `NVDA` property to the Nvidia injector. Explained [here](https://www.insanelymac.com/forum/topic/306156-clover-problems-and-solutions/page/84/?tab=comments#comment-2443062).

## Dual Link
The default value is `1`, but for some older configurations this will cause issues. In this case, set it to `0`.

## EDID
![Clover_EDID](https://user-images.githubusercontent.com/76865553/184686410-d3157963-4c17-4d2b-8892-74fa95bab6f5.png)
**Extended Display Identification Data** (or EDID) is a metadata format for display devices to describe their capabilities to a video source (e.g. graphics card). The data format is defined by a standard published by the Video Electronics Standards Association (VESA). Sometimes this value is necessary to fix issues like graphical glitches like black screen on wake or the 8 Apples glitch, etc.

**The following parameters can be entered**:

- `VendorID`
- `ProductID`
- `HorizontalSyncPulseWidth`: Fixes the eight apples issue. See `EightApple` Fix under &rarr; Kernel and Kext Patches.
- `VideoInputSignal`

Follow this [**guide**](https://github.com/5T33Z0/OC-Little-Translated/blob/main/11_Graphics/Inject_EDID/README.md) to figure out how to obtain the EDID of your display.

## Load VBios
Loads video bios from a file. It must be present in `EFI/CLOVER/OEM/xxx/ROM` or `EFI/CLOVER/ROM` and its name must consist of `vendor_device.rom`, e.g. `1002_68d8.rom`. Since r3222, longer file names including sub-vendor and sub-revision are supported as well. For example `10de_0f00_1458_3544.rom`. This option is useful if you want to load a patched Vbios when running macOS.

This can also be used to inject device information of mobile Radeon cards into the system, if macOS cannot detect it. Clover will grab the VBios from the legacy memory at address `0xc0000` injects it into the system so the mobile Radeon card turns on.

For computers with UEFI-only BIOS, there is no Vbios on the legacy address.

## PatchVbios
Fixes the VBios at address `0xC0000`, so that it supports the highest possible resolution for the connected display. For example, the `EDID` of the monitor supports 1920x1080, but the VBios does not. Clover will prescribe it as the first mode and start using it. If the monitor itself does not provide an `EDID`, it can be injected as described above.

There have been cases where enabling this patch would cause a black screen when trying to boot. In this case, disable this setting. Use the value from the config.plist file for the patch instead. If the automation made a mistake, you can write the VBios patch manually, using the standard Find/Replace algorithm (not covered here).

## VRAM
The amount of video memory in MB. In fact, it is detected automatically, but you can adjust it manually. But in reality, I cannot remember a single case where this parameter has helped anyone in any way other than for mobile Radeon cards: if you set `LoadVBios=true` the correct amount of memory will be displayed.

If you see "7Mb", don't try to change this parameter, it's useless. In this case you need to either adjust the framebuffer patch of the Intel iGPU or get a discrete video card instead.
