# Jupyter Notebook 使用指南

## 文件说明

本项目包含两个Jupyter Notebook，它们是原始R脚本的完整转写版本，并添加了详细的中文注释。

### 1. QENIE主分析流程.ipynb

**对应原始文件：** R-script.R

**主要内容：**

#### 第一步：环境准备与库导入
- 加载WGCNA、reshape2、tidyverse包
- 说明每个包的用途和功能

#### 第二步：数据导入
- 导入分泌蛋白列表（Secreted_proteins_Uniprot.txt）
- 导入脂肪组织表达数据（adipose.txt）
- 导入肝脏组织表达数据（liver.txt）
- 详细说明数据来源和格式

#### 第三步：构建跨组织相关性和P值矩阵
- 使用bicorAndPvalue计算肝脏-脂肪基因对的相关性
- 说明bicor方法的优势
- 生成相关系数矩阵和P值矩阵

#### 第四步：计算Ssec显著性评分
- 计算每个肝脏基因的原始显著性评分
- 筛选分泌蛋白
- 归一化处理得到Ssec评分
- 保存结果到文件

#### 第五步：条件相关性矩阵用于通路富集分析
- 提取特定基因（如Notum）的相关性数据
- 生成正相关基因列表（Top 500）
- 生成负相关基因列表（Top 500）
- 生成绝对值相关基因列表（Top 500）

**代码单元格数量：** 约15个

**预计运行时间：** 10-20分钟（取决于计算资源）

---

### 2. QQ图分析.ipynb

**对应原始文件：** QQ-Plot_R-script.R

**主要内容：**

#### 第一步：环境准备与数据导入
- 加载WGCNA包
- 导入分泌蛋白列表
- 导入表达数据

#### 第二步：计算跨组织相关性P值
- 计算P值矩阵
- 计算Ssec评分

#### 第三步：分离分泌蛋白和非分泌蛋白的评分
- 将基因分为两组
- 比较统计特征
- 进行t检验

#### 第四步：定义增强版QQ图绘制函数
- 定义qqplotAnnot函数
- 详细说明参数和功能

#### 第五步：生成并解读QQ图
- 绘制QQ图
- 进行分位数富集分析
- 进行Kolmogorov-Smirnov检验
- 进行Wilcoxon秩和检验

#### 补充可视化
- 绘制重叠直方图
- 绘制箱线图

**代码单元格数量：** 约12个

**预计运行时间：** 5-10分钟

---

## 使用步骤

### 前置要求

1. **安装Jupyter**
```bash
pip install jupyter
```

2. **安装R和必要的包**
```r
install.packages(c("WGCNA", "reshape2", "tidyverse"))
```

3. **安装R kernel for Jupyter**
```r
install.packages('IRkernel')
IRkernel::installspec()
```

### 运行Notebook

1. **启动Jupyter**
```bash
cd /path/to/QENIE
jupyter notebook
```

2. **在浏览器中打开**
   - 会自动打开浏览器，显示文件列表
   - 点击要运行的notebook

3. **运行所有单元格**
   - 方式1：点击菜单 Cell → Run All
   - 方式2：逐个单元格运行（Shift + Enter）

4. **查看结果**
   - 每个单元格执行后会显示输出
   - 图形会直接显示在notebook中
   - 数据文件会保存到当前目录

### 注意事项

1. **数据文件位置**
   - 确保数据文件在正确的位置
   - 默认在notebook同一目录下

2. **内存要求**
   - 建议至少8GB RAM
   - 计算相关性矩阵需要较多内存

3. **运行时间**
   - 第三步（计算相关性）可能需要5-15分钟
   - 请耐心等待，不要中断

4. **修改分析参数**
   - 可以修改候选基因（如将Notum改为其他基因）
   - 可以修改Top基因数量（如将500改为1000）
   - 可以修改组织数据（如分析其他组织对）

## 注释风格说明

每个notebook的注释包含三个层次：

### 1. Markdown单元格（概述性说明）
- 使用Markdown格式
- 提供章节标题和整体介绍
- 解释生物学背景和计算原理
- 包含示例和图表

### 2. 代码注释（行内注释）
```r
# 这是行注释，解释单行或几行代码的功能
data <- read.delim('file.txt', check.names=F)  # check.names=F: 保持原始列名
```

### 3. Print输出（运行时说明）
```r
print("正在执行某个步骤...")
print(paste("统计结果：", result))
```

## 学习建议

### 对于生物学背景的读者

1. **关注生物学部分**
   - 重点阅读Markdown单元格中的生物学背景
   - 理解内分泌系统和基因表达的关系
   - 关注结果解读部分

2. **理解数据结构**
   - 了解基因表达矩阵的含义
   - 理解为什么需要筛选分泌蛋白
   - 掌握Ssec评分的生物学意义

