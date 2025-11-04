# QENIE 项目中文文档

## 项目概述

QENIE (Quantitative Endocrine Network Interaction Estimation，定量内分泌网络互作估计) 是一个基于相关性分析的生物信息学方法，用于识别跨组织的内分泌互作关系。

## 新增内容

本项目已将原始的R脚本转换为Jupyter Notebook格式，并添加了详细的中文注释，涵盖生物学和计算机科学两个方面的知识。

### 📓 Jupyter Notebooks

1. **QENIE主分析流程.ipynb**
   - 对应原始文件：`R-script.R`
   - 内容：完整的QENIE分析流程
   - 包含5个主要步骤的详细说明和注释

2. **QQ图分析.ipynb**
   - 对应原始文件：`QQ-Plot_R-script.R`
   - 内容：QQ图生成和方法验证
   - 包含统计检验和补充可视化

### 📁 原始文件

- `R-script.R`: 原始R脚本（主分析）
- `QQ-Plot_R-script.R`: 原始R脚本（QQ图）
- 这些文件保持不变，供参考使用

## 分析流程详解

### 第一步：环境准备

```r
library(WGCNA)      # 用于bicor相关性计算
library(reshape2)   # 用于数据重塑
library(tidyverse)  # 用于数据处理
```

### 第二步：数据导入

**输入文件：**
- `Secreted_proteins_Uniprot.txt`: UniProt分泌蛋白列表
- `adipose.txt`: 脂肪组织表达数据（12,242基因 × 106品系）
- `liver.txt`: 肝脏组织表达数据（12,242基因 × 106品系）

**数据来源：**
- GEO数据库：GSE64770
- HMDP (Hybrid Mouse Diversity Panel) 小鼠群体

### 第三步：跨组织相关性分析

使用bicor（双权重中值相关性）计算肝脏和脂肪组织间的基因表达相关性：

```r
liv.adip = bicorAndPvalue(Liver, Adipose, use='pairwise.complete.obs')
```

**输出：**
- 相关系数矩阵：`liv.adip$bicor`
- P值矩阵：`liv.adip$p`

### 第四步：Ssec评分计算

计算每个基因的显著性评分（Significance Score, Ssec）：

```r
scores = rowSums(-log(liv.adip$p))
Ssec = score / length(colnames(Adipose))  # 归一化
```

**筛选条件：**
- 只保留分泌蛋白
- 按Ssec降序排列

**生物学意义：**
- Ssec值高：该基因与目标组织基因表达高度相关
- 可能是重要的内分泌调控因子

### 第五步：通路富集分析准备

为特定候选基因（如Notum）生成三种基因列表：

1. **正相关基因列表**（Top 500）
   - 候选蛋白可能上调的通路
   
2. **负相关基因列表**（Top 500）
   - 候选蛋白可能下调的通路
   
3. **绝对值相关基因列表**（Top 500）
   - 候选蛋白影响最大的通路

## QQ图分析

### 目的

验证QENIE方法能够有效区分分泌蛋白和非分泌蛋白。

### 原理

QQ图比较分泌蛋白和非分泌蛋白的Ssec评分分布：
- **X轴**：非分泌蛋白的分位数
- **Y轴**：分泌蛋白的分位数
- **红色对角线**：两组分布相同的参考线

### 期望结果

如果方法有效，应该看到：
1. 大部分点在对角线上方（分泌蛋白评分更高）
2. 高分位数（90%、99%）显著偏离对角线
3. 表明高评分基因富集了分泌蛋白

## 输出文件

### 主分析输出

1. **Liver X Adipose ranked by sig score**
   - 所有分泌蛋白及其Ssec评分
   - 按显著性降序排列
   - 用于识别候选内分泌因子

2. **Positive [Gene] Liver X Adipose Pathways Enrichment File**
   - 正相关基因列表
   - 用于通路富集分析

3. **Negative [Gene] Liver X Adipose Pathways Enrichment File**
   - 负相关基因列表
   - 用于通路富集分析

