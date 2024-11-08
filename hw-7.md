# 2.1
## (1)
### a.CPM/RPM (Counts/Reads per Million):
将每个基因的read count除以样本中的总read count，然后乘以一百万。适用于不同测序深度的比较。

此方法不考虑基因长度的影响，因此更多适用于小RNA。
### b.RPKM (Reads Per Kilobase per Million Reads Mapped):

将每个基因的read count除以基因长度（千碱基），再除以样本的每百万mapped reads。
同时考虑了测序深度和基因长度，适用于样本中基因之间的基因计数比较；不适用于样本比较或差异表达分析。

### c.FPKM (Fragments Per Kilobase of Exon per Million mapped reads):

类似于RPKM，但适用于双端测序。
计算方式与RPKM相同，区别在于处理的单位是“片段”而不是“reads”。因此FPKM = RPKM/2.
### d.TPM (Transcripts Per Million):

计算方法是先对每个基因的read count按照千碱基长度进行标准化，再对整个**样本总数**进行标准化。即RPKM/Σ(RPKM)。
此方法也同时考虑了测序深度和基因长度，同时适用于样本内和样本间的比较。
### e.TMM (Trimmed Mean of M-values):

首先获取每个样本中每个基因的library size normalized read count
计算两个样本间的M-value和A-value，最后处理数据（去除M值的±30%，去除A值的±5%）
修剪后得到M的加权平均值，计算Normalize Factor。
此方法适用于样本间的表达量差异分析。

### f.DESeq2:
与TMM类似，通过将每个样品的Counts除以其几何平均值来计算比值
然后将 Size Factor 定义为每个样本的该比值的中位数。
接下来使用Size Factor对每个样本的原始计数矩阵（Raw Count Data）。
## （2）
E D A
## （3）
* **判断链特异性：参考之前的方法，对代码稍作更改即可。**
```
/usr/local/bin/infer_experiment.py -r GTF/Arabidopsis_thaliana.TAIR10.34.bed -i bam/Shape01.bam
# 结果：
Fraction of reads failed to determine: 0.0315
Fraction of reads explained by "1++,1--,2+-,2-+": 0.4769
Fraction of reads explained by "1+-,1-+,2++,2--": 0.4916
```
结果显示"1++,1--,2+-,2-+"与"1+-,1-+,2++,2--"的比例几乎相同，有很大的把握认定这个数据是由非链特异性建库得到的。
* **计算read_counts并读取特定基因的counts**
```
# 生成counts文件
/home/software/subread-2.0.3-source/bin/featureCounts \
 -s 0 -p -t exon -g gene_id \
 -a GTF/Arabidopsis_thaliana.TAIR10.34.gtf \
 -o share/counts.txt bam/Shape02.bam
# 读取
grep "AT1G09530" share/counts.txt

AT1G09530       1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1;1   3075768;3075768;3075768;3076401;3076401;3076401;3076459;3076459;3076459;3077173;3077173;3077173;3077173;3077378;3077378;3077378;3077378;3077378;3077378;3078346;3078346;3078346;3078346;3078346;3078346;3078545;3078545;3078545;3078545;3078545;3078545;3078843;3078843;3078843;3078843;3078843;3078843;3078984;3078984;3078984;3078984;3078984;3078984   3075852;3075852;3075852;3077286;3076808;3076748;3076808;3077286;3076748;3077286;3077286;3077286;3077286;3078257;3078257;3078257;3078257;3078257;3078257;3078453;3078453;3078453;3078453;3078453;3078453;3078610;3078610;3078610;3078610;3078610;3078610;3078908;3078908;3078908;3078908;3078908;3078908;3079544;3079544;3079544;3079654;3079654;3079654   +;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+;+   2762    86
```
即counts为86
## （4）
文件在压缩包内

# 2.3
## （1）
Multiple test correction是一种统计方法，用来校正由于进行多次假设检验而导致的显著性水平被过度高估的问题。假设有一个样本量为10000的检验场景，如果使用通常的p>0.05的标准，则假阳性将达到10000×0.05=500个，对结果产生较大影响。在进行大量的假设检验时，由于多次比较的原因，存在较高的概率出现假阳性结果。因此，为了控制整体的错误率，需要对p value进行校正。

q value是一种在第二类型错误率控制方面更加强调的多重检验校正方法。它表示在进行多次检验中，错误检验的比例不超过设定的水平。通常情况下，q value也被称为FDR（False Discovery Rate），即错误发现率。假设要进行N次假设检验，在这N个检验中，有N0组数据是阴性，其余N1=N−N0组数据为阳性。对于某个假设检验算法，假设其在N次检验中给出了R次判定为阳性，而在这R次中有a次为假阳性(即犯了第一类错误),b=R−a为真阳性，那么我们定义False Discovery Proportion（FDp）为Fdp(D)=a/R然后进一步定义FDR:FDR(D)=E[Fdp(D)]。通过一些算法可以控制q值或FDR，例如最简单地，可以另q=p*N.

与q value不同，p value是指在原假设成立的情况下，观察到的样本数据或更极端情况出现的概率。p value用于衡量在原假设为真时，观察到的数据产生的可能性大小，代表了实验结果的显著性程度。在进行多重检验时，p value是在没有进行校正的情况下得到的结果，可能导致过高的错误率。因此，为了控制错误率，在进行Multiple test correction时，需要使用q value进行校正。
## （2）
DESeq2中的数据归一化方法主要包括size factors和DESeq2中的均衡处理。size factors是一种用于调整每个样本的测序深度的方法，可以将测序深度不同的样本进行比较。对于每个样本，DESeq2会计算一个size factor，用于调整每个基因的计数值。具体计算公式如下：

size factor = total count for sample / median of total counts across samples

DESeq2中还包括进行均衡处理的方法，通过对条件间的均衡性进行建模，可以进一步消除特定条件下的批次效应和技术偏差。

edgeR中的数据归一化方法主要包括TMM（trimmed mean of M values）和RPKM（reads per kilobase per million mapped reads）归一化。TMM方法是一种基于基因的方法，过程为首先获取每个样本中每个基因的library size normalized read count计算两个样本间的M-value和A-value，最后处理数据（去除M值的±30%，去除A值的±5%）修剪后得到M的加权平均值，计算Normalize Factor。RPKM方法则是将测序数据根据测序深度和基因长度进行归一化，以避免测序深度对基因表达量的影响。

