# Part I
## 1
Bowtie 利用 Burrows-Wheeler Transform (BWT) 的逆变换特性和高度压缩性来提高运算速度。
BWT 能通过重组字符串使得相同字符聚在一起，从而在压缩和搜索中显著提高效率。
通过这种方式可以在重复数据较多的基因组序列中表现出极好的压缩性能，这使得 Bowtie 可以快速定位潜在的匹配位置。

在内存优化方面，Bowtie 采用以下策略：

FM-index：利用 BWT 的逆变换和前缀和信息，使得在查询时能够以较少的内存消耗来处理大规模数据。
比对分块：将数据分为较小的段，以减少所需内存，从而在内存限制条件下，加速比对过程。
数据结构优化：使用紧凑的数据结构来存储索引，从而降低内存占用。

## 2

chrIII 15

chrI 18

chrXIII 67

chrXV 101

chrV 33

chrX 71

chrXVI 78

chrII 51

chrVII 125

chrIV 194

chrIX 25

chrXII 169

chrVIII 68

chrmt 12

chrXIV 58

chrVI 17

chrXI 56

## 3
### 3.1
CIGAR string 是 SAM/BAM 文件中用于描述 reads 和参考基因组之间比对关系的一种字符串表示。它包括了以下信息：

M: 匹配（Match）

I: 插入（Insertion）

D: 删除（Deletion）

N: 跳过（Skips）

S: 软剪切（Soft Clipping）

H: 硬剪切（Hard Clipping）

P: 填充（Padding）

如："73M1I5D" 表示前 10 个碱基匹配，接着 1 个碱基插入，之后 5 个碱基删除。
### 3.2
clipped alignment因为着在比对过程中，并没有用到全部的read的序列，read中间匹配成功，但是两段的序列没有成功匹配。如：

```
Read:      ACGGTTGCGTTAATCCGCCACG
               |||||||||||||||
Reference: TAACTTGCGTTAATCCGCCTGG
```

clip alignment对应的CIGAR表示有两种S （soft clip） 和H （hard clip）。如果是hard clip，则截取的部分不会在SAM文件对应的read中出现 (clipped sequences not present in SEQ)，如果是soft clip (clipped sequences present in SEQ)，则会出现。
如上面的例子就可以写作4S15M3S。
当后续讨论结果时，无论S还是H都不会被考虑。
