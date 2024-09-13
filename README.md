# 时尚推荐系统项目 README 文件

## 概述
本项目开发了一个结合**内容基础**、**规则基础**和**协同过滤方法**的混合时尚推荐系统。主要目标是通过为用户提供个性化推荐来增强时尚应用程序的购物体验，特别是那些没有具体购买意图的浏览者。

## 系统架构
- **特征提取**：使用 DINO v2 提取图像特征，使用 GloVe 提取文本特征。
- **候选生成**：实现了基于内容、基于规则和协同过滤方法的组合，以生成潜在推荐池。
- **集成推荐**：使用集成模块结合不同方法的推荐，以产生最终推荐列表。

## 主要特点
- **混合推荐方法**：结合多种方法提供强大且多样的推荐。
- **可扩展性和性能**：有效处理大量项目和用户互动数据，使用高级模型如 DINO v2 和协同过滤技术。

## 设置和使用
- 确保安装了 Python 和必要的库（transformers, faiss, gensim）。
- 加载预训练模型并使用初始数据配置系统。
- 运行系统以为现有用户生成和存储推荐；定期更新推荐。

## 更新
- 系统支持使用新数据进行更新，以反映用户偏好和物品可用性的变化。
- 通过适用于冷启动场景的特定方法处理新用户的推荐。

## 方法详解

### 1. 基于内容的过滤（Content-Based Filtering）
基于内容的过滤方法通过比较产品的特性和用户的偏好或之前的行为来推荐商品。在这个系统中，采用了两种类型的基于内容的推荐：

#### 1.1 基于图像的推荐
- **特征提取**：使用 DINO v2 模型，系统从每个商品中提取图像特征。DINO v2 使用视觉变换器（Vision Transformer）架构处理图像，提取出代表每个图像的一系列嵌入式补丁的强大特征。
- **相似度计算**：对于数据库中的每个项目，使用欧几里得距离的倒数计算它们特征向量之间的相似度。推荐与用户互动过的商品最相似的特征向量的商品。

#### 1.2 基于文本的推荐
- **特征提取**：文本特征使用 GloVe 模型提取，该模型为单词提供向量表示。将每个项目的文本属性连接起来形成“文档”，这些向量的均值被用作项目的文本特征向量。
- **相似度计算**：与基于图像的方法类似，基于文本特征计算项目之间的相似度，使用欧几里得距离的倒数。推荐在文本上与用户显示出兴趣的商品相似的商品。

### 2. 基于规则的方法（Rule-Based Methods）
基于规则的方法使用特定的业务规则或逻辑来过滤和推荐商品。这里使用了几种基于规则的方法：

#### 2.1 按年龄组的流行度
- **分组**：用户被分成年龄组（例如，每5年一个组）。
- **流行度计算**：根据过去的购买次数计算每个年龄组内的商品流行度。
- **推荐**：推荐用户年龄组内流行的商品。

#### 2.2 按地区的流行度
- **分组**：根据用户的邮政编码将用户分组。
- **流行度计算**：与年龄组方法相似，计算每个地区的商品流行度。
- **推荐**：推荐在用户所在地区流行的商品。

#### 2.3 联合购买的商品
- **共购分析**：此方法检查经常一起购买的商品的模式。一个捕捉这些共购模式的矩阵有助于识别哪些商品经常一起购买。
- **推荐**：推荐与用户之前购买的商品一起购买的商品。

### 3. 协同过滤（Collaborative Filtering）
协同过滤通过收集许多用户的偏好或口味信息来预测用户的兴趣。使用以下类型：

#### 3.1 矩阵分解
- **模型**：使用交替最小二乘法（ALS）算法分解用户-商品互动矩阵，得到用户和商品的低维潜在因子。
- **推荐**：根据用户和商品向量的点积预测评分最高的商品。

#### 3.2 Item2Vec
- **模型训练**：类似于 Word2Vec，此模型将商品视为句子中的词，根据用户购买序列学习商品之间的上下文关系。
- **推荐**：通过计算用户配置文件向量与商品向量之间的余弦相似度，推荐与用户购买的商品上下文相似的商品。

### 4. 集成方法（Ensemble Methods）
集成模块聚合来自各种基于内容、基于规则和协同过滤方法的推荐：

- **分数计算**：每个由任何方法推荐的商品根据其在该方法中的排名获得分数。
- **最终推荐**：根据其分数across所有方法对商品进行排名，展示给用户的是排名最高的商品。

这些方法共同利用用户和商品数据的不同维度，提供个性化和准确的推荐。

### 公式

平均精度 (AP) 定义为：
$$
AP = \frac{1}{K} \sum_{k=1}^{K} P(k) \cdot \text{rel}(k)
$$
其中 \( K \) 是推荐项目的数量，\( P(k) \) 是第 \( k \) 位的精度，而 \( \text{rel}(k) \) 是一个指示函数，如果第 \( k \) 位的项目相关，则为 1，否则为 0。然后，计算所有用户的 AP 平均值得到平均平均精度 (MAP)：
$$
MAP = \frac{1}{U} \sum_{u=1}^{U} \text{AP}_u
$$
其中 \( U \) 是用户总数。精度和召回率直接衡量推荐的相关性，而 MAP 考虑了推荐项目的排名，提供了对模型性能的全面评估。

### 表格

所有现有用户的各种方法和集成：

| 方法                    | 精度@12           | 召回@12           | MAP@12            |
|------------------------|------------------|------------------|-------------------|
| 图像内容基线            | 0.0010 ± 0.000   | 0.0043 ± 0.001   | 0.0016 ± 0.000    |
| 文本内容基线            | 0.0026 ± 0.000   | 0.0108 ± 0.000   | 0.0044 ± 0.000    |
| 产品                    | 0.0088 ± 0.001   | 0.0401 ± 0.001   | 0.0127 ± 0.000    |
| 邮政                    | 0.0102 ± 0.001   | 0.0365 ± 0.003   | 0.0110 ± 0.001    |
| 年龄组                  | 0.0029 ± 0.000   | 0.0106 ± 0.002   | 0.0035 ± 0.001    |
| 同时购买                 | 0.0014 ± 0.000   | 0.0052 ± 0.000   | 0.0013 ± 0.000    |
| 用户协同过滤             | 0.0036 ± 0.000   | 0.0133 ± 0.001   | 0.0048 ± 0.000    |
| Item2Vec (分类)         | 0.0099 ± 0.001   | 0.0419 ± 0.003   | 0.0203 ± 0.001    |
| Item2Vec (相似度)       | 0.0090 ± 0.002   | 0.0407 ± 0.005   | 0.0180 ± 0.002    |
| 集成 (表 1)             | 0.0102 ± 0.001   | 0.0448 ± 0.003   | 0.0202 ± 0.001    |
| 集成 (表 2)             | **0.0112 ± 0.001** | **0.0502 ± 0.003** | **0.0213 ± 0.001**|
| 集成 (表 3)             | 0.0100 ± 0.001   | 0.0440 ± 0.002   | 0.0176 ± 0.001    |

