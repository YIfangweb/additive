# 食品添加剂数据开源项目

## 项目简介

本项目收集、整理并开源了网络公开的食品添加剂数据，旨在为研究人员、开发者以及对食品安全感兴趣的公众提供可靠的食品添加剂信息资源。数据经过去重和分类处理，适用于食品安全应用、营养分析以及健康建议系统开发等多种场景。

## 数据来源

本项目的数据来源于权威机构和专业网站：

1. **WHO系列数据**：来源于世界卫生组织(World Health Organization)官方发布的食品添加剂数据。这些数据包含了全球范围内广泛使用的食品添加剂信息，具有高度的权威性和可靠性。
   - 文件：WTO.json, WTO235.json, WTO1871.json（文件名以WTO为前缀，数据来源于WHO）

2. **WEB系列数据**：来源于中国食品添加剂和配料网(http://www.31tjj.com/)。该网站是中国食品添加剂和配料行业的专业信息平台，提供了大量中文环境下使用的食品添加剂信息。
   - 文件：WEB.json, WEB_enriched.json, WEB_no_duplicates.json, WEB_edible.json

## 数据说明

所有数据文件均采用JSON格式存储，基本结构为：

```json
{
  "data": [
    {
      "name": "添加剂中文名称",
      "english_name": "English Name",
      "chemical_code": "化学代号",
      "chemical_name": "化学名称",
      "molecular_formula": "分子式",
      "description": "详细介绍",
      "usage_type": "食用或工业用途",
      "safe_dose": {
        "ADI": "每日允许摄入量",
        "notes": "安全注意事项"
      },
      "metabolism": "在人体内代谢原理",
      "common_uses": ["使用的食品类别1", "使用的食品类别2"],
      "regulatory_status": {
        "FDA": "FDA监管状态",
        "EU": "欧盟监管状态",
        "China": "中国监管状态"
      },
      "controversy": "争议事件（如有）"
    }
  ]
}
```

### 主要数据文件说明

- **WEB.json**：原始食品添加剂数据，包含约7100条记录，来源于中国食品添加剂和配料网
- **WEB_no_duplicates.json**：去重后的数据，包含约5639条记录
- **WEB_edible.json**：仅包含食用级添加剂的数据，排除了工业用途添加剂，约1969条记录
- **WTO.json**：世界卫生组织(WHO)发布的主要食品添加剂数据
- **WTO235.json**和**WTO1871.json**：世界卫生组织(WHO)发布的特定分类添加剂数据

## 数据处理流程

原始数据经过以下处理流程：

1. 数据收集：从权威来源获取原始数据
2. 数据清洗：修正格式错误、补充缺失字段、统一命名规范
3. 数据丰富：通过机器学习模型补充详细描述、代谢原理等信息
4. 数据去重：移除重复条目，保留信息最完整的版本
5. 分类筛选：将添加剂按用途分为食用级和工业级

## 应用场景

本数据集可应用于多种场景：

1. **食品安全评估**：评估食品中添加剂的安全性和合规性
2. **个性化健康建议**：根据用户健康状况（如孕妇、糖尿病患者等）提供针对性的添加剂摄入建议
3. **添加剂识别系统**：开发识别食品包装上添加剂成分的应用
4. **学术研究**：为添加剂安全性、代谢机制等研究提供数据支持
5. **公众科普**：制作食品添加剂科普内容，普及食品安全知识

## 使用方法

### 直接使用JSON数据

```python
import json

# 加载数据
with open('path/to/WEB_edible.json', 'r', encoding='utf-8') as f:
    data = json.load(f)

# 访问数据
additives = data['data']
for additive in additives:
    print(f"名称: {additive['name']}, 安全用量: {additive.get('safe_dose', {}).get('ADI', '未知')}")
```

### 导入到数据库

```python
import json
import sqlite3

# 连接数据库
conn = sqlite3.connect('additives.db')
cursor = conn.cursor()

# 创建表
cursor.execute('''
CREATE TABLE IF NOT EXISTS additives (
    id INTEGER PRIMARY KEY,
    name TEXT,
    english_name TEXT,
    chemical_code TEXT,
    description TEXT,
    usage_type TEXT,
    safe_dose TEXT,
    metabolism TEXT
)
''')

# 加载数据
with open('path/to/WEB_edible.json', 'r', encoding='utf-8') as f:
    data = json.load(f)

# 插入数据
for item in data['data']:
    safe_dose = json.dumps(item.get('safe_dose', {}), ensure_ascii=False)
    cursor.execute('''
    INSERT INTO additives (name, english_name, chemical_code, description, usage_type, safe_dose, metabolism)
    VALUES (?, ?, ?, ?, ?, ?, ?)
    ''', (
        item.get('name', ''),
        item.get('english_name', ''),
        item.get('chemical_code', ''),
        item.get('description', ''),
        item.get('usage_type', ''),
        safe_dose,
        item.get('metabolism', '')
    ))

conn.commit()
conn.close()
```

## 许可证

本项目数据采用[CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)许可证。您可以自由地：

- 共享 — 复制和重新发布该材料
- 改编 — 重混、转换材料并以材料为基础进行创作

但必须遵循以下条款：

- 署名 — 您必须给出适当的署名，提供指向本许可证的链接，同时标明是否作出了更改
- 非商业性使用 — 您不得将材料用于商业目的
- 相同方式共享 — 如果您重混、转换或在材料的基础上创作，您必须采用相同的许可证发布您的贡献

## 项目维护

本项目持续更新维护，欢迎贡献更多数据或提出改进建议。如有问题或建议，请通过Issues或Pull Requests方式提交。

## 致谢

感谢以下机构和网站提供的原始数据：
- 世界卫生组织(WHO)
- 中国食品添加剂和配料网(http://www.31tjj.com/)

同时感谢所有为本项目做出贡献的开发者和研究人员。 