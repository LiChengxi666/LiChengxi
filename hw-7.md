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
```
结果显示"1++,1--,2+-,2-+"与"1+-,1-+,2++,2--"的比例几乎相同，有很大的把握认定这个数据是由非链特异性建库得到的。
* ****
