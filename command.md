**0. 进入对应路径**
```
cd dic_name
# dic_name应替换为test_command.gtf所在的文件夹
```
**1.统计文件的行数以及字符数。**
```
# 统计行数
wc -l test_command.gtf

# 统计字符数
wc -c test_command.gtf
```
输出：
1. 8 test_command.gtf
2. 636 test_command.gtf

**2.利用 ```grep ```等命令尝试筛选并输出示例文件中以 chr_ 起始，并且基因id为 YDL248W 的行。**
```
grep '^chr_.*gene_id "YDL248W"' test_command.gtf
```
输出：

chr_IV  ensembl gene    1802    2953    .       +       .       gene_id "YDL248W"; gene_version "1";

chr_IV  ensembl transcript      802     2953    .       +       .       gene_id "YDL248W"; gene_version "1";

chr_IV  ensembl start_codon     1802    1804    .       +       0       gene_id "YDL248W"; gene_version "1";

**3.利用 sed 等命令将示例文件中的 chr_ 替换为 chromosome_ 并输出每行的第1，3，4，5列。（无需改动原文件，只输出结果）**
```
# 先用sed指令替换后的内容pipe给awk指令
sed 's/^chr_/chromosome_/' test_command.gtf | awk '{print $1, $3, $4, $5}'
```
输出：

chromosome_IV gene 1802 2953

chromosome_IV transcript 802 2953

chromosome_IV exon 1802 2953

chromosome_IV CDS 1802 950

chromosome_IV start_codon 1802 1804

chromosome_IV stop_codon 2951 2953

chromosome_IV gene 762 3836

chromosome_IV transcript 3762 836


**4.通过man命令以及更多的资料学习简单的 awk 命令，尝试互换示例文件的第2列和第3列，并且对输出结果利用 sort 命令依照第4和第5列数字大小排序，将最终结果输出到result.gtf文件中。**
```
# 先用awk指令互换2、3列，再输出所有列（$0）并pipe给sort指令
awk '{temp = $2; $2 = $3; $3 = temp; print $0}' test_command.gtf | sort -k4,4n -k5,5n > result.gtf
#查看结果
cat result.gtf
```
输出:

chromosome_IV gene ensembl 762 3836 . + . gene_id "YDL247W-A"; gene_version "1";

chr_IV transcript ensembl 802 2953 . + . gene_id "YDL248W"; gene_version "1";

chromosome_IV CDS ensembl 1802 950 . + 0 gene_id "YDL248W"; gene_version "1";

chr_IV start_codon ensembl 1802 1804 . + 0 gene_id "YDL248W"; gene_version "1";

chr_IV gene ensembl 1802 2953 . + . gene_id "YDL248W"; gene_version "1";

chromosome_IV exon ensembl 1802 2953 . + . gene_id "YDL248W"; gene_version "1";

chromosome_IV stop_codon ensembl 2951 2953 . + 0 gene_id "YDL248W"; gene_version "1";

chr_IV transcript ensembl 3762 836 . + . gene_id "YDL247W-A"; gene_version "1";

**5.更改示例文件的权限，使得文件所有者及所在用户组用户可读、写、执行而其他用户只可读，展示权限修改前后的权限变化。**
```
# 可读/写/执行：4+2+1，仅可读：4
chmod 774 test_command.gtf
```