4. **Absolute [Gene] Liver X Adipose Pathways Enrichment File**
   - 绝对值相关基因列表
   - 用于通路富集分析

## 使用指南

### 安装依赖

在R中安装所需包：

```r
install.packages(c("WGCNA", "reshape2", "tidyverse"))
```

### 运行分析

#### 方法1：使用Jupyter Notebook（推荐）

1. 安装Jupyter和R kernel：
```bash
pip install jupyter
R -e "install.packages('IRkernel'); IRkernel::installspec()"
```

2. 启动Jupyter：
```bash
jupyter notebook
```

3. 打开并运行notebook：
   - `QENIE主分析流程.ipynb`
   - `QQ图分析.ipynb`

#### 方法2：使用原始R脚本

```r
source("R-script.R")
source("QQ-Plot_R-script.R")
```

### 分析其他组织

修改输入数据即可：

```r
# 例如：肠道→肌肉回路
Intestine <- read.delim('Intestine.txt', check.names=F)
Muscle <- read.delim('Muscle.txt', check.names=F)

int.mus = bicorAndPvalue(Intestine, Muscle, use='pairwise.complete.obs')
# 后续步骤相同...
```

## 关键概念解释

### 生物学概念

1. **内分泌信号传递**
   - 源组织产生分泌蛋白
   - 通过血液循环到达目标组织
   - 调控目标组织的基因表达

2. **分泌蛋白**
   - 能够从细胞释放到细胞外
   - 包括激素、细胞因子、生长因子等
   - 是组织间通讯的关键分子

3. **HMDP（Hybrid Mouse Diversity Panel）**
   - 包含约100个重组近交系小鼠
   - 遗传多样性高
   - 适合基因型-表型关联研究

### 计算方法

1. **Bicor（双权重中值相关性）**
   - 比Pearson相关系数更稳健
   - 对异常值不敏感
   - 适合基因表达数据

2. **P值转换**
   - 使用-log(p)放大显著性差异
   - p=0.01 → -log(0.01)≈2
   - p=0.001 → -log(0.001)≈3

3. **归一化**
   - 除以目标组织基因数量
   - 确保不同研究间可比性

## 示例结果

### Notum基因

根据QENIE分析，Notum在肝脏→脂肪回路中排名第5：

**生物学背景：**
- Notum是一种分泌型脂肪酶
- 抑制Wnt信号通路
- 调控脂质代谢和胰岛素敏感性

**Ssec评分：** ~4.148

**通路富集分析：**
通过分析Notum相关的脂肪基因，可以识别其调控的代谢通路。

## 下游分析建议

### 1. 通路富集分析

使用生成的基因列表进行富集分析：

**在线工具：**
- DAVID (david.ncifcrf.gov)
- Enrichr (enrichr.org)
- Metascape (metascape.org)

**R包：**
```r
library(clusterProfiler)
library(enrichR)
library(ReactomePA)
```

### 2. 组织特异性验证

使用BioGPS检查基因表达模式：
- 网址：biogps.org
- 确认候选基因在源组织高表达
- 确认候选基因在目标组织低表达或不表达

### 3. 文献验证

在PubMed搜索候选基因的功能研究：
- 验证已知的内分泌功能
- 寻找机制研究
- 查找相关疾病关联

### 4. 实验验证

**体外实验：**
- 用重组蛋白处理目标组织细胞
- 检测靶基因表达变化
- Western blot确认蛋白水平

**体内实验：**
- 基因敲除/过表达小鼠模型
- ELISA测量血清蛋白水平
- 表型分析（代谢、体重等）

## 方法优势

1. **无偏好性**
   - 不依赖先验知识
   - 能发现新的内分泌因子

2. **统计稳健**
   - 使用bicor相关性
   - 对异常值不敏感

3. **可扩展性**
   - 可应用于任何组织对
   - 易于批量分析

4. **整合性**
   - 整合表达数据和蛋白注释
   - 多层次筛选候选基因

