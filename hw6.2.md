# 3.1
## (1)
python脚本significant_genes.txt用于生成包含所有符合条件的基因名称。之后再进行分析。

过程：[https://cloud.tsinghua.edu.cn/smart-link/c5acc76f-66cd-40e9-b82e-f10f3134b784/]

结果：[https://cloud.tsinghua.edu.cn/smart-link/c5acc76f-66cd-40e9-b82e-f10f3134b784/]
## (2)
在GO分析中，Fold Enrichment和P value分别用于衡量某个GO term在研究基因集中的富集程度和统计显著性。

Fold Enrichment（FE）的计算公式为：
FE = Input number/expected = (k/n) / (K/N)
其中，

k 表示研究基因集中包含某个GO term的基因数量，
n 表示研究基因集中的全部基因数量，
K 表示参考基因组中包含某个GO term的基因数量，
N 表示参考基因组中的全部基因数量。

P value的计算方法：[https://cloud.tsinghua.edu.cn/smart-link/6277587f-6e1d-46a2-9f2f-65cbd1b3849e/]

在定义显著富集的GO terms时，一般使用FDR（False Discovery Rate）而不是单纯的P value。此情景下采用的是Multiple test correction。FDR通过校正多重假设检验而得到，可以控制犯第一类错误（将假阳性错误判定为真阳性）的概率。由于在大规模基因组数据分析中，会进行大量的假设检验，因此需要控制FDR来减少错误发现的可能性。通过调整P value得到FDR，以此来决定哪些GO term是显著富集的。通常，FDR<0.05被认为是一个合理的阈值来定义显著富集的GO terms。
# 3.2
分析过程和结果：

[https://cloud.tsinghua.edu.cn/smart-link/1ece2a1f-5b1a-4cad-80c8-49cb600d1f15/]

[https://cloud.tsinghua.edu.cn/smart-link/c7590284-2ccc-4ebe-8ac4-a25d688daf16/]

[https://cloud.tsinghua.edu.cn/smart-link/f920ee60-ba68-446c-b06f-6ab3a6e19df1/]

可见GO和KEGG的结果并不完全一致，原因在于KEGG enrichment主要关注生物通路的富集，可以更直接地揭示基因之间的相互作用和调控关系，更侧重于基因在特定通路中的作用和调控，有助于理解基因组在生物过程中的作用机制；而GO enrichment则注重基因的功能分类和生物过程，在更宏观的层次上揭示基因的功能，更侧重于揭示基因功能的细致分类和参与的生物过程，有助于理解基因的功能和作用方式。

