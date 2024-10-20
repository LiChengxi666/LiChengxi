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
### 3.3
mapping quality代表了reads所mapping的位置是否可信。APQ越高，表示比对质量越好，说明该read比对到参考基因组上的位置越唯一。如果一条reads可以mapping到多个位置，那么就会有比较低的mapping quality。一般在后续分析的时候，我们都需要把MAPQ质量过低的reads去掉，一般的cutoff是MAPQ≥10，严格一些的时候需要MAPQ≥30。
### 3.4
可以。

MD tag与前文所述CIGAR和SEQ（序列）字段一起使用，可以重建read比对到的参考基因组序列。

具体步骤如下：

MD 字符串由数字和字母组成，交替出现。数字代表与 SEQ 相同碱基的个数，字母代表与 SEQ 不同的碱基。^ 后面跟着的字母代表参考基因组上被删除的碱基。因此获取这些信息之后，再根据 CIGAR 字符串，跳过插入 (I) 和软剪切 (S) 的碱基，参考跳过 (N) 需要根据参考基因组信息进行补充。
最终拼接即可得到read比对到的区域对应的参考基因组序列。

例如：

SEQ : ACGTACGT

CIGAR: 8M

MD: 5A2

则对应的参考基因组序列为： ACGTT CGT。
## 4
### 4.1安装：
1. 确保以root身份进入：
```
docker exec -it -u root lichengxi bash
```
2. 安装sudo
```
apt install sudo
```
3. 下载bwa
```
#安装
wget http://sourceforge.net/projects/bio-bwa/files/bwa-0.7.17.tar.bz2
#解压缩
tar -jxvf bwa-0.7.17.tar.bz2
```
4. 编译bwa
```
#首先安装相关的包
sudo apt-get install make
sudo apt-get install build-essential
sudo apt-get install zlib1g-dev
#之后进行编译
cd bwa-0.7.17/
make
```
5. 添加环境变量
```
#打开系统变量的配置文件：
vim ~/.bashrc

#按i进入编辑状态，在文件里面的最后一行添加如下内容（最后路径应为安装bwa的文件夹）：
export PATH=$PATH:/home/test/mapping/bwa-0.7.17 
#然后Esc，输入:wq，保存文件并退出。

#之后打开bwa
source ~/.bashrc
bwa
```
### 4.2下载并操作
1. 下载文件：[sacCer3.fa.gz](https://hgdownload.soe.ucsc.edu/goldenPath/sacCer3/bigZips/sacCer3.fa.gz)

2. 解压缩：
```
gunzip sacCer3.fa.gz
```
3. 建立索引：
```
bwa index sacCer3.fa
```
4. 映射到参考基因组
```
bwa mem sacCer3.fa THA2.fa > THA2-bwa.sam
```
