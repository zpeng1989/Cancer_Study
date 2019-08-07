# 不断扩大的癌症基因组学笔记和数据收集

欢迎有问题的建议和拉请求!


* [工具](#工具)
  * [肿瘤含量](#肿瘤含量)
  * [免疫细胞分析](#免疫细胞分析)
  * [BRCA](#brca)
  * [TCGA泛癌肿](#tcga泛癌肿)
  * [图片分析](#图片分析)
  * [克隆分析](#克隆分析)
* [生存分析](#生存分析)
  * [查找生存分析最佳分割点](#查找生存分析最佳分割点)
* [癌症驱动基因](#癌症驱动基因)
* [癌症驱动突变](#癌症驱动突变)
* [药物](#药物)
* [数据](#数据)
  * [TCGA](#tcga)
* [甲基化](#甲基化)


## 工具


### 肿瘤含量

- `ABSOLUTE` - 从SNPs、CNVs推断肿瘤纯度、倍数。还可检测亚克隆异质性。
    - Carter, Scott L., Kristian Cibulskis, Elena Helman, Aaron McKenna, Hui Shen, Travis Zack, Peter W. Laird, et al. “Absolute Quantification of Somatic DNA Alterations in Human Cancer.” Nature Biotechnology 30, no. 5 (May 2012): 413–21. https://doi.org/10.1038/nbt.2203.
    - Aran, Dvir, Marina Sirota, and Atul J. Butte. “Systematic Pan-Cancer Analysis of Tumour Purity.” Nature Communications 6, no. 1 (December 2015). https://doi.org/10.1038/ncomms9971. - TCGA肿瘤纯度评估采用四种方法: ESTIMATE, ABSOLUTE, LUMP, IHC, 和中位数一致性进行评估。 基因表达与纯度有关，可能影响相关和差异表达检测分析 - `data/ABSOLUTE_scores.xlsx` - Supplementary Data 1: 根据四种方法估计肿瘤纯度，并对所有TCGA样本用现有数据进行一致性分析. [Source](https://media.nature.com/original/nature-assets/ncomms/2015/151204/ncomms9971/extref/ncomms9971-s2.xlsx)

- `ESTIMATE` (利用表达数据估计恶性肿瘤组织中的间质细胞和免疫细胞)是预测肿瘤纯度的工具，利用基因表达数据预测肿瘤组织中是否存在浸润的间质/免疫细胞。评估算法基于单样本基因集富集分析，生成三个分数:间质分数(捕获肿瘤组织中间质的存在)、免疫分数(表示肿瘤组织中免疫细胞的浸润)和评估分数(推断肿瘤纯度)。 http://bioinformatics.mdanderson.org/main/ESTIMATE:Overview. R包 http://bioinformatics.mdanderson.org/estimate/rpackage.html
    - Yoshihara, Kosuke, Maria Shahmoradgoli, Emmanuel Martínez, Rahulsimham Vegesna, Hoon Kim, Wandaliz Torres-Garcia, Victor Treviño, et al. “Inferring Tumour Purity and Stromal and Immune Cell Admixture from Expression Data.” Nature Communications 4 (2013): 2612. https://doi.org/10.1038/ncomms3612. - ESTIMATE - 肿瘤间质纯度检测。141免疫和间质基因。single-sample GSEA分析。评估得分作为免疫和间质评分的组合。 [Supplementary data](https://www.nature.com/articles/ncomms3612#supplementary-information).
- `data/ESTIMATE_signatures.xlsx` - 间质和免疫特征的基因列表。 [Source](https://media.nature.com/original/nature-assets/ncomms/2013/131011/ncomms3612/extref/ncomms3612-s2.xlsx)
- `data/ESTIMATE_scores.xlsx` - TCGA数据集中间质、免疫和评估得分的列表。所有的癌症，所有的基因表达形式。[Source](https://media.nature.com/original/nature-assets/ncomms/2013/131011/ncomms3612/extref/ncomms3612-s3.xlsx)

- `ISOpureR` - 反卷积策略计算肿瘤轮廓，以提高肿瘤纯度。基于回归的方法，利用纯化的肿瘤剖面来估计肿瘤样本的比例。讨论过参数化引起的过拟合。 https://cran.r-project.org/web/packages/ISOpureR/index.html
    - Quon, Gerald, Syed Haider, Amit G Deshwar, Ang Cui, Paul C Boutros, and Quaid Morris. “Computational Purification of Individual Tumor Gene Expression Profiles Leads to Significant Improvements in Prognostic Prediction.” Genome Medicine 5, no. 3 (2013): 29. https://doi.org/10.1186/gm433.

### 免疫细胞分析

- `Immunophenogram` - 癌症中免疫细胞类型的划分。 https://github.com/mui-icbi/Immunophenogram, https://tcia.at/home
    - Charoentong, Pornpimol, Francesca Finotello, Mihaela Angelova, Clemens Mayer, Mirjana Efremova, Dietmar Rieder, Hubert Hackl, and Zlatko Trajanoski. “Pan-Cancer Immunogenomic Analyses Reveal Genotype-Immunophenotype Relationships and Predictors of Response to Checkpoint Blockade.” BioRxiv, 2016, 056101. - https://tcia.at/ - 癌症中的免疫细胞。使用功能GSEA富集和CIBERSORT进行估计。  https://github.com/MayerC-imed/Immunophenogram

- `ImmQuant` - 反向卷积计算免疫谱系. http://csgi.tau.ac.il/ImmQuant/downloads.html
    - Frishberg, Amit, Avital Brodt, Yael Steuerman, and Irit Gat-Viks. “ImmQuant: A User-Friendly Tool for Inferring Immune Cell-Type Composition from Gene-Expression Data.” Bioinformatics 32, no. 24 (December 15, 2016): 3842–43. https://doi.org/10.1093/bioinformatics/btw535.

- `TIMER` - 免疫细胞浸润的六种类型，对生存的影响。 使用CHAT R包计算肿瘤纯度。估计免疫细胞丰度的线性回归。 巨噬细胞浸润预示更糟的结果，包括BRCA。合并RNA-seq使用批量删除，过滤所有TCGA处理。数据位置 http://cistrome.org/TIMER/download.html, 工具位置 https://cistrome.shinyapps.io/timer/
    - Li, Bo, Eric Severson, Jean-Christophe Pignon, Haoquan Zhao, Taiwen Li, Jesse Novak, Peng Jiang, et al. “Comprehensive Analyses of Tumor Immunity: Implications for Cancer Immunotherapy.” Genome Biology 17, no. 1 (22 2016): 174. https://doi.org/10.1186/s13059-016-1028-7.


### BRCA

- `TNBCtype` 分类三阴性乳腺癌样本(微阵列基因表达)分为六个亚型, http://cbc.mc.vanderbilt.edu/tnbc/index.php

- `genefu` PAM50 R包分类预测生存https://www.bioconductor.org/packages/release/bioc/html/genefu.html

### TCGA

- Zhang, Zhuo, Hao Li, Shuai Jiang, Ruijiang Li, Wanying Li, Hebing Chen, and Xiaochen Bo. “A Survey and Evaluation of Web-Based Tools/Databases for Variant Analysis of TCGA Data.” Briefings in Bioinformatics, March 29, 2018. https://doi.org/10.1093/bib/bby023. - 最全面的tcga相关工具综述。 Table 3 - Web服务器和数据库列表。

- NCI基因组数据通用API. https://docs.gdc.cancer.gov/API/Users_Guide/Getting_Started/ - 文档. https://github.com/Bioconductor/GenomicDataCommons - R 包
    - Shane Wilson, Michael Fitzsimons, Martin Ferguson, Allison Heath, Mark Jensen, Josh Miller, Mark W. Murphy, James Porter, Himanso Sahni, Louis Staudt, Yajing Tang, Zhining Wang, Christine Yu, Junjun Zhang, Vincent Ferretti and Robert L. Grossman. "Developing Cancer Informatics Applications and Tools Using the NCI Genomic Data Commons API." DOI: 10.1158/0008-5472.CAN-17-0598 Published November 2017 http://cancerres.aacrjournals.org/content/77/21/e15

## 图片分析

- `DeepPATH` - 利用深卷积神经网络对肺癌图像进行分类。按肿瘤类型、突变类型分类。参考其他使用深度学习的图像分类研究。GoogleNet inception v3架构。培训、验证、测试团队(70%、15%、15%)。图像处理的细节。 https://github.com/ncoudray/DeepPATH
    - Coudray, Nicolas, Paolo Santiago Ocampo, Theodore Sakellaropoulos, Navneet Narula, Matija Snuderl, David Fenyö, Andre L. Moreira, Narges Razavian, and Aristotelis Tsirigos. “Classification and Mutation Prediction from Non–Small Cell Lung Cancer Histopathology Images Using Deep Learning.” Nature Medicine 24, no. 10 (October 2018): 1559–67. https://doi.org/10.1038/s41591-018-0177-5.


- `IHCount` - IHC-分析软件, https://github.com/mui-icbi/IHCount

- `pathology_learning` - 利用传统的机器学习和深度学习方法对TCGA病理切片进行预测。 [https://github.com/millett/pathology_learning](https://github.com/millett/pathology_learning)

## 克隆分析

- `Awesome-CancerEvolution` - 研究癌症进化的论文和工具列表。 https://github.com/iron-lion/Awesome-CancerEvolution

- `E-scape` - 癌症进化可视化。时间序列分析, http://bioconductor.org/packages/release/bioc/html/timescape.html, MapScape - 空间分布,http://bioconductor.org/packages/release/bioc/html/mapscape.html, CellScape - single-cell 进化, http://bioconductor.org/packages/release/bioc/html/cellscape.html
    - Smith, Maia A., Cydney B. Nielsen, Fong Chun Chan, Andrew McPherson, Andrew Roth, Hossein Farahani, Daniel Machev, Adi Steif, and Sohrab P. Shah. “E-Scape: Interactive Visualization of Single-Cell Phylogenetics and Cancer Evolution.” Nature Methods 14, no. 6 (30 2017): 549–50. https://doi.org/10.1038/nmeth.4303.

- `fishplot` - 创建显示肿瘤克隆结构变化的时间进程"fish plots" . https://github.com/chrisamiller/fishplot

- `MACHINA` - 转移和克隆史综合分析。 https://github.com/raphael-group/machina 

- `PyClone` - 从深度测序的数据推断点突变的细胞普遍率。 http://compbio.bccrc.ca/software/pyclone/

- `clonevol` R package, 在多样本癌症测序中推断和可视化克隆进化. https://github.com/hdng/clonevol
    - Dang, H. X., B. S. White, S. M. Foltz, C. A. Miller, J. Luo, R. C. Fields, and C. A. Maher. “ClonEvol: Clonal Ordering and Visualization in Cancer Sequencing.” Annals of Oncology: Official Journal of the European Society for Medical Oncology 28, no. 12 (December 1, 2017): 3076–82. https://doi.org/10.1093/annonc/mdx517.


- `SciClone` - 通过分析体细胞突变的变异等位基因频率，分析肿瘤亚克隆的数目和遗传组成。. https://github.com/genome/sciclone

- `ape` - 系统发育和进化分析, https://cran.r-project.org/web/packages/ape/index.html

- `DeconstructSig` - 已知SNP肿瘤突变特征对肿瘤样本的贡献预测克隆。 https://github.com/raerose01/deconstructSigs 



## 生存分析

- `cBioPortal` - 癌症基因组学cBioPortal提供大规模癌症基因组数据集的可视化、分析和下载。OncoPrint突变图，差异表达，共表达，存活。比较基因表达与拷贝数变异。 http://www.cbioportal.org/

- `R2` - 基因组分析可视化平台。以基因为中心，生存分析，预处理RNA微阵列研究的收集. http://hgserver1.amc.nl/

- `KM plotter` - Gene-centric, 可定制的乳腺癌、卵巢癌、肺癌、胃癌的生存分析. http://kmplot.com/
    - Györffy, Balazs, Andras Lanczky, Aron C. Eklund, Carsten Denkert, Jan Budczies, Qiyuan Li, and Zoltan Szallasi. “An Online Survival Analysis Tool to Rapidly Assess the Effect of 22,277 Genes on Breast Cancer Prognosis Using Microarray Data of 1,809 Patients.” Breast Cancer Research and Treatment 123, no. 3 (October 2010): 725–31. https://doi.org/10.1007/s10549-009-0674-9.

- `The Human Protein Atlas` - 基因和蛋白质在多种癌症组织、细胞系中的表达数据。简单的单基因搜索，总结组织特异性表达，生存意义。 http://www.proteinatlas.org/
    - Uhlen, Mathias, Cheng Zhang, Sunjae Lee, Evelina Sjöstedt, Linn Fagerberg, Gholamreza Bidkhori, Rui Benfeitas, et al. “A Pathology Atlas of the Human Cancer Transcriptome.” Science (New York, N.Y.) 357, no. 6352 (August 18, 2017). doi:10.1126/science.aan2507. http://science.sciencemag.org/content/357/6352/eaan2507. Rich downloadable data - tissue-specific gene expression in cancer and normal, isoform expression, protein expression. http://www.proteinatlas.org/about/download. http://science.sciencemag.org/content/suppl/2017/08/16/357.6352.eaan2507.DC1  
        - `Table S2` - 在正常组织和癌组织中，每种基因的组织特异性表达概述。 
        - `Table S6` - 生存预后价值的总结，每个基因都有一个简单的“有利/不利”标签。每个工作表对应一个不同的癌症。  
        - `Table S8` - 每个基因的总结，在其中癌症是生存的预后。

- `PRECOG` - 从基因组谱预测临床结果。以基因为中心，快速概述一个基因在所有癌症中的生存效应，KM图。 https://precog.stanford.edu
    - Gentles, Andrew J., Aaron M. Newman, Chih Long Liu, Scott V. Bratman, Weiguo Feng, Dongkyoon Kim, Viswam S. Nair, et al. “The Prognostic Landscape of Genes and Infiltrating Immune Cells across Human Cancers.” Nature Medicine 21, no. 8 (August 2015): 938–45. https://doi.org/10.1038/nm.3909. -TCGA泛癌生存分析PRECOG, CIBERSORT。39种的癌症。包括异质性、z分数等描述，批处理效果对z分数没有显著影响，2/3的癌症预后基因是相同的，常染色体聚类方法等。

- `GEPIA` - TCGA数据的单基因和多基因分析。基因表达在不同肿瘤正常比较，差异表达基因，相关分析，相似基因，生存分析。 http://gepia.cancer-pku.cn/
    - Zefang Tang et al., “GEPIA: A Web Server for Cancer and Normal Gene Expression Profiling and Interactive Analyses,” Nucleic Acids Research 45, no. W1 (July 3, 2017): W98–102, https://doi.org/10.1093/nar/gkx247. - 经典分析-差异表达分析，图谱绘制，相关分析，患者生存分析，相似基因检测和降维分析. http://gepia.cancer-pku.cn/
- `GEPIA2` -  TCGA分析。癌症subtype-specific分析。八种类型的表达分析，以及附加的癌症亚型分类器和表达比较。用于API访问的Python包。 http://gepia2.cancer-pku.cn
    - Tang, Zefang, Boxi Kang, Chenwei Li, Tianxiang Chen, and Zemin Zhang. “GEPIA2: An Enhanced Web Server for Large-Scale Expression Profiling and Interactive Analysis.” Nucleic Acids Research, May 22, 2019. https://doi.org/10.1093/nar/gkz430.


- `UALCAN` - 以基因为中心，肿瘤正常表达，生存分析，TCGA癌症。 http://ualcan.path.uab.edu/
    - Chandrashekar DS, Bashel B, Balasubramanya SAH, Creighton CJ, Rodriguez IP, Chakravarthi BVSK and Varambally S. UALCAN: A portal for facilitating tumor subgroup gene expression and survival analyses. Neoplasia. 2017 Aug;19(8):649-658. doi: 10.1016/j.neo.2017.05.002 [PMID:28732212]

- `Project Betastasis` - 以基因为中心，生存分析，基因表达，选择癌症研究。 http://www.betastasis.com/

- `OncoLnc` - 基因中心，任何TCGA癌症的生存分析。 http://www.oncolnc.org/

### 查找生存分析最佳分割点

- `KMplotter` - Kaplan Meier绘图仪能够通过18,674个癌症样本评估54,675个基因对生存率的影响。其中包括5143名乳腺癌患者，1816名卵巢癌患者，2437名肺癌患者，364名肝癌患者，1065名胃癌患者，无复发和总体生存数据。miRNA子系统还包括来自20种不同癌症类型的11,456个样本。该工具的主要目的是基于meta分析的生物标志物评估。
    - Györffy, Balazs, Andras Lanczky, Aron C. Eklund, Carsten Denkert, Jan Budczies, Qiyuan Li, and Zoltan Szallasi. “An Online Survival Analysis Tool to Rapidly Assess the Effect of 22,277 Genes on Breast Cancer Prognosis Using Microarray Data of 1,809 Patients.” Breast Cancer Research and Treatment 123, no. 3 (October 2010): 725–31. https://doi.org/10.1007/s10549-009-0674-9. - 通过RNA表达分析最佳分割点；

- `ctree` 自动切断发现和建立一个多元协变量的回归树。 `partykit::ctree()`. 
    - Hothorn, Torsten, Kurt Hornik, and Achim Zeileis. “Ctree: Conditional Inference Trees.” The Comprehensive R Archive Network, 2015, 1–34.

- `Cutoff Finder` - web工具，用于寻找关于结果或生存变量的最优二分法。五个方法. http://molpath.charite.de/cutoff/
    - Budczies, Jan, Frederick Klauschen, Bruno V. Sinn, Balázs Győrffy, Wolfgang D. Schmitt, Silvia Darb-Esfahani, and Carsten Denkert. “Cutoff Finder: A Comprehensive and Straightforward Web Application Enabling Rapid Biomarker Cutoff Optimization.” PloS One 7, no. 12 (2012): e51862. https://doi.org/10.1371/journal.pone.0051862.


## 癌症驱动基因

- Cancer Gene Census (CGC), 下载 [COSMIC](http://cancer.sanger.ac.uk/cosmic/download)
    - Hudson, T. J. et al. International network of cancer genome projects. Nature 464, 993–8 (2010).
    - `data/Census_all*.csv` - [The cancer Gene Census](http://cancer.sanger.ac.uk/census)
    - `data/COSMIC_genes.txt` - ：根据与之相关的记录数量对基因进行排序。获得使用 `zcat <CosmicCompleteTargetedScreensMutantExport.tsv.gz | sed '1d' | cut -f1 | sort | uniq -c | sort -k1 -r -n > COSMIC_genes.txt`
    - `data/CosmicCodingMuts.vcf.gz` - VCF file.

- Tumor suppressor gene database (TSGene), https://bioinfo.uth.edu/TSGene/
    - Zhao, M., Sun, J. & Zhao, Z. TSGene: a web resource for tumor suppressor genes. Nucleic Acids Res, 41(Database issue), D970–6 (2013).
    - Download various lists of tumor suppressor genes, https://bioinfo.uth.edu/TSGene/download.cgi

- `OncoScape` - 致癌基因/抑癌基因/综合得分作为基因表达、体细胞突变、DNA拷贝数和甲基化以及shRNA敲除筛选数据。 http://oncoscape.nki.nl/
    - Schlicker, Andreas, Magali Michaut, Rubayte Rahman, and Lodewyk F. A. Wessels. “OncoScape: Exploring the Cancer Aberration Landscape by Genomic Data Fusion.” Scientific Reports 6 (20 2016): 28103. https://doi.org/10.1038/srep28103.

- `data/Bailey_2018_cancer_genes.xlsx` - Table S1, 癌症驱动基因的一致列表
	- Bailey, Matthew H., Collin Tokheim, Eduard Porta-Pardo, Sohini Sengupta, Denis Bertrand, Amila Weerasinghe, Antonio Colaprico, et al. “Comprehensive Characterization of Cancer Driver Genes and Mutations.” Cell 173, no. 2 (April 5, 2018): 371-385.e18. https://doi.org/10.1016/j.cell.2018.02.060. - 泛癌肿分析 包括26种软件 (https://www.cell.com/cell/fulltext/S0092-8674(18)30237-X#secsectitle0075, 及使用方法) 299个癌症驱动基因，大于3400个假定的错义驱动突变的分析

- `data/TARGET_db_v3_02142015.xlsx` - TARGET(与基因组驱动治疗相关的肿瘤改变)是一个基因数据库，当癌症发生体细胞改变时，这些基因与临床行为直接相关。靶基因可以预测治疗的反应或耐药性、预后和/或诊断 https://software.broadinstitute.org/cancer/cga/target

- `data/Tokheim_2016_cancer_driver_genes.xlsx` - Dataset S2:用多种方法预测驱动基因
    - Tokheim, Collin J., Nickolas Papadopoulos, Kenneth W. Kinzler, Bert Vogelstein, and Rachel Karchin. “Evaluating the Evaluation of Cancer Driver Genes.” Proceedings of the National Academy of Sciences 113, no. 50 (December 13, 2016): 14330–35. https://doi.org/10.1073/pnas.1616440113. - 20/20+机器学习方法，比率计量学方法预测癌症驱动基因。其他方法的性能比较，20/20+、TUSON、OncodriveFML和MutsigCV表现最好. https://github.com/KarchinLab/2020plus

## 癌症驱动突变

- `clinvar` -  将ClinVar数据转换为以制表符分隔的平面文件的工具，还提供了生成的以制表符分隔的文件。 https://github.com/macarthur-lab/clinvar

- `CANCERSIGN` - 识别3-mer和5-mer突变签名，通过签名聚类样本。基于Alexandrov方法，非负矩阵分解处理获得。 compared in Table 1. https://github.com/ictic-bioinformatics/CANCERSIGN
    - Bayati, Masroor, Hamid Reza Rabiee, Mehrdad Mehrbod, Fatemeh Vafaee, Diako Ebrahimi, Alistair Forrest, and Hamid Alinejad-Rokny. “CANCERSIGN: A User-Friendly and Robust Tool for Identification and Classification of Mutational Signatures and Patterns in Cancer Genomes.” BioRxiv, January 1, 2019, 424960. https://doi.org/10.1101/424960.


## 药物

- `CARE` - 从药物靶基因与其他基因的相互作用中鉴定生物标志物。具有交互项的多元线性建模。说明BRAF突变与EGFR表达相互作用的例子。基因表达相关性与护理评分的样本分离更能预测生存率。比较相关，支持向量回归. http://care.dfci.harvard.edu/, download page http://care.dfci.harvard.edu/download/, nls_logsig tool to compute AUC for dose curves.
    - Jiang, Peng, Winston Lee, Xujuan Li, Carl Johnson, Jun S. Liu, Myles Brown, Jon Christopher Aster, and X. Shirley Liu. “Genome-Scale Signatures of Gene Interaction from Compound Screens Predict Clinical Efficacy of Targeted Cancer Therapies.” Cell Systems 6, no. 3 (March 2018): 343-354.e5. https://doi.org/10.1016/j.cels.2018.01.009.


- `CellMinerCDB` - 癌症细胞系的基因组学(基因表达、突变、拷贝数、甲基化和蛋白质表达)和药物基因组学(药物反应和基因组相互作用)分析。 结合 NCI-60, GDSC, CCLE, CTRP, 和 NCI-SCLC 数据库上建立 `rcellminer` R 包.相关和多元分析。组织分析. https://discover.nci.nih.gov/cellminercdb/,
    - Rajapakse, Vinodh N., Augustin Luna, Mihoko Yamade, Lisa Loman, Sudhir Varma, Margot Sunshine, Francesco Iorio, et al. “CellMinerCDB for Integrative Cross-Database Genomics and Pharmacogenomics Analyses of Cancer Cell Lines.” IScience 10 (December 2018): 247–64. https://doi.org/10.1016/j.isci.2018.11.029.

- `DSigDB` - drug-gene 数据库. D1(批准药物)，D2(激酶抑制剂)，D3(微扰剂标记)，D4(计算预测). 下载及在线. http://tanlab.ucdenver.edu/DSigDB/DSigDBv1.0/download.html
    - Yoo, Minjae, Jimin Shin, Jihye Kim, Karen A. Ryall, Kyubum Lee, Sunwon Lee, Minji Jeon, Jaewoo Kang, and Aik Choon Tan. “DSigDB: Drug Signatures Database for Gene Set Analysis: Fig. 1.” Bioinformatics 31, no. 18 (September 15, 2015): 3069–71. https://doi.org/10.1093/bioinformatics/btv313.

- `GDA` - 基因组学与药物综合分析. The Genomics and Drugs integrated Analysis portal (GDA) is 是一个基于web的工具集合 NCI60和CCLE基因突变和表达谱的药物敏感性数据。 基因到药物和反向分析. http://gda.unimore.it/

- `OncoKB` - 癌基因数据库，不同水平的证据，完全可下载. http://oncokb.org
    - Chakravarty, Debyani, Jianjiong Gao, Sarah M. Phillips, Ritika Kundra, Hongxin Zhang, Jiaojiao Wang, Julia E. Rudolph, et al. “OncoKB: A Precision Oncology Knowledge Base.” JCO Precision Oncology 2017 (July 2017).

- `CancerRxGene` - Drug-gene 目标. 很多药物敏感信息. http://www.cancerrxgene.org/
    - Yang, Wanjuan, Jorge Soares, Patricia Greninger, Elena J. Edelman, Howard Lightfoot, Simon Forbes, Nidhi Bindal, et al. “Genomics of Drug Sensitivity in Cancer (GDSC): A Resource for Therapeutic Biomarker Discovery in Cancer Cells.” Nucleic Acids Research 41, no. Database issue (January 2013): D955-961. https://doi.org/10.1093/nar/gks1111.

- `CTRP` - The Cancer Therapeutics Response Portal (CTRP) 加速发现与病人相匹配的癌症疗法的目标。. https://portals.broadinstitute.org/ctrp/

- 药物联合筛选，协同作用。统计分析用于大型药物筛选，108种药物，40个细胞株。独立模型描述。Bliss-based 线性模型来评估单个药物的生存能力. Web: http://www.cmtlab.org:3000/combo_app.html. Code 分析: https://github.com/arnaudmgh/synergy-screen. 原始数据: https://raw.githubusercontent.com/arnaudmgh/synergy-screen/master/data/rawscreen.csv
    - Amzallag, Arnaud, Sridhar Ramaswamy, and Cyril H. Benes. “Statistical Assessment and Visualization of Synergies for Large-Scale Sparse Drug Combination Datasets.” BMC Bioinformatics 20, no. 1 (December 2019). https://doi.org/10.1186/s12859-019-2642-7.



## 数据

- `UCSCXenaTools` - 一个从UCSC Xena数据中心下载和研究数据的R包. CRAN: https://cran.r-project.org/web/packages/UCSCXenaTools/, GitHub: https://github.com/ShixiangWang/UCSCXenaTools, https://shixiangwang.github.io/UCSCXenaTools/

- 乳腺癌全基因组资源，一个门户网站的蛋白质组学，转录组学，基因组学和代谢组学的乳腺癌. 下载 http://www.breastcancerlandscape.org/
    - Consortia Oslo Breast Cancer Research Consortium (OSBREAC), Henrik J. Johansson, Fabio Socciarelli, Nathaniel M. Vacanti, Mads H. Haugen, Yafeng Zhu, Ioannis Siavelis, et al. “Breast Cancer Quantitative Proteome and Proteogenomic Landscape.” Nature Communications 10, no. 1 (December 2019): 1600. https://doi.org/10.1038/s41467-019-09018-y. - 乳腺癌亚型的蛋白质基因组学。用LS-MS/MS测定~10K蛋白。五个PAM50亚型各9个样本。蛋白质表达部分地概括了PAM50亚型，它们自己的一致聚类。与mRNA相关性高，与CNV相关性低。290种经fda批准的药物靶点蛋白的相关性。在线工具, http://www.breastcancerlandscape.org/, 附录有蛋白质表达数据矩阵,https://www.nature.com/articles/s41467-019-09018-y#Sec15

- `Refine.bio` 将公开可用的pb级生物数据协调为癌症研究人员和AI/ML科学家的现成数据集. https://www.refine.bio/. Documentation, http://docs.refine.bio/en/latest/, GitHub, https://github.com/AlexsLemonade/refinebio.

- Zehir, Ahmet, Ryma Benayed, Ronak H Shah, Aijazuddin Syed, Sumit Middha, Hyunjae R Kim, Preethi Srinivasan, et al. “Mutational Landscape of Metastatic Cancer Revealed from Prospective Clinical Sequencing of 10,000 Patients.” Nature Medicine 23, no. 6 (May 8, 2017): 703–13. https://doi.org/10.1038/nm.4333. - MSK-IMPACT study. 多种癌症样本中341-410基因的深度测序。突变，拷贝数改变，融合。 数据 http://www.cbioportal.org/study?id=msk_impact_2017#summary, downloadable, 包括临床数据.

- Gendoo, Deena M.A., Michael Zon, Vandana Sandhu, Venkata Manem, Natchar Ratanasirigulchai, Gregory M. Chen, Levi Waldron, and Benjamin Haibe-Kains. “MetaGxData: Clinically Annotated Breast, Ovarian and Pancreatic Cancer Datasets and Their Use in Generating a Multi-Cancer Gene Signature,” November 12, 2018. https://doi.org/10.1101/052910. - 包含乳腺癌和卵巢癌数据、矩阵数据和RNA-seq基因表达和临床注释数据在MetaGxData包。用于对所有基因进行全基因组生存分析的脚本。 https://github.com/bhklab/MetaGxData 

- `DepMap` - 大规模RNAi筛选20种癌症501个细胞系的癌症易感基因，shRNA沉默约17000个基因。数据未知: https://depmap.org/portal/download/
    - Tsherniak, Aviad, Francisca Vazquez, Phil G. Montgomery, Barbara A. Weir, Gregory Kryukov, Glenn S. Cowley, Stanley Gill, et al. “Defining a Cancer Dependency Map.” Cell 170, no. 3 (July 2017): 564-576.e16. https://doi.org/10.1016/j.cell.2017.06.010. [Supplemental tables](https://www.sciencedirect.com/science/article/pii/S0092867417306517?via%3Dihub#app2), `DepMap_TableS3_DependencyCorrelation.csv` - Table S3. 基因依赖关系，对增殖/存活至关重要的一对基因。Columns: Gene symbol 1, Gene symbol 2, correlation (r), z_score. [Source](https://ars.els-cdn.com/content/image/1-s2.0-S0092867417306517-mmc3.csv)

- `CCLE2 data` - 。数据描述:RNA剪接，DNA甲基化，组蛋白修饰，miRNA表达，1072个细胞的RPPA。: https://portals.broadinstitute.org/ccle/data, https://depmap.org/portal/download/
    - Ghandi, Mahmoud, Franklin W. Huang, Judit Jané-Valbuena, Gregory V. Kryukov, Christopher C. Lo, E. Robert McDonald, Jordi Barretina, et al. “Next-Generation Characterization of the Cancer Cell Line Encyclopedia.” Nature, May 8, 2019. https://doi.org/10.1038/s41586-019-1186-3.


### TCGA PanCancer

- ATAC-seq数据来自TCGA(23种癌症类型)的410个肿瘤样本。与基因表达的相关性预测远端相互作用。按癌症类型划分的18个集群。数据:  https://gdc.cancer.gov/about-data/publications/ATACseq-AWG 
    - Corces, M. Ryan, Jeffrey M. Granja, Shadi Shams, Bryan H. Louie, Jose A. Seoane, Wanding Zhou, Tiago C. Silva, et al. “The Chromatin Accessibility Landscape of Primary Human Cancers.” Edited by Rehan Akbani, Christopher C. Benz, Evan A. Boyle, Bradley M. Broom, Andrew D. Cherniack, Brian Craft, John A. Demchok, et al. Science 362, no. 6413 (2018). https://doi.org/10.1126/science.aav1898.

- 来自PanCancer出版物的论文和补充数据。临床注释、RNA-seq计数、RPPA、甲基化、miRNA、拷贝数、.maf格式突变. https://gdc.cancer.gov/about-data/publications/pancanatlas
    - Ding, Li, Matthew H. Bailey, Eduard Porta-Pardo, Vesteinn Thorsson, Antonio Colaprico, Denis Bertrand, David L. Gibbs, et al. “Perspective on Oncogenic Processes at the End of the Beginning of Cancer Genomics.” Cell 173, no. 2 (April 5, 2018): 305-320.e10. https://doi.org/10.1016/j.cell.2018.03.033. - An overview of PanCancer Atlas.

- 泛癌分析由TCGA联合发表，全部论文. https://www.cell.com/pb-assets/consortium/pancanceratlas/pancani3/index.html


- `PCAGW` - PCAWG研究是一项国际合作，旨在确定来自国际癌症基因组联盟的2800多个癌症全基因组的常见突变模式。该项目产生了大量类型多样的数据，包括简单的体细胞突变(SNVs、MNVs和小indel)、大规模的体细胞结构变异、拷贝数改变、种系变异、RNA表达谱、基因融合、表型注释等。PCAWG数据已被导入、处理并提供给以下四种主要的在线资源供全球癌症研究人员下载和探索。 http://docs.icgc.org/pcawg/
    - Goldman, Mary, Junjun Zhang, Nuno A. Fonseca, Qian Xiang, Brian Craft, Elena Piñeiro, Brian O’Connor, et al. “Online Resources for PCAWG Data Exploration, Visualization, and Discovery.” BioRxiv, October 18, 2017. https://doi.org/10.1101/163907. https://www.biorxiv.org/content/early/2017/10/18/163907


## 甲基化

- `MEXPRESS` - 基因中心甲基化与临床参数的关系. http://mexpress.be/

- `Pancan-meQTL` 23种TCGA癌症类型的meQTLs数据库 Cis-, trans-meQTLs, pancancer-meQTLs, survival meQTLs. SNP-, gene-, CpG-centric 对于不同癌症类型. 图例, KM-survival图. Download. http://bioinfo.life.hust.edu.cn/Pancan-meQTL/
    - Gong, Jing, Hao Wan, Shufang Mei, Hang Ruan, Zhao Zhang, Chunjie Liu, An-Yuan Guo, Lixia Diao, Xiaoping Miao, and Leng Han. “Pancan-MeQTL: A Database to Systematically Evaluate the Effects of Genetic Variants on Methylation in Human Cancer.” Nucleic Acids Research, September 7, 2018. https://doi.org/10.1093/nar/gky814.


