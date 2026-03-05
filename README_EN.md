### Concept of SPA Data Collection Using JEOL Microscopes

- The stage accuracy of JEOL microscopes is not very high. Therefore, using the conventional SerialEM workflow requires various adjustments and additional time to achieve accurate alignment of positions. In addition, the hysteresis that occurs when switching from **LowMag to Mag mode** is relatively large, which is another reason why frequent switching between these modes is not recommended.
- To address this, the scripts we provide pursue **speed while sacrificing a small amount of positional accuracy**, and are designed with consideration of this hysteresis behavior. This framework is called the **Osaka Framework**. The target performance is **30–60 minutes for screening one square**, followed by **an additional ~30 minutes before starting data collection**.

One important point to note is that **once the microscope has switched to Mag mode, it should avoid returning to LowMag as much as possible**. If it is absolutely necessary to go back to LowMag, the **beam alignment must be adjusted again**.

This concept remains the same even when using **multi-grid operation**. Therefore, we recommend first acquiring the **Global map in batch mode**, and then acquiring the **Square maps also in batch mode**.

Workflow diagram

![workflow_multigrid](https://github.com/user-attachments/assets/edd98b35-ab29-473d-a710-bafd4d082522)

Videos explaining each task are linked in the respective sections.

Scripts used at the **Graduate School of Frontier Biosciences, Osaka University**  
[CRYO ARM 300](https://github.com/FumiakiMakino/Multiple-SPA-data-collection-with-SerialEM-scripts-using-CRYO-ARM-series/blob/bfe698c861ee2f44a79467c884d560721d6856dc/SerialEMsettings-script_SPA-Muye)

---

If the CL aperture has **two stages**, add the following line to the Property file:

JeolHasExtraApertures 1

In EMProperties, the value of **CLapt_type** is interpreted as follows:
- `0`: upper CL aperture
- `1`: lower CL aperture  
(This behaves in the same way as the `SetApertureSize` command.)

```bash
ScriptName EMProperties

# CL aperture
# 0: CryoARM200, CryoARM200CA, CryoARM300II, F200
# 1: 2nd CL aperture of first gen CryoARM300
CLapt_type = 0 #1, For JEOL plugin, 1 means 2nd CL aperture
Arm300I = 1 # CryoARM300I, open apertures
CLapt_for_record = 2 #1 CLapt for record, 0: Remove, 1: No.1, 2: No.2, 3:No.3, 4:No.4
CLapt_for_view = 2 #1 CLapt for record, 0: Remove, 1: No.1, 2: No.2, 3:No.3, 4:No.4
```

When using these scripts, set the following value in the property file:

JeolHasNitrogenClass = 2

Please download the scripts **from the repository Code → Download ZIP**, rather than directly from the link above.

---

## Preparation

- SerialEM calibration and installation have been completed (**version 4.2 or later recommended**)
- In the **Focus/Tune** menu, make sure **Drift Protection** is enabled
- Prepare a default hole template (with the hole size commonly used) and copy it in advance to:
- Add MulGridSkipRealign to the property file.
If this parameter is not set, SerialEM’s Realign function may be triggered, which can cause the system to switch back to LowMag and perform additional alignment steps. This behavior is not ideal for JEOL microscopes.
```
MulGridSkipRealign    1
```
However, the JEOL CRYO ARM uses a cartridge system, which minimizes grid rotation and typically limits positional shifts to only a few micrometers. Therefore, the Realign procedure is generally unnecessary.
C:\ProgramData\SerialEM\Data\HoleImage\hole_template.mrc

Explanation video (using the previous script version): PyJEM and hole template  
https://youtu.be/zP3PCU6qTfY

---

## Installing the New Script

After downloading the new script, copy it to a desired location (for example):

C:\ProgramData\SerialEM\

In SerialEM, select from the top menu:

script → Load new package

Then load the script you copied.

Explanation video (using the previous script version): Installation  
https://youtu.be/m1iIJxrMrSQ

---

### Confirm and Enter EMProperties Values

If you have used a similar script previously, copying the values from that configuration will be the easiest and fastest approach (note that variable names may have changed). Below is an example used on the **CryoARM 300 II in the Namba laboratory**.

__JEOL TEM type__
- CLapt_type = 0  
  \# 0: CryoARM200, CryoARM200CA, CryoARM300II, F200  
  \# 1: 2nd CL aperture board, CryoARM300
- ARM300: CRYO ARM 300 (Z300), set to 0.

__Stage setting__
- safty_z_lower = -210 [um], safty_z_upper = 205 [um]: Z limit values
- eucentric_height = 0 [um]: default is 0
- koehler_z_offset = 0 [um]: default is 0; when using Köhler mode, around -120 is recommended

__Setting for Atlas__
- default_spot_atlas: spot size for Atlas map acquisition
- mag_atlas: magnification for Atlas map acquisition (50 for CRYO ARM 300, 30 for CRYO ARM 200)
- brightness_atlas: 100% is commonly used

__Setting for Square__
- default_SquareMag = 150: magnification for Square map acquisition
- spot_square = 4: spot size for Square map acquisition
- brightness_square = 100: 100% is commonly used; when using an energy filter, adjust to a suitable brightness together with FL focus

__Setting for FOV adjustment between Low-mag and record mag__
- cmd_MagAtlas  @= C:\ProgramData\SerialEM\afs\LowMag30.txt
- cmd_MagSquare @= C:\ProgramData\SerialEM\afs\LowMag150.txt
- cmd_MagRecord @= C:\ProgramData\SerialEM\afs\MagMode60k.txt

When configuring these values for the first time:
1. Move to each magnification and run SaveAtlasMag, SaveSquareMag, and SaveRecordMag.
2. Then run the AdjustFOV_byDrag-FLA script.

This script uses Python libraries, so you must ensure that Python can be called via PATH. Below is an example of adding Python modules in SerialEMproperties.txt:

```bash
EnableExternalPython 1
PathToPython 312 C:\Users\End-user\AppData\Local\Programs\Python\Python312\
```

For details, see:
https://bio3d.colorado.edu/SerialEM/download.html#PythonModules

__YoneoLocr__
- progdir = C:\Users\VALUEDGATANCUSTOMER\Desktop\yoneoLocr-main  
  When using YoneoLocr for hole alignment, specify this variable (progdir) as the location where the program is installed.
  *YoneoLocr must be installed in advance. Please refer to the YoneoLocr site:
  https://github.com/YonekuraLab/yoneoLocr
  Also, if you use it, please remember to cite it in your paper.

There are many other parameters, but the above are the minimum required.

---

### Notes on the Scripts

- Do not usually modify EMProperties values (FOV adjustment is recommended about once a week).
- In this script, parameters such as defocus, flashing timing, and zero-loss peak adjustment (FL focus adjustment) should be changed using SerialEM. Please refer to the manual for details.

---

### Scripts to Verify

**1. SPADataCollection_Screening**
- Check operation.
- `whichAlignHole = 0`  
  Which hole alignment do you use? 0: Find One Hole, 1: Template (Buf T), 2: YoneoLocr  
  Set this value according to your microscope environment. `0` is recommended.
- To ensure AutoFocusRoutine works correctly, do not forget to set Focus menu “target defocus” to `-1.4` to `-2.0`.
- If you use YoneoLocr (YoneoHole) for AlignToHole (Yonekura et al. 2021; https://www.nature.com/articles/s42003-021-02577-1 ; source: https://github.com/YonekuraLab/yoneoLocr), set `use_YL = 1`.  
  In this case, make sure to start YoneoHole. Also note that `use_YL` inside AlignToHole takes priority.

Video:
https://youtu.be/SymgfCqZiIg

**2. AligncomaAndStig or FullAutoAligncomaAndStig**
- Check operation only.
- settle_time = 10 sec
- exp_time = 1 (depends on ice thickness)
- If it does not work well: in many cases either the height adjustment or AutoFocus is not working. Both are often fixed by AutoFocus calibration.

Video:
https://youtu.be/ngeKlpl55SU

**3. ZLPalignByFL**
- Check operation.
- Confirm that a command prompt appears and that the FLF value in TEMCenter on the JEOL PC changes, or that the image becomes dark at least once.
- parameters: search_range = 40 (default)

**4. SPADataCollection**
- Check operation.
- Try running it following the workflow. See the manual for details.

* Script for CRYO ARM 200: Only the behavior when launching YoneoHole is different. Not yet validated.
