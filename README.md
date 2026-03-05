## 前置き  introduction
本スクリプトは、JEOL製 CRYO ARM シリーズ（CRYO ARM 200、300、300Ⅱ）における単粒子解析用データ収集（SPA data collection）およびマルチグリッド運用に対応したものです。

(本スクリプトの名前にMuyeとついています。これは中国語で牧野を発音したMùyě (ムーイエ)に由来しております。)

その他の装置（JEOL F200 や Thermo Fisher Scientific 社製装置など）での動作は保証しておりません。また、阪大生命機能科学科のCRYO ARM 300で動作しているスクリプトを提供します。もし、他の装置で運用する場合に不具合等あった場合は大阪大学大学院生命機能研究科 難波研究室 牧野かJEOLのクライオサポートチームに連絡をください。

使用する SerialEM のバージョンは 4.2 以上を推奨します。
また、本スクリプトの動作には Bart の plugin が必須です。インストール方法については、以下のリンクのManual_v-1-3.pdf をご参照ください。
https://bio3d-mirror.nexperion.net/ftp/SerialEM/Tools/JeolTemExtPlugin/

本スクリプトをインストールする場合は、以下に記載した手順に従って設定してください。
また、操作マニュアルも同サイトにて配布しております。
[here](https://github.com/FumiakiMakino/Multiple-SPA-data-collection-with-SerialEM-scripts-using-CRYO-ARM-series/blob/bfe698c861ee2f44a79467c884d560721d6856dc/SerialEM_SPA_Script-Muye_MultiGrid_2026forDistribution.pdf)


なお、このマニュアルは個人的に作成したものであり、十分に清書されていない部分や説明が不十分な箇所が含まれている可能性があります。あらかじめご了承ください。

また、本マニュアルおよびスクリプトの二次配布や、ファイルへの直接リンク（直リンク）は一切禁止いたします。

本マニュアルを整理・清書した正式版については、JEOL 側に依頼して装置へインストールされた場合に提供されます。
内容は概ね同様ですが、正式版をお持ちの場合はそちらをご参照ください。

もし不明な点がありましたら、日本電子（JEOL）のアプリケーションエンジニア、または
大阪大学大学院生命機能研究科 難波研究室 牧野までお問い合わせください。

makino.fumiaki.fbs[at]osaka-u.ac.jp


This script is designed for single-particle analysis (SPA) data collection and multi-grid operation on the JEOL CRYO ARM series microscopes (CRYO ARM 200, 300, and 300Ⅱ).

(The script name includes “Muye,” which comes from the Chinese pronunciation of the surname Makino (牧野).)

Operation on other microscopes (such as JEOL F200 or instruments manufactured by Thermo Fisher Scientific) is not guaranteed.
The scripts provided here are those currently used on the CRYO ARM 300 at the Graduate School of Frontier Biosciences, Osaka University.

If you attempt to operate the scripts on other instruments and encounter any problems, please contact Fumiaki Makino at the Namba Laboratory, Graduate School of Frontier Biosciences, Osaka University, or the JEOL Cryo Support Team.

We recommend using SerialEM version 4.2 or later.

In addition, this script requires Bart’s plugin to operate.
For installation instructions, please refer to the following manual:

https://bio3d-mirror.nexperion.net/ftp/SerialEM/Tools/JeolTemExtPlugin/

When installing this script, please follow the procedures described below.
The operation manual is also available at the following link:

[here (only japanese)](https://github.com/FumiakiMakino/Multiple-SPA-data-collection-with-SerialEM-scripts-using-CRYO-ARM-series/blob/bfe698c861ee2f44a79467c884d560721d6856dc/SerialEM_SPA_Script-Muye_MultiGrid_2026forDistribution.pdf)

Please note that this manual was personally prepared and may contain sections that are not fully polished or where explanations may be insufficient. We appreciate your understanding.

In addition, redistribution of this manual and scripts, as well as direct linking to the files (hotlinking), is strictly prohibited.

A cleaned and formally edited version of this manual will be provided when the scripts are installed on the instrument through JEOL.
The contents are generally the same; however, if you have access to the official version, please refer to that version instead.

If you have any questions, please contact a JEOL application engineer, or

Fumiaki Makino (Namba Lab., Graduate School of Frontier Biosciences, The University of Osaka)

makino.fumiaki.fbs[at]osaka-u.ac.jp


English version is [here](https://github.com/FumiakiMakino/Multiple-SPA-data-collection-with-SerialEM-scripts-using-CRYO-ARM-series/blob/f9d76bda02b12ae5299037a07e7e871597c33f59/README_EN.md).

### 日本電子の装置を使用した単粒子解析のデータ収集(SPAデータ収集)の考え方  
・日本電子の装置はステージの精度がそれほど高くありません。そのため、SerialEMの従来のやり方では正確に場所をアライメントするために色々な工夫や正確なアライメントを行うための時間が必要になってしまいます。また、LowMagからMagに移行した際のヒステリシスも大きいので頻繁な行き来はお勧めできないのも理由の一つです。
そこで、正確性を少し犠牲にして速さを追求し、ヒステリシスを考慮したのが、私たちが提供するスクリプトです。これはOsaka Frameworkと呼ばれるものです。1スクエアのスクリーニングが30-60分、その後データ収集に+30分が目標です。 

ここで注意が必要なのは一度Magに移行してからはなるべくLowMagに行かないようにすることです。どうしても必要な場合は再度ビームのアライメント調整が必要です。

この考え方はマルチグリッド対応になっても変更ありません。従って、バッチでGlobal mapを取得したあと、同じくバッチでスクエアマップを取得する手順を薦めています。

ワークフローの図

![workflow_multigrid](https://github.com/user-attachments/assets/edd98b35-ab29-473d-a710-bafd4d082522)


また、それぞれの作業を説明した動画を各項目でリンクしています。
  
  
大阪大学大学院生命機能研究科で使用しているスクリプト  
[CRYO ARM 300](https://github.com/FumiakiMakino/Multiple-SPA-data-collection-with-SerialEM-scripts-using-CRYO-ARM-series/blob/bfe698c861ee2f44a79467c884d560721d6856dc/SerialEMsettings-script_SPA-Muye)



このとき、CL apertureが2段の場合はProperty fileに "JeolHasExtraApertures 1"を追加すること。EMPropertiesのCLapt_typeの値は、0は上段のCL aperture, 1は下段のCL aperture (SetApertureSizeのコマンドと同様の動き)となる。

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
これらのスクリプトを使用する場合、プロパティファイルのJeolHasNitrogenClassを2に設定してください。


上記リンクからではなく、codeからzipをダウンロードしてtextファイルとしてダウンロードしてください。 
   

## 事前準備、用意しておくこと
・SerialEMのキャリブレーションとインストール(ver4.2以上 推奨)が完了していること  
  
・Focus/Tuneメニュー  Drift Protection にチェックが入っていること  

・MulGridSkipRealignをプロパティファイルに導入すること。これをいれないとSerialEMのRealignの機能が動く、LowMagに移行したりと日本電子の顕微鏡と相性が悪い。

ただ、日本電子のCRYO ARMはカートリッジを採用しており、 回転が起こらず、位置ズレも数ミクロンに収まるためこれが必要ない。

```bash　　
MulGridSkipRealign			1
```

・もし、ホールテンプレートを使う場合は、デフォルトのホールテンプレート(よく使うホールの大きさで作成しておく)を準備し、C:¥ProgramData¥SerialEM¥Data¥HoleImage¥hole_template.mrc  
に事前にコピーしておくと便利です  
  
[説明動画(旧スクリプトによる説明)：PyJEM関連とhole templateについて](https://youtu.be/zP3PCU6qTfY)


## 新しいスクリプトのインストール方法  
新しいスクリプトをダウンロード後、任意の場所にコピー(例：C:¥ProgramData¥SerialEM¥)  
SerialEM上部のメニューscript --> Load new packageから先程コピーしたスクリプトをロードする。  
[説明動画(旧スクリプトによる説明)：インストールについて](https://youtu.be/m1iIJxrMrSQ)  
  
### EMProperties の値を確認および入力    
　もし以前同様のスクリプトを使用していた場合は下記の値をそちらからコピーすると簡単で早い（変数名が変更してある可能性があるので注意が必要である）。  ここでは難波研究室CRYO ARM 300Ⅱでの使用例をのせる。    
   
__JEOL TEM type__   
・CLapt_type = 0 : # 0: CryoARM200, CryoARM200CA, CryoARM300II, F200  
                 # 1: 2nd CL aperture board, CryoARM300  
・ARM300：CRYO ARM 300(Z300), set to 0.  

__Stage setting__  
・safty_z_lower = -210 #[um], safty_z_upper = 205 #[um] :zの限界値  
・eucentric_height = 0 # [um] ：0がデフォルト  
・koehler_z_offset = 0 # [um] ：0がデフォルト、ケラーモード使う場合は-120程度入れるとよい。

__Setting for Atlas__  
・default_spot_atlas: Atlas map作成時のspotサイズ  
・mag_atlas: Atlas map作成時の倍率(CRYO ARM 300なら50,  CRYO ARM 200なら30)  
・brightness_atlas: 100%が多く使われる。  
        
__Setting for Square__  
・default_SquareMag = 150：squaremapを取得するときの倍率  
・spot_square = 4：squaremapを取得するときのspot size  
・brightness_square = 100：100%が多く使われる。ただしenergy filterを入れる場合はその限りではなく、ちょうどよい明るさが存在する。FL focusと合わせて調整するとよい。  

__Setting for FOV adjustment between Low-mag and record mag__         
・cmd_MagAtlas @= C:\ProgramData\SerialEM\afs\LowMag50.txt  
・cmd_MagSquare @= C:\ProgramData\SerialEM\afs\LowMag150.txt  
・cmd_MagRecord @= C:\ProgramData\SerialEM\afs\MagMode60k.txt  

  最初にこれらの値を設定する場合、まずはそれぞれの倍率に移動しSaveAtlasMag, SaveSquareMag, SaveRecordMagを行うこと。
  
  その後、AdjustFOV_byDrag-FLAのスクリプトを動かすこと。このスクリプトにはpythonライブラリを使用するため、pythonのパスを通して使用できるようするようにすること。以下にSerialEMproperties.txtへpythonモジュールの追加例を掲載する。
  ```bash　　
EnableExternalPython			1
PathToPython 312 C:\Users\End-user\AppData\Local\Programs\Python\Python312\
```
 詳しくは以下のリンク先を参考
 https://bio3d.colorado.edu/SerialEM/download.html#PythonModules
 
__YoneoLocr__  
・progdir = C:\Users\VALUEDGATANCUSTOMER\Desktop\yoneoLocr-main ： Hole alignにYoneoLocrを使用する場合はこの変数progdirにプログラムが参照する場所として指定する。  
＊事前にYoneoLocrのインストールなどが必要です。これについてはYoneoLocrの[サイト](https://github.com/YonekuraLab/yoneoLocr)を参照してください。また、使用した場合は論文への引用も忘れないようにしてください。

他にも沢山の値が存在するが必要最低限なものは以上。  

  

### スクリプトについての注意点  
＊EMPropertiesの値は普段変更を加えない(FOVの調整は１週間に一度程度が推奨)。    
＊このスクリプトではそれぞれのparameter(defocus、flashingのタイミング、zero loss peakの調整[FL focusの調整])はSerialEMを使用して変更するようにした。詳しくはマニュアルを参照


  
### 確認すべきスクリプト  
**1. SPADataCollection_Screening**  
動作確認を行う。  
whichAlignHole = 0  # Which hole alignmet do you use 0:Find One Hole, 1: Template (Buf T), 2: YoneoLocr: ここの値を自分たちの装置の環境に合わせて行うこと。0がオススメ。
スクリプトAutoFocusRoutine を確実に動かすためにFocusメニュー”target defocus” で-1.4~-2.0にsetすることを忘れずにすること。  
スクリプトAlignToHoleを動かす際にYoneoLocrのYoneoHole ([Yonekura et al. 2021](https://www.nature.com/articles/s42003-021-02577-1)　[入手先](https://github.com/YonekuraLab/yoneoLocr))を使用する場合にはwhichAlignHole = 2にすること。このとき、YoneoHoleを起動することを忘れずに行うことと。  
[説明動画](https://youtu.be/SymgfCqZiIg)

**2.AligncomaAndStig or FullAutoAligncomaAndStig**  
動作確認のみ。  
settle_time = 10 sec  
exp_time = 1(氷の厚さなどによる)  
もし、うまく動かない場合：高さ調整かAutoFocusがうまく行ってない場合が多い。どちらも、AutoFocusのキャリブレーションで直る場合が多い。  
[説明動画](https://youtu.be/ngeKlpl55SU)

**3. ZLPalignByFL**  
動作確認。コマンドプロンプトが現れることと、JEOL PCのTemCenterのFLFが変更していあるかどうかを確認。または画像が一度でも暗くなると良い。  
parameters: search_range = 40 がデフォルト


**4. SPADataCollection**   
 動作確認を行う。
 ワークフローに従い実行してみる。詳しくはマニュアル参照。


＊ CRYO ARM 200用のスクリプト。yoneohole起動時の動作のみ異なります。未検収。　　
















