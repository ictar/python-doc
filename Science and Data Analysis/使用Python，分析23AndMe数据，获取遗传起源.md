原文：[Genetic Ancestry by analysing 23AndMe Data using Python](http://online.cambridgecoding.com/notebooks/cca_admin/genetic-ancestry-analysis-python)  

---

[下载notebook](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/notebook.ipynb)

## 从公开可用的基因数据我们可以学到什么？

你的DNA包含了关于你的主线，易患疾病以及复杂特性，包括身高、体重、五官和行为等丰富的信息。使用来自23andMe，一家直接面向消费者的遗传学公司，的公众可获取数据，我们将展示如何确定在网上找到的来自23andMe的一份匿名样本的祖先。

在本教程的最后，你会学到并且能够做到三件事：
1\. 如何理解和分析来自两个不同源的基因数据 (23andMe和千人基因组计划)。
2\. 主成分分析的基本知识，一个用于无监督聚类、降维和数据探索的工具。
3\. 如何使用主成分分析来预测一个匿名基因数据集的祖先。

首先，你可以下载一组公开可用的23andMe数据。这个文件的大小大概是23Mb。我们会使用python的urllib包来下载文件。

```python

    import urllib
    
    u = urllib.URLopener()
    u.retrieve("https://s3-eu-west-1.amazonaws.com/dm-23andme-v3/dm_23andme_v3_110219.txt", "dm_23andme_v3_110219.txt")
    
```

我们将使用pandas库来加载数据为dataframe，并看看形状和内容。这个文件有一些以‘#’开头的标题行。我们将跳过这些行。

```python

    import pandas as pd
    anon = pd.read_table("dm_23andme_v3_110219.txt", sep = "\t", comment = "#", header = None)
    
```

检查数据有多少行和列，以及数据是咋样的：

```python

    print("The 23andMe datset has {} rows and {} columns.".format(anon.shape[0], anon.shape[1]))
    print(anon.head())
    
    
```

```python

    The 23andMe datset has 966977 rows and 4 columns.
                0  1       2   3
    0   rs4477212  1   72017  AA
    1   rs3094315  1  742429  AG
    2   rs3131972  1  742584  AG
    3  rs12124819  1  766409  --
    4  rs11240777  1  788822  AG
    
```

在本教程中，你只需要使用这个dataframe约1000行即可 —— 使用pandas来切出前1000行：

```python

    anon = anon.iloc[0:1000,:]
    
```

## 基因数据是如何组织的

人类基因组是一个由A / T / G / C组成的超过30亿的字母（称为“碱基”）的序列。这30亿个字母序列被分成23个不同的段，称为染色体。其中最大的染色体1号，有近2.5亿个碱基长。我们的基因有两个副本（一个来自母亲，一个来自父亲），因此，我们每个人拥有超过60亿个字母的DNA序列。

下面的示意图显示了单个染色体，以及百万个碱基对是如何被压缩并打包到染色体中的。

![](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/0321DNAMacrostructure.jpg)

在你刚刚打印的dataframe中，第二和第三列对应于染色体及位置。第四列包含在该染色体和位置的两个碱基。第一列称为rsid，它是基因变异数据库中使用的标识符。

所有这些列的命名信息位于我们加载的文件的头部，但在上面，我们用`comment = "#"`跳过了它。

让我们重命名相应的数据集的列：

```python

    anon.columns = ["rsid", "chrom", "pos", "genotype"]
    print(anon.head())
    
    
```

```python

             rsid chrom     pos genotype
    0   rs4477212     1   72017       AA
    1   rs3094315     1  742429       AG
    2   rs3131972     1  742584       AG
    3  rs12124819     1  766409       --
    4  rs11240777     1  788822       AG
    
```

## 处理来自三大人群的公开可用基因组数据

接下来，我们将下载来自于三个主要人群的样本，它是千人基因组计划编目世界各地的人类遗传变异序列化的一部分。这三组是CEU (来自犹他州的北欧人)，YRI (尼日利亚伊巴丹的约鲁巴人)以及CHB/JPT (中国汉人和东京的日本人)。

有关处理基因数据的第一个重要教训是，它可能会非常大。序列化涵盖60亿左右个位点的单个人类基因组生成的原始数据通常最少30Gb。通过减少到已知在人类群体内各异的位点子集(23andMe使用大约1百万个位点)，可以减少大小。我们所使用的匿名的23andMe数据只有23Mb。

我们将使用tabix，这一个用以按顺序索引和查询大的基因组数据集的流行工具，来从千人基因组文件中提取来自我们所感兴趣的位点的数据。tabix为我们节省了大量的时间，并让大文件更容易访问 —— 更多关于tabix的信息，见这里：<http://www.htslib.org/doc/tabix.html>。

我们会下载两个文件 - 一个是基因数据本身，另一个是由tabix生成的索引，它可以让查询更快更省内存。这个文件被压缩，大小约为1Gb (969Mb)，因此，取决于你的互联网连接，可能要花几分钟来下载它。你不需要解压缩它 —— 如果你决定解压缩，看看里面有啥，那么解压缩后大小大概为7Gb，因此，不要尝试一次性看完整个文件！

你需要运行命令'pip install -user pytabix'，才能在python中使用tabix。

```python

    u = urllib.URLopener()
    u.retrieve("ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/pilot_data/paper_data_sets/a_map_of_human_variation/low_coverage/snps/YRI.low_coverage.2010_09.genotypes.vcf.gz", "YRI.low_coverage.2010_09.genotypes.vcf.gz")
    u.retrieve("ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/pilot_data/paper_data_sets/a_map_of_human_variation/low_coverage/snps/YRI.low_coverage.2010_09.genotypes.vcf.gz.tbi", "YRI.low_coverage.2010_09.genotypes.vcf.gz.tbi")
    
```

```python

    import tabix
    
    YRI_file = "YRI.low_coverage.2010_09.genotypes.vcf.gz"
    yri = tabix.open(YRI_file)
    
    
```

基因坐标通常是基于0的，意味着一个序列中的第一个碱基将位于位置0和位置1“之间”。下面，我们将通过查询1号染色体位置742428和742429（在序列中，将会是第742429个碱基）之间的位点，检查YRI数据集，并将其赋给rs3094315变量。下面是显式0碱基 vs 1碱基坐标之间差异的简单的图形，来自这篇biostars文章：<https://www.biostars.org/p/84686/>

![](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/basicdiagram.jpg)

rs3094315是我们的23andMe数据集中的第二个变量，在YRI数据集中，它也被序列化：

```python

    rs3094315 = yri.query("1", 742428, 742429)  # an iterator object
    print(rs3094315.next())
    
```

```python

    ['1', '742429', 'rs3094315', 'G', 'A', '.', 'PASS', 'AA=g;AC=46;AN=118;DP=237;HM2;GP=1:752566;BN=103', 'GT:DP:CB', '0|1:3:SM', '1|1:4:MB', '1|0:5:SMB', '0|1:2:SMB', '1|0:6:SMB', '1|0:7:SMB', '0|1:4:SMB', '0|0:4:SMB', '1|1:0:SMB', '1|1:12:SMB', '0|1:4:SMB', '0|1:2:SMB', '1|0:4:MB', '0|0:7:SMB', '1|1:4:SMB', '0|0:4:SMB', '1|1:6:SMB', '0|0:5:SMB', '0|1:4:SMB', '1|1:5:MB', '0|0:6:SMB', '0|0:5:SMB', '0|1:1:SMB', '1|1:2:SMB', '0|0:9:SMB', '0|0:1:SMB', '0|0:10:SMB', '0|1:9:SMB', '1|0:9:SMB', '0|1:2:SMB', '0|1:8:SMB', '1|1:4:SMB', '0|1:9:SMB', '0|0:2:SMB', '1|0:5:SMB', '0|1:2:SMB', '0|0:3:SMB', '0|0:0:SMB', '0|0:4:SMB', '0|1:7:SMB', '1|0:3:SM', '0|0:2:SMB', '0|0:0:SMB', '0|1:9:SMB', '0|1:4:SMB', '0|0:1:SMB', '0|0:1:SMB', '0|0:1:SMB', '0|0:3:SMB', '1|1:2:SMB', '0|0:2:SMB', '1|0:4:SMB', '0|0:2:SMB', '0|0:2:SMB', '1|0:2:SMB', '0|0:0:SMB', '1|0:2:SMB', '1|1:3:SMB', '1|0:4:SMB']
    
```

我们要如何阅读上面的输出呢？正如你可能已经注意到的，我们的23andMe数据组织的方式相当不同。

千人基因组文件保存在VCF (variant call format)文件中，这与23andMe提供的格式不同，因此，我们将需要进行一些数据转换，以使得这两个可以一起工作。

从左到右，前五个列编码染色体、位置、rsid、参考碱基 (这是我们大部分时间希望看到的字母)、替代碱基 (这是“变量”字母，它存在于人群中的一些成员中)。更多关于VCF中包含的信息的信息，可以在这里找到：<http://www.1000genomes.org/wiki/Analysis/vcf4.0/>。这是遗传学中最常见的文件格式之一，而除了基因类型本身外的许多额外数据可以用于过滤数据，从而提高数据质量。要看看VCF中的数据，你可以创建一份你所下载的压缩的VCF文件的拷贝，解压它，然后在命令行上使用“less”来看一看。一旦解压，文件大小约为7Gb。

VCF的最后一列和(这个列表的最后一条)显示在这个最终文件中包含的59个YRI样本的基因类型。列表中的最后59条都遵循相似的格式来报告用冒号分隔的基因类型。现在，我们只对第一块信息感兴趣，它是基因类型 (它将是0|0, 1|0, 0|1, or 1|1)。

正如你可以在上面看到的，在这个位点上，我们的匿名样本基因类型是AG，它对应于上面列出的格式的中的1|0。0是参考(G)，而1是替代(A)。有几个YRI样本是1|1，这意味着它们在这个位点是AA，而0|0的YRI样本在这个位点上是GG。在我们更进一步之前，通过添加一列展示在千人基因组VCF文件中使用的格式为0和1的基因类型，我们想要将我们的23andMe数据转换成更紧密匹配我们刚刚下载的人群参考格式。(这比23andMe使用的格式更常用)。

```python

    def convert_anon_genotype(chrom, pos, genotype, vcf_tabix):
        site = vcf_tabix.query(chrom, pos - 1, pos)
        try:
            row = site.next() # this will throw an error (which is caught by 'except' on the next line) if the site we queried is not in the tabix file
        except StopIteration:
            return None # put None in the dataframe if we are missing this genotype in 1000 Genomes
        ref = row[3]
        alt = row[4]
        if genotype == ref+ref:
            return("0|0")
        elif (genotype == ref+alt) | (genotype == alt+ref):
            return("0|1")
        elif genotype == alt+alt:
            return("1|1")
        else: # missing genotype, or incorrect annotation, we assume ref/ref
            return("0|0")
    
```

```python

    genotypes_1kg_format = []
    for chrom, pos, genotype in zip(anon['chrom'], anon['pos'], anon['genotype']):
        genotypes_1kg_format.append(convert_anon_genotype(str(chrom), pos, genotype, yri))
    
    anon['genotype_1kg_format'] = genotypes_1kg_format
    print(anon.head())
    print(anon.shape)
    
```

```python

             rsid chrom     pos genotype genotype_1kg_format
    0   rs4477212     1   72017       AA                None
    1   rs3094315     1  742429       AG                 0|1
    2   rs3131972     1  742584       AG                 0|1
    3  rs12124819     1  766409       --                None
    4  rs11240777     1  788822       AG                None
    (1000, 5)
    
```

## 从基因类型创建一个特征空间

记住，我们的首要目标是预测祖先，因此想要将基因数据表示为一个特征空间。我们将构建一个dataframe，其中，行是样本（人），而我们将每一条染色体和每一个位置上的基因类型当成一个单独的特征。由于我们将匿名23andMe样本限制为前一千个位点，因此我们的dataframe将有两个列，描述每个样本的人群和名字，加上另外1000个列 (每个特性一个)。

```python

    # make a data frame with one row for each of the YRI samples
    yri_genotypes = pd.DataFrame({"sample": ["YRI" + str(i) for i in range(1, 60)], "population": "YRI"})
    print(yri_genotypes.head())
    
```

```python

      population sample
    0        YRI   YRI1
    1        YRI   YRI2
    2        YRI   YRI3
    3        YRI   YRI4
    4        YRI   YRI5
    
```

要获取每个人的基因类型，我们要写一个使用pytabix的函数：

```python

    # extract genotype information for a set of sites
    def extract_genotype(chrom, pos, vcf_tabix):
        site = vcf_tabix.query(chrom, pos - 1, pos)
        try:
            g = site.next()[9:]
        except StopIteration:
            return None # put None in the dataframe if we are missing this genotype in 1000 Genomes
        g = [i.split(":")[0] for i in g]  # if present in 1000 genomes, get the genotypes
        return(g)      
```

```python

    for rsid, chrom, pos in zip(anon['rsid'], anon['chrom'], anon['pos']):
        g = extract_genotype(str(chrom), pos, yri)
        yri_genotypes[rsid] = g
    
```

```python

    print("The dataframe including all of the samples from the YRI population has {} samples and {} genotypes.".format(yri_genotypes.shape[0], yri_genotypes.shape[1] - 2))
    
```

```python

    The dataframe including all of the samples from the YRI population has 59 samples and 1000 genotypes.
    
```

所有带有'None'的列都是由23andMe测序，但在我们的YRI数据集中没有的位点。稍后，我们将丢弃所有在我们任意人群中缺失数据的位点。

```python

    print(yri_genotypes.iloc[0:10, 0:7])
    
```

```python

      population sample rs4477212 rs3094315 rs3131972 rs12124819 rs11240777
    0        YRI   YRI1      None       0|1       0|1       None       None
    1        YRI   YRI2      None       1|1       1|0       None       None
    2        YRI   YRI3      None       1|0       1|0       None       None
    3        YRI   YRI4      None       0|1       0|1       None       None
    4        YRI   YRI5      None       1|0       1|0       None       None
    5        YRI   YRI6      None       1|0       0|0       None       None
    6        YRI   YRI7      None       0|1       0|1       None       None
    7        YRI   YRI8      None       0|0       0|0       None       None
    8        YRI   YRI9      None       1|1       1|1       None       None
    9        YRI  YRI10      None       1|1       1|0       None       None
    
```

现在，我们要下载数据集，然后为CEU (犹他州的欧洲人)和CHB/JPT (中国汉族/日本人)创建数据帧。再次，应该在命令行使用curl或者wget来下载这些文件和tabix索引：

```python

    u = urllib.URLopener()
    u.retrieve("ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/pilot_data/paper_data_sets/a_map_of_human_variation/low_coverage/snps/CEU.low_coverage.2010_09.genotypes.vcf.gz", "CEU.low_coverage.2010_09.genotypes.vcf.gz")
    u.retrieve("ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/pilot_data/paper_data_sets/a_map_of_human_variation/low_coverage/snps/CEU.low_coverage.2010_09.genotypes.vcf.gz.tbi", "CEU.low_coverage.2010_09.genotypes.vcf.gz.tbi")
    u.retrieve("ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/pilot_data/paper_data_sets/a_map_of_human_variation/low_coverage/snps/CHBJPT.low_coverage.2010_09.genotypes.vcf.gz", "CHBJPT.low_coverage.2010_09.genotypes.vcf.gz")
    u.retrieve("ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/pilot_data/paper_data_sets/a_map_of_human_variation/low_coverage/snps/CHBJPT.low_coverage.2010_09.genotypes.vcf.gz.tbi", "CHBJPT.low_coverage.2010_09.genotypes.vcf.gz.tbi")
    
    
```

```python

    # europeans from utah
    CEU_file = "CEU.low_coverage.2010_09.genotypes.vcf.gz"
    ceu = tabix.open(CEU_file)
    
    number_ceu_samples = len(ceu.query("1", 742428, 742429).next()[9:])
    
    ceu_genotypes = pd.DataFrame({"sample": ["CEU" + str(i) for i in range(1, number_ceu_samples + 1)], "population": "CEU"})
    
    # Han chinese and Japanese
    CHBJPT_file = "CHBJPT.low_coverage.2010_09.genotypes.vcf.gz"
    chbjpt = tabix.open(CHBJPT_file)
    
    number_chbjpt_samples = len(chbjpt.query("1", 742428, 742429).next()[9:])
    
    chbjpt_genotypes = pd.DataFrame({"sample": ["CHBJPT" + str(i) for i in range(1, number_chbjpt_samples + 1)], "population": "CHBJPT"})
    
    
```

```python

    for rsid, chrom, pos in zip(anon['rsid'], anon['chrom'], anon['pos']):
        yri_genotypes[rsid] =  extract_genotype(str(chrom), pos, yri)
        ceu_genotypes[rsid] =  extract_genotype(str(chrom), pos, ceu)
        chbjpt_genotypes[rsid] =  extract_genotype(str(chrom), pos, chbjpt)
    
```

此时，我们有了来自我们感兴趣的三个人群的三个独立的dataframe，包含它们在1000个不同位置的基因类型。我们的目标是看看忽略原有的人群标签，仅仅基于其遗传信息，我们是否能够将这些样本分离到各个人群中。

```python

    genotypes = yri_genotypes.copy()
    genotypes = genotypes.append(ceu_genotypes, ignore_index=True)
    genotypes = genotypes.append(chbjpt_genotypes, ignore_index=True)
    
    print("Now the genotypes data frame has {} samples and {} genotypes").format(genotypes.shape[0], genotypes.shape[1]-2)
    
```

```python

    Now the genotypes data frame has 179 samples and 1000 genotypes
    
```

## 使用主成分分析的无监督聚类

要尝试将我们的数据分离到人群，我们将使用主成分分析 (PCA)，这是一个无监督方法，它对于将来自于大的特征空间的数据整合成一个较小集合的特性，以捕获样本间的大部分差异非常有用。

PCA需要一个高维的特征空间 (在本例中，是10个特性)，并使用所有特性的线性组合创建一个捕获样本间差异的“主成分”。我们的数据可以被想象成高维空间中的点云。第一个主成分是通过这个高维空间的一条线。第二个主成分必须正交（成90度角）于第一个主成分，并构造以捕获第二大的变异量。

另一种想象主成分的方法是，作为高维数据的“投影”或“影子”到低维空间。下面的图显示了一个在二维空间中可视化思维特性空间的例子。

这张图来自于一个scikit-learn教程，它使用非常流行的Iris数据集的数据：<http://scikit-learn.org/stable/auto_examples/decomposition/plot_pca_vs_lda.html>

![](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/plotpcavslda001.png)

现在，我们将在我们的基因数据集上使用PCA，尝试可视化我们的样本间的差异。我们会使用scikit-learn库来进行PCA。下面，我们将基因类型数据转换成连续值。有参考字母的两份拷贝的样本设为0，一份参考拷贝和一份替代拷贝的设为0.5，有两份替代拷贝的设为1.0。另外，我们会丢弃千人基因组的人群面板中存在'None'的任何变量，因为它们并不含信息。

```python

    from sklearn.decomposition import PCA
    pca = PCA(n_components = 2)
    
    genotypes_only = genotypes.copy().iloc[:, 2:]  # we make a copy here, otherwise pandas will gripe at us!
    genotypes_only[genotypes_only == "1|1"] = 1
    genotypes_only[genotypes_only == "0|1"] = 0.5
    genotypes_only[genotypes_only == "0/1"] = 0.5
    genotypes_only[genotypes_only == "1|0"] = 0.5
    genotypes_only[genotypes_only == "0|0"] = 0.0
    
    # remove variants with None
    genotypes_only = genotypes_only.dropna(axis=1)
    
    
```

```python

    import matplotlib.pyplot as plt
    %matplotlib inline
    
```

```python

    pca.fit(genotypes_only)
    pc = pca.transform(genotypes_only)
    
    plt.figure(figsize=(10,6))
    plt.scatter(pc[:, 0], pc[:, 1])
    plt.title('PCA of 1000 23andMe SNPs')
    plt.xlabel('PC1')
    plt.ylabel('PC2')
    plt.show()
    
    
```

![png](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/notebook_files/notebook_42_0.png)

使用来自我们的23andMe数据集的少于一千个位点(记住，这只是23andMe数据集中总共近1百万个位点的一小部分)，我们看到，我们的数据被非常清晰地分到三个集群中。如果我们把人群标签加回去，那么可以看到，这些集群代表了我们开始的三个人群。对于这个特定的分析，PCA一个最强大的部分是，一开始，我们无需指定寻找的集群数。

```python

    import numpy as np
    
    plt.figure(figsize=(10,6))
    
    for c, pop in zip("rby", ["YRI", "CEU", "CHBJPT"]):
        plt.scatter(pc[np.where(genotypes['population'] == pop), 0], pc[np.where(genotypes['population'] == pop), 1], c = c, label = pop)
    plt.title('PCA of 1000 23andMe SNPs')
    plt.xlabel('PC1')
    plt.ylabel('PC2')
    plt.legend(loc = 'upper left')
    plt.show()
    
```

![png](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/notebook_files/notebook_44_0.png)

分析还告诉我们的最重要的事情之一是，如果我们对从我们的基因数据中学到除了人们的祖先来自哪里的其他东西感兴趣，那么需要控制人群结构 (例如，利用人群/祖先作为协变量)。当我们尝试了解对于常见疾病(例如，糖尿病，肥胖，心脏疾病，遗传性癌症)的易感性，基因所扮演的角色，那么我们必须确保我们的观察组(具有某疾病的人)和控制组(不具有某疾病的人)的祖先并不会有很大差异。

对PCA图另一个有趣的观察室，在第一个主成分中，约鲁巴集群（红色）从中国人/日本人和欧洲人集群中分离开来。这是在非洲的现代人类起源和随后迁移出非洲并进入欧洲/亚洲的反映。然后，第二个主成分反映了后续的迁移导致了中国人/日本人集群和欧洲人集群之间的基因差异。

在这张地图中，我们的匿名23andMe样本落在哪里呢？23andMe数据格式与VCF不同，因此，我们会写一个快速的函数来检查VCF (使用tabix)，以映射23andMe基因类型，这将把字母对应到我们现在习惯了的0|0，0|1和1|1。

```python

    # keep only the genotypes used in our PCA above
    anon = anon.loc[anon['rsid'].isin(genotypes_only.columns.values), :]  # only keep the 23andMe data where we have no missing data in 1000 genomes
    
    anon_genotypes = anon.copy()["genotype_1kg_format"]
    
    anon_genotypes[anon_genotypes == "1|1"] = 1
    anon_genotypes[anon_genotypes == "0|1"] = 0.5
    anon_genotypes[anon_genotypes == "1|0"] = 0.5
    anon_genotypes[anon_genotypes == "0|0"] = 0.0
    #anon_genotypes[anon_genotypes == None] = 0.0
    anon_genotypes = anon_genotypes.reshape(1,-1) # reshape, otherwise sci-kit learn will throw a deprecation warning
    
    # assume any missing data in our 23andme sample is ref/ref
    #anon_genotypes[anon_genotypes is None] = "0|0"
    
    anon_pca = pca.transform(anon_genotypes)  # pca was fit on the 1000 genomes data and we use it to transform the anonymous genotypes
    
```

在上面的第一行中，我们丢弃了那些不属于千人基因组位点的23andMe位点，因为为了在主成分空间中计算匿名样本的值，我们必须有两个数据集（匿名23andMe和千人基因组）之间的相同的基础数据。

```python

    print(anon.head())
    
```

```python

            rsid chrom     pos genotype genotype_1kg_format
    1  rs3094315     1  742429       AG                 0|1
    2  rs3131972     1  742584       AG                 0|1
    6  rs4970383     1  828418       CC                 0|0
    7  rs4475691     1  836671       CC                 0|0
    8  rs7537756     1  844113       AA                 0|0
    
```

```python

    plt.figure(figsize=(10,6))
    
    for c, pop in zip("rgb", ["YRI", "CEU", "CHBJPT"]):
        plt.scatter(pc[np.where(genotypes['population'] == pop), 0], pc[np.where(genotypes['population'] == pop), 1], c = c, label = pop)
    
    # take the code above and add in the anonymous sample
    
    plt.scatter(anon_pca[0,0], anon_pca[0,1], c = "yellow", label = "Anonymous 23andMe Sample", marker = (5,1,0), s = 200)
    
    plt.title('PCA of 1000 23andMe SNPs')
    plt.xlabel('PC1')
    plt.ylabel('PC2')
    plt.legend(loc = 'upper left')
    plt.show()
    
```

![png](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/notebook_files/notebook_50_0.png)

正如我们在上面图中所看到的那样，我们的匿名样本（黄色的星星）正对欧洲人集群。在实践中，一个人群参考板比这里使用的三个具有多得多的子人群。本教程展示了如何用几个Python段进行简单的祖先分析，但我们可以更进一步，使用一个更复杂的模型来预测祖先。

千人基因组计划的数据集的最终版本拥有来自26个不同人群的数据 (<http://www.1000genomes.org/category/population/)>。在这种情况下，将来自千人基因组计划的基因数据当成训练数据，例如使用高斯混合模型或K-近邻，你就可以构建一个分类器来预测比例祖先 (例如，1/2的爱尔兰，1/2中国汉人)。

除了祖先，基因数据可以用于预测疾病风险，药物副作用，甚至构建脸部模型 (<https://www.newscientist.com/article/mg22129613-600-genetic-mugshot-recreates-faces-from-nothing-but-dna/)>。

回来看看关于使用Python分析基因数据的更多免费教程，或者注册我们的数据科学课程！

### 背景

![](https://s3-eu-west-1.amazonaws.com/com.cambridgecoding.students/cca_admin/notebooks/040adfad7bc0d533a407c5ecb1f46e0f/sangerheadshotPJS.JPG)

Patrick是剑桥大学和威康信托基金会桑格研究所的一名数学基因组学和医学的博士研究生，学习严重的神经发育障碍中基因突变的作用。在他的博士学位之前，Patrick在北卡罗来纳大学教堂山分校学习应用数学和定量生物学。

* * *