3. **逐步运行**
   - 一个单元格一个单元格运行
   - 查看每步的输出结果
   - 理解数据如何被转换和分析

### 对于计算机背景的读者

1. **关注算法部分**
   - 理解bicor相关性的计算方法
   - 学习R语言的数据处理技巧
   - 掌握矩阵操作和统计方法

2. **学习R语法**
   - tidyverse的管道操作（%>%）
   - dplyr的数据处理函数
   - ggplot2的可视化方法

3. **优化和扩展**
   - 考虑如何提高计算效率
   - 尝试并行计算
   - 开发批量分析脚本

### 对于两者兼具的读者

- 完整理解整个分析流程
- 能够独立进行新的组织对分析
- 可以修改和优化方法
- 能够解释结果的生物学和统计学意义

## 常见问题

### Q1: notebook运行出错怎么办？

**A:** 检查以下几点：
1. R包是否正确安装
2. R kernel是否正确配置
3. 数据文件路径是否正确
4. 内存是否足够

### Q2: 如何保存结果？

**A:** 结果文件会自动保存到notebook所在目录：
- `Liver X Adipose ranked by sig score`
- `Positive [Gene] Liver X Adipose Pathways Enrichment File`
- `Negative [Gene] Liver X Adipose Pathways Enrichment File`
- `Absolute [Gene] Liver X Adipose Pathways Enrichment File`

### Q3: 如何修改分析其他基因？

**A:** 在第五步中，修改这一行：
```r
target_gene = 'Notum'  # 改为你感兴趣的基因名
```

### Q4: 如何分析其他组织对？

**A:** 修改第二步的数据导入：
```r
Adipose <- read.delim('your_tissue1.txt', check.names=F)
Liver <- read.delim('your_tissue2.txt', check.names=F)
```

### Q5: 图形不显示怎么办？

**A:** 确保：
1. 在Jupyter中运行，不是在RStudio
2. 图形输出选项设置正确
3. 使用`options(repr.plot.width=10, repr.plot.height=10)`调整图形大小

## 进阶使用

### 批量分析多个基因

```r
# 在主分析notebook的第五步后添加：
top_genes <- head(Ssec$Gene_symbol, 10)  # 取Top 10基因

for(gene in top_genes) {
  # 提取该基因的相关性
  gene_data <- filter(bicor.data, Gene_symbol_1 == gene) %>%
    select(-Gene_symbol_1) %>%
    rename(Gene_symbol = Gene_symbol_2)
  
  # 生成三种基因列表
  upreg <- gene_data %>% arrange(desc(bicor)) %>% head(500)
  downreg <- gene_data %>% arrange(bicor) %>% head(500)
  totalreg <- gene_data %>% arrange(desc(abs(bicor))) %>% head(500)
  
  # 保存文件
  write.table(upreg, file=paste0("Positive_", gene, "_pathways.txt"), 
              col.names=F, sep='\t', quote=F)
  write.table(downreg, file=paste0("Negative_", gene, "_pathways.txt"), 
              col.names=F, sep='\t', quote=F)
  write.table(totalreg, file=paste0("Absolute_", gene, "_pathways.txt"), 
              col.names=F, sep='\t', quote=F)
}
```

### 自动化QQ图比较

```r
# 为多个组织对生成QQ图
tissue_pairs <- list(
  list(name="Liver-Adipose", source=Liver, target=Adipose),
  list(name="Intestine-Muscle", source=Intestine, target=Muscle)
)

for(pair in tissue_pairs) {
  # 计算相关性
  corr_p <- bicorAndPvalue(pair$source, pair$target)$p
  scores <- rowSums(-log(corr_p))
  
  # 分组
  sec <- scores[names(scores) %in% Secreted_proteins$`Gene names  (primary )`]
  non_sec <- scores[!(names(scores) %in% Secreted_proteins$`Gene names  (primary )`)]
  
  # 绘制QQ图
  qqplotAnnot(non_sec, sec, main=paste("QQ-plot,", pair$name))
}
```

## 技术支持

如果在使用过程中遇到问题，可以：

1. **查看原始README**
   - README.md（英文）
   - README_CN.md（中文）

2. **检查代码注释**
   - 每个步骤都有详细说明

3. **参考原始R脚本**
   - R-script.R
   - QQ-Plot_R-script.R

4. **联系作者**
   - Email: mseldin3@gmail.com

## 版权和引用

如果您在研究中使用了这些notebook，请引用：

1. **QENIE方法**：引用相关发表的论文
2. **WGCNA包**：Langfelder & Horvath (2008)
3. **数据来源**：相应的GEO数据集
4. **QQ图脚本**：致谢Simon Koplev和IIT Madras BIRDS Lab

## 贡献

欢迎对这些notebook提出改进建议：
- 添加更多注释
- 优化代码效率
- 增加新的可视化
- 修正错误或不清楚的地方

---

**最后更新：** 2024-11
**版本：** 1.0
