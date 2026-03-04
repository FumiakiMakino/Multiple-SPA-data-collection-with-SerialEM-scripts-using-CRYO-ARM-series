
# SerialEM SPA Scripts for JEOL CRYO ARM Series

## 前置き / Introduction

本スクリプトは、JEOL製 CRYO ARM シリーズ（CRYO ARM 200、300、300Ⅱ）における単粒子解析用データ収集（SPA data collection）およびマルチグリッド運用に対応したものです。
その他の装置（JEOL F200 や Thermo Fisher Scientific 社製装置など）での動作は保証しておりません。

本ページでは、大阪大学大学院生命機能研究科で使用している CRYO ARM 300 用スクリプトを提供しています。
他の装置で使用する場合に不具合などが生じた場合は、大阪大学大学院生命機能研究科 難波研究室 牧野
または JEOL のクライオサポートチームまでご連絡ください。

使用する SerialEM のバージョンは 4.2 以上を推奨します。
また、本スクリプトの動作には Bart の plugin が必須です。

Plugin manual:
https://bio3d-mirror.nexperion.net/ftp/SerialEM/Tools/JeolTemExtPlugin/

このマニュアルは個人的に作成したものであり、説明が不十分な箇所が含まれている可能性があります。
また、本マニュアルおよびスクリプトの二次配布や直リンクは禁止します。

問い合わせ先:
makino.fumiaki.fbs[at]osaka-u.ac.jp

English version:
https://github.com/fmakino/SerialEM_SPAScripts_for_CRYO_ARM_series/blob/main/README_EN.md


------------------------------------------------------------

# 日本電子装置での SPA データ収集の考え方

JEOL装置ではステージ精度がそれほど高くないため、SerialEMの従来方法では正確な位置合わせに多くの時間が必要になります。
また LowMag → Mag 移行時のヒステリシスも大きいため、頻繁なモード移動は推奨されません。

本スクリプトでは位置精度を多少犠牲にして速度を優先する設計を採用しています。
この方法を Osaka Framework と呼びます。

目標

Square screening : 30–60 min
Data collection preparation : +30 min

一度 Mag モードに移行した後は、できるだけ LowMag に戻らないようにしてください。
戻る場合はビームアライメントが必要になります。


------------------------------------------------------------

# Script (Osaka University)

CRYO ARM 300

https://github.com/fmakino/SPAscripts-using-SerialEM-for-CRYOARMseries/blob/main/SerialEMsettings-script_Fukumura-Makino_z300_simple.txt


------------------------------------------------------------

# CL aperture setting

2段 CL aperture の場合 Property file に追加

JeolHasExtraApertures 1

Example

ScriptName EMProperties

CLapt_type = 0
Arm300I = 1

CLapt_for_record = 2
CLapt_for_view = 2


追加設定

JeolHasNitrogenClass 2


------------------------------------------------------------

# 事前準備

・SerialEM calibration 完了
・SerialEM ver4.2 以上

Focus/Tune → Drift Protection ON

Hole template を以下にコピー

C:\ProgramData\SerialEM\Data\HoleImage\hole_template.mrc

動画
https://youtu.be/zP3PCU6qTfY


------------------------------------------------------------

# Script installation

例

C:\ProgramData\SerialEM\

SerialEM menu

Script → Load new package

動画
https://youtu.be/m1iIJxrMrSQ


------------------------------------------------------------

# EMProperties

Example (CRYO ARM 300II)

JEOL TEM

CLapt_type = 0
ARM300 = 0


Stage

safty_z_lower = -210
safty_z_upper = 205
eucentric_height = 0
koehler_z_offset = 0


Atlas

mag_atlas
default_spot_atlas
brightness_atlas


Square

default_SquareMag = 150
spot_square = 4
brightness_square = 100


------------------------------------------------------------

# FOV adjustment

cmd_MagAtlas
cmd_MagSquare
cmd_MagRecord

初回設定

1 move magnification
2 SaveAtlasMag / SaveSquareMag / SaveRecordMag
3 AdjustFOV_byDrag-FLA


Python setting

EnableExternalPython 1
PathToPython 312 C:\Users\End-user\AppData\Local\Programs\Python\Python312\

https://bio3d.colorado.edu/SerialEM/download.html#PythonModules


------------------------------------------------------------

# YoneoLocr

progdir = C:\Users\...\yoneoLocr-main

https://github.com/YonekuraLab/yoneoLocr

論文引用を忘れないこと


------------------------------------------------------------

# Scripts to check

SPADataCollection_Screening

target defocus
-1.4 ～ -2.0

use_YL = 1

video
https://youtu.be/SymgfCqZiIg


AligncomaAndStig / FullAutoAligncomaAndStig

settle_time = 10
exp_time = 1

video
https://youtu.be/ngeKlpl55SU


ZLPalignByFL

search_range = 40


SPADataCollection

AutoFocusbyOL
AutoFocusbyZ


------------------------------------------------------------

# CryoARM200 script

SerialEMsettings-script_Fukumura-Makino_z200_simple.txt

※未検証