## 局限性

1. **相关性≠因果性**
   - 需要实验验证
   - 可能存在间接调控

2. **数据依赖**
   - 结果质量取决于输入数据
   - 需要高质量的表达数据

3. **组织特异性假设**
   - 假设分泌蛋白能到达目标组织
   - 实际情况可能更复杂

4. **功能假设**
   - 假设基因表达相关性反映蛋白功能
   - 转录后调控可能改变这种关系

## 引用

### 数据来源

**UniProt数据库：**
> UniProt Consortium. (2017). UniProt: the universal protein knowledgebase. Nucleic Acids Research, 45(D1), D158-D169.

**HMDP表达数据（GSE64770）：**
> Parks, B. W., et al. (2013). Genetic control of obesity and gut microbiota composition in response to high-fat, high-sucrose diet in mice. Cell Metabolism, 17(1), 141-152.

**BioGPS数据（GSE1133）：**
> McClurg, P., et al. (2007). Genomewide association analysis in diverse inbred mice: power and population structure. Genetics, 176(1), 675-683.

### 方法

**WGCNA包：**
> Langfelder, P., & Horvath, S. (2008). WGCNA: an R package for weighted correlation network analysis. BMC Bioinformatics, 9(1), 559.

### 致谢

**QQ图脚本：**
- Simon Koplev：设计和提供脚本

**脚本验证和修正：**
- Arjun Sarathi (IIT Madras BIRDS Lab)
- Manikandan Narayanan (IIT Madras BIRDS Lab)

## 联系方式

如有问题或建议，请联系：
- Email: mseldin3@gmail.com

## 其他GEO数据集

本研究中使用的其他数据集：

- **chow aorta**: GSE38120
- **chow heart**: GSE77263
- **chow liver**: GSE16780
- **chow adipose**: GSE42890
- **high fat/high sucrose hypothalamus**: GSE79551

## 常见问题（FAQ）

### Q1: 为什么选择bicor而不是Pearson相关？

**A:** Bicor（双权重中值相关性）对异常值更稳健。基因表达数据常含有异常值，使用bicor可以获得更可靠的结果。

### Q2: Ssec评分的阈值是多少？

**A:** 没有固定阈值。建议：
1. 查看评分分布，选择Top N个基因
2. 使用QQ图评估富集效果
3. 结合组织特异性和文献验证

### Q3: 可以用于人类数据吗？

**A:** 可以，但需要：
1. 准备人类的表达数据
2. 使用人类的分泌蛋白列表（UniProt）
3. 注意基因命名的差异

### Q4: 计算需要多长时间？

**A:** 取决于数据规模：
- 本例（12,242基因）：5-15分钟
- 内存需求：建议至少8GB RAM
- 使用多核可以加速计算

### Q5: 如何批量分析多个组织对？

**A:** 可以写循环脚本：

```r
tissues = list(
  list(source="liver", target="adipose"),
  list(source="intestine", target="muscle"),
  # 添加更多组织对...
)

for(tissue_pair in tissues) {
  # 读取数据
  source_data = read.delim(paste0(tissue_pair$source, ".txt"), check.names=F)
  target_data = read.delim(paste0(tissue_pair$target, ".txt"), check.names=F)
  
  # 运行分析
  # ... (参照主分析流程)
}
```

## 更新日志

### 2024-11 - Jupyter Notebook版本

- ✅ 创建`QENIE主分析流程.ipynb`
  - 完整的中文注释
  - 分步骤详细说明
  - 生物学和计算机科学双重解释

- ✅ 创建`QQ图分析.ipynb`
  - QQ图生成和解读
  - 统计检验
  - 补充可视化（直方图、箱线图）

- ✅ 创建中文文档`README_CN.md`
  - 完整的项目文档
  - 使用指南
  - 常见问题解答

### 原始版本

- R脚本：`R-script.R`
- QQ图脚本：`QQ-Plot_R-script.R`
- 英文文档：`README.md`
