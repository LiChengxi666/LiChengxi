# 1
**ChIP-seq实验中的对照：** 
* 阴性对照：此步骤的目的是确定结果中的非特异性DNA片段，即背景信号强度。这有助于识别和校正这些背景信号，可以提高信噪比，以便更准确地识别真正的结合位点。通常包括IgG和input。

  - input：样本经过超声，但是没有进行ChIP，包含样本超声后总DNA。以总DNA进行电泳，检测超声效果，同时，可以与最后ChIP样本进行比较，判断ChIP的效率（如果用同一引物进行PCR，ChIP组和input组亮度差不多，说明ChIP效率高，样本中所有的目的基因片段都被ChIP下来了，反之，说明效率低，实验条件有待改进）

  - IgG：用普通的IgG做为抗体，理论上不会ChIP下来任何DNA片段，但是由于非特异结合，或者实验过程中，没有发生结合的DNA清除不完全，可能也会出现条带。

* 阳性对照：一般用anti-RNA polymerase II抗体，因为RNA polymerase II是通用转录因子，在所有细胞中都能结合基因的核心启动子区，因此理论上，ChIP后PCR会有条带。一般阳性对照不进行测序。
或者使用阳性对照抗体（即总组蛋白 H3）和/或阳性对照 qPCR 引物（针对已知的阳性和阴性靶蛋白结合位点）。
# 2
## findPeaks：
-style: 设置分析模式，如factor、histone、groseq等，不同的模式针对不同的实验类型。

-i: 存储input样本中间文件的"tag directory"

-L: 设定最小Fold Change阈值。

-P: 设定最大p-value阈值。

-F： 用于设置Fold Change (vs Control)。
## findMotifsGenome.pl：
-mask : 该参数告诉motif分析程序，在得到一个可能的motif之后，在后续的motif分析中是否排除该motif的影响。有点类似于抽样调查中的无放回抽样。

-size: 指定用于motif分析的片段长度，默认为200； -size given 设置片段大小为目标序列长度。越大，motif分析所需要的计算资源越多。

-p : 设置线程数-S : 所寻找的motif数目，默认为25。25已经不算少了，如果自定义数目，推荐设置更少而不是更多。

-mis : 所允许的错配数，默认为2

-cpg : 在对背景序列和目标序列进行normalization时，使用CpG%矫正而非GC%。

-rna : 搜索RNA上的motif（如使用CLIP-seq数据进行分析的时候）

-bg : 指定自定义的背景序列。HOMER默认随机选取基因组序列作为背景。

-h: findMotifsGenome.pl脚本默认使用二项分布进行motif富集分析，这在背景序列多于目标序列时是十分有用的。但是有时我们使用 -bg 参数自定义背景序列时，其数目可能会小于目标序列，此时使用 -h 参数选择超几何分布会更加适合。

-len : motif的长度设置，逗号分隔，默认8，10，12。

-N: 用于motif分析时所需的序列数目，通常当我们设置 -len过大，内存不够时，会选择减小 -N参数或者 -size 参数。
# 3
1. 生成中间文件
```
makeTagDirectory homework/ip    homework/ip.chrom_part.bam
makeTagDirectory homework/input    homework/input.chrom_part.bam
```
2. 进行peak calling
```
findPeaks homework/ip/ -style factor -o output_homework/part_homework.peak -i homework/input/
```
3. 筛选基因
```
awk -F'\t' '($ Fold Change vs Control >= 8) && ($p-value vs Control <= 1e-8)' output_homework/part_homework.peak > output_homework/part_homework_selected.peak
```
最终的part_homework_selected.peak即包含所有符合条件的peak
4. motif finding
```
findMotifsGenome.pl output_homework/part_homework.peak sacCer2 output_hoemwork/part.motif.output -len 8
```
