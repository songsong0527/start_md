Math-Verify 项目简介和目标
Math-Verify 是一个面向数学题答案验证的Python库，能够解析数学表达式（支持 LaTeX 格式和普通文本）并对比答案正确性 。该工具在 MATH 等数学题型数据集上的评测中表现优异，能够实现“最高的准确率和最优得分” 。其核心功能包括：解析数学答案（自动抽取并解析 LaTeX 或纯数学表达式）、数值和符号等价性检查（支持数值近似比较和符号等价判断）、以及对集合、区间、矩阵、不等式等特殊表达式的智能比较 。简言之，Math-Verify 致力于提供一个稳健的数学答案验证系统，用于评估大模型在数学问题上的输出是否正确（例如通过parse() 将答案转换为符号表达式，通过 verify() 函数判定两者是否等价 ）。

自然语言指令  (Prompt)
请创建一个名为 Math-Verify 的 Python 项目，实现一个数学答案验证库。该项目应包括以下功能：
1. 表达式解析器：能够从输入的字符串中提取并解析数学表达式，支持 LaTeX 格式（如 \frac{1}{2} 、集合表达式等）和普通数学文本（如 x^2 + 3*x ）。解析结果应为 Sympy 符号对象或等价的可比较形式。
2. 等价性检查：实现函数（或脚本）能够比较两个数学表达式是否等价，包括数值比较和符号比较。应支持分数与小数近似比较、符号表达式化简判断，以及关系式/不等式的等价判断。
3. 特殊结构处理：对集合、区间、矩阵、百分比、单位符号等进行专门处理，比如 {1,2,3} 和 {3,2,1} 应视为等价，区间 [0,1] 等价于 [1,0] 等。
4. 接口设计：为每个功能模块（如解析、数值比较、符号比较、关系式比较等）设计独立的命令行接口或函数接口，支持终端调用测试。每个模块应定义清晰的输入输出格式。
5. 示例与评测脚本：提供示例代码和测试用例，演示如何用 parse() 和 verify() 函数进行输入解析和答案验证（如 verify(parse("${1,3}\\cup{2,4}$"), parse("${1,2,3,4}$")) 应返回 True ）。 以上功能需结合，构建一个完整的数学验证工具包。项目最终应包含解析、比较、整体验证等模块，并附带典型测试用例，形成一个可复现的验证流程。
6. 测试验证体系：提供全面的测试用例覆盖，13个功能节点测试：从数值格式标准化到复杂表达式等价性的完整测试流程；数据集回归测试：基于真实数学题数据集的验证；边界情况测试：超时处理、内存保护、错误恢复等异常场景；性能基准测试：解析速度和验证准确率的量化评估；关键文件实现要求。
7. 核心文件要求：项目必须包含一个完善的setup.py文件，该文件不仅要配置项目为可安装的包（支持pip install），还要声明完整的依赖列表（包括sympy==1.14.0、latex2sympy2_extended==1.10.1、mpmath==1.3.0、numpy、pandas、antlr4-python3-runtime、pytest等核心库），setup.py能够验证所有功能模块是否正常工作，同时需要提供math_verify/__init__.py作为统一的API入口，从parser和verifier模块导入parse和verify核心函数，导出LatexExtractionConfig、ExprExtractionConfig、StringExtractionConfig等配置类，并提供版本信息，使用户能够通过简单的"from math_verify import parse, verify"语句访问所有主要功能。

环境配置
核心依赖库版本
# 核心数学计算库
sympy==1.14.0                    # 符号数学计算引擎
latex2sympy2_extended==1.10.1    # LaTeX解析核心库
mpmath==1.3.0                    # 高精度数学计算

# 数据处理库
numpy==1.26.4                    # 数值计算基础
pandas==2.2.3                    # 数据处理和分析

# 解析支持库
antlr4-python3-runtime==4.13.2   # 语法解析运行时

# 测试框架
pytest==8.4.0                    # 单元测试框架
Math-Verify 项目架构
项目目录结构
Math-Verify-main/
├── src/
│   └── math_verify/
│       ├── __init__.py                 # 模块初始化，API导出
│       ├── parser.py                   # 数学表达式解析器
│       ├── grader.py                   # 数学表达式验证和评分
│       ├── metric.py                   # 度量计算
│       ├── utils.py                    # 工具函数集合
│       ├── tasks.py                    # 任务处理和配置
│       ├── few_shots.py                # Few-shot 示例数据
│       └── errors.py                   # 自定义异常类
├── examples/
│   ├── sample_answers.csv              # 示例答案数据
│   ├── model_outputs.csv               # 模型输出示例
│   └── few_shot_answers.csv            # Few-shot 答案示例
├── scripts/
│   └── publish.sh                      # 项目发布脚本
├── assets/
│   └── flow.svg                        # 项目工作流程图
├── latex2sympy2_extended_cloned/       # LaTeX 到 SymPy 转换的扩展库
├── evaluate_model.py                   # 模型评估脚本
├── evaluate_model_outputs.py           # 模型输出评估脚本
├── extract_answers.py                  # 答案提取脚本
├── pyproject.toml                      # Python 项目配置文件
├── README.md                           # 项目主要说明文档
├── Makefile                            # 构建和开发工具命令
├── .gitignore                          # Git 忽略文件配置
├── .gitmodules                         # Git 子模块配置
├── output.csv                          # 评估输出结果

API使用指南
核心 API
1. 模块导入
   from math_verify import parse, verify, LatexExtractionConfig, ExprExtractionConfig, StringExtractionConfig
2. parse() 函数 - 数学表达式解析
   **功能**：从文本中提取数学表达式并将其转换为 SymPy 对象。
   **参数**：
- pred (str): 待解析的文本字符串
- extraction_config: 提取配置列表，控制如何识别和提取表达式
- fallback_mode: 主要提取失败时的处理方式（"no_fallback" 或 "first_match"）
- extraction_mode: 提取模式（"first_match" 或 "any_match"）
- parsing_timeout (int): 解析超时时间，默认5秒
  **返回值**：解析得到的 SymPy 表达式列表
  **用法**：用于从 LLM 输出、学生答案或任意文本中提取数学表达式。
3. verify() 函数 - 表达式等价性验证
   **功能**：验证两个数学表达式是否在数学意义上等价。
   **参数**：
- gold: 标准答案（表达式或表达式列表）
- target: 待验证的表达式
- float_rounding (int): 浮点数舍入精度，默认6位
- numeric_precision (int): 数值计算精度，默认15位
- strict (bool): 是否使用严格模式
- allow_set_relation_comp (bool): 是否允许集合与关系式的比较
- timeout_seconds (int): 验证超时时间
  **返回值**：布尔值，表示是否等价
  **用法**：用于自动评分、答案验证等场景。
  配置类
1. LatexExtractionConfig
   **功能**：配置 LaTeX 表达式的提取规则
- try_extract_without_anchor: 是否在没有明确答案标识时也尝试提取
- boxed_match_priority: boxed 表达式的匹配优先级（0=最高，-1=禁用）
- normalization_config: LaTeX 标准化配置
2. ExprExtractionConfig
   **功能**：配置普通数学表达式的提取规则
- try_extract_without_anchor: 是否在没有明确答案标识时也尝试提取
3. StringExtractionConfig
   **功能**：配置字符串答案的提取规则（如选择题选项）
- strings: 要识别的字符串元组，默认 ("A", "B", "C", "D")
- try_extract_without_anchor: 是否在没有明确答案标识时也尝试提取
- lowercase: 是否转换为小写
  使用模式
  基础使用
# 简单解析和验证
gold = parse("$\\frac{1}{2}$")
answer = parse("$0.5$")
is_correct = verify(gold, answer)
- **数值类型**：整数、浮点数、分数、百分比、科学记数法
- **代数表达式**：多项式、有理函数、根式表达式
- **几何表达式**：坐标、向量、矩阵
- **集合与区间**：有限集合、无限集合、开闭区间
- **关系式**：等式、不等式、方程组
- **特殊符号**：希腊字母、数学常数、函数符号
  错误处理
  系统提供完善的错误处理机制：
- **超时保护**：防止复杂表达式解析时间过长
- **格式容错**：自动修复常见的 LaTeX 格式错误
- **回退机制**：多种提取策略确保最大兼容性
- **异常捕获**：优雅处理解析失败的情况

功能测试workflow拆解
节点 1：数值格式标准化 (Number Format Normalization)
功能描述：处理各种数值表示格式，统一标准化为可比较的数值形式。支持千位分隔符、小数点变体、货币符号、单位转换等复杂场景。
核心算法：
- 千位分隔符识别与移除
- 欧式/美式小数点转换
- 货币符号和单位过滤
- 科学记数法标准化
  输入格式：包含数值的字符串
  输出格式：标准化的数值表示
  详细测试样例：
  输入类型
  测试输入
  期望输出
  测试说明
  千位分隔符
  "7,425,000"
  7425000
  美式千位分隔符处理
  欧式小数点
  "1000,99"
  1000.99
  欧式逗号小数点转换
  货币符号
  "$1,000.99"
  1000.99
  移除美元符号
  单位处理
  "10 cm"
  10
  移除长度单位
  科学记数法
  "1.23e-4"
  0.000123
  科学记数法展开
  百分比
  "25.5%"
  0.255
  百分比转小数
  实际例程：
# 基础数值格式标准化
from math_verify import parse

# 测试千位分隔符
result1 = parse("The population is 7,425,000")
print(f"千位分隔符: {result1}")  # [7425000]

# 测试货币符号
result2 = parse("The cost is $1,000.99")  
print(f"货币符号: {result2}")  # [1000.99]

# 测试科学记数法
result3 = parse("The value is 1.23e-4")
print(f"科学记数法: {result3}")  # [0.000123]
边界情况测试：
- 负数处理："-1,234.56" → -1234.56
- 零值处理："0.000" → 0
- 超大数值："1.23e+20" → 1230000000000000000000
- 混合格式："$-1,234.56 USD" → -1234.56

节点 2：LaTeX环境识别与提取 (LaTeX Environment Detection)
功能描述：智能识别和提取不同 LaTeX 环境中的数学表达式，支持嵌套环境、转义字符和格式错误修复。
支持的 LaTeX 环境：
- 内联数学：$...$, \(...\)
- 块级数学：$$...$$, \[...\]
- 答案框：\boxed{...}
- 矩阵环境：pmatrix, bmatrix, array
- 对齐环境：align, equation
  输入格式：包含 LaTeX 标记的文本字符串
  输出格式：提取的数学表达式列表及环境类型
  详细测试样例：
  LaTeX 环境
  测试输入
  期望输出
  环境类型
  内联数学
  "Answer $ 9 $"
  ["9"]
  inline
  块级数学
  "Answer \\[ 9 \\]"
  ["9"]
  block
  答案框
  "Answer \\boxed{42}"
  ["42"]
  boxed
  分数
  "$ \\frac{1}{3} $"
  ["\\frac{1}{3}"]
  inline
  矩阵
  "$\\begin{pmatrix}1&0\\\\0&1\\end{pmatrix}$"
  ["Matrix([[1,0],[0,1]])"]
  matrix

实际例程：
from math_verify import parse
from math_verify import LatexExtractionConfig

# 配置 LaTeX 提取策略
latex_config = LatexExtractionConfig(
try_extract_without_anchor=True,
boxed_match_priority=0  # 最高优先级
)

# 测试不同 LaTeX 环境
test_cases = [
"The answer is $\\frac{1}{2}$",
"Solution: $$x^2 + 1 = 0$$",
"Final result: \\boxed{42}",
"Matrix: $\\begin{pmatrix} 1 & 0 \\\\ 0 & 1 \\end{pmatrix}$"
]

for text in test_cases:
result = parse(text, [latex_config])
print(f"输入: {text}")
print(f"输出: {result}\n")

嵌套环境处理：
# 复杂嵌套 LaTeX 表达式
nested_latex = """
Solution: The matrix equation is
$$\\begin{pmatrix}
\\frac{1}{2} & \\sqrt{3} \\\\
0 & \\boxed{\\frac{\\pi}{4}}
\\end{pmatrix}$$
"""
result = parse(nested_latex)
print(f"嵌套环境: {result}")

节点 3：LaTeX符号修复与规范化 (LaTeX Symbol Repair)
功能描述：自动修复常见的 LaTeX 格式错误，规范化数学符号表示，提高解析成功率。
修复策略：
- 缺失花括号：\frac12 → \frac{1}{2}
- 命令标准化：\cfrac → \frac
- 符号规范化：\pm → ±
- 空格处理：移除多余空格
- 转义字符：处理双重转义
  输入格式：可能包含格式错误的 LaTeX 表达式
  输出格式：修复后的标准 LaTeX 表达式
  详细测试样例：
  错误类型
  错误输入
  修复输出
  修复说明
  缺失花括号
  "\\frac13"
  "\\frac{1}{3}"
  自动添加分数花括号
  根号修复
  "\\sqrt3"
  "\\sqrt{3}"
  添加根号花括号
  命令标准化
  "\\cfrac{1}{3}"
  "\\frac{1}{3}"
  连分数转普通分数
  幂次修复
  "x^2y"
  "x^{2}y"
  多字符幂次加花括号
  下标修复
  "x_abc"
  "x_{abc}"
  多字符下标加花括号
  实际例程：
  from math_verify import parse

# 测试 LaTeX 符号修复
malformed_expressions = [
"\\frac13 + \\frac25",  # 缺失花括号的分数
"\\sqrt3 + \\sqrt[3]8", # 缺失花括号的根号
"x^2y + z^abc",         # 多字符幂次
"\\cfrac{1}{\\cfrac{2}{3}}", # 连分数
"\\sin x + \\cos y"     # 函数名后缺空格
]

for expr in malformed_expressions:
try:
result = parse(f"Answer: ${expr}$")
print(f"原始: {expr}")
print(f"修复: {result}")
print(f"状态: ✅ 修复成功\n")
except Exception as e:
print(f"原始: {expr}")
print(f"错误: {e}")
print(f"状态: ❌ 修复失败\n")

高级修复功能：
# 复杂表达式修复测试
complex_expr = """
\\frac{x^2+2xy+y^2}{x-y} = \\frac{(x+y)^2}{x-y}
"""
result = parse(f"Solution: ${complex_expr}$")
print(f"复杂表达式修复: {result}")

# 矩阵格式修复
matrix_expr = "\\begin{pmatrix}1&0\\\\0&1\\end{pmatrix}"
result = parse(f"Matrix: ${matrix_expr}$")  
print(f"矩阵修复: {result}")

节点 4：百分比格式转换 (Percentage Format Conversion)
功能描述：识别和转换各种百分比表示形式，支持文字描述、符号表示和数值转换。
支持格式：
- 符号形式：28%, 28 %
- 文字形式：28 percent, 28 pct
- LaTeX 包装：$28$ %, \boxed{28} pct
- 小数转换：0.28 ↔ 28%
  输入格式：包含百分比的文本表达式
  输出格式：统一的百分比或小数表示
  详细测试样例：
  输入格式
  测试输入
  期望输出
  转换类型
  标准符号
  "28%"
  "28%"
  保持原样
  带空格
  "28 %"
  "28%"
  移除空格
  文字形式
  "28 percent"
  "28%"
  文字转符号
  缩写形式
  "28 pct"
  "28%"
  缩写转符号
  LaTeX包装
  "$28$ %"
  "28%"
  提取并转换
  boxed形式
  "\\boxed{28} pct"
  "28%"
  复合提取
  实际例程：
  from math_verify import parse, verify

# 百分比格式转换测试
percentage_tests = [
"The interest rate is 28%",
"Success rate: 95 percent",
"The value is $\\boxed{50}$ pct",
"Efficiency: 99.9 %",
"Growth rate is 28 percent annually"
]

for text in percentage_tests:
result = parse(text)
print(f"输入: {text}")
print(f"解析: {result}\n")

# 百分比等价性验证
test_pairs = [
("25%", "0.25"),
("50 percent", "0.5"),
("\\boxed{75} pct", "0.75"),
("100%", "1.0")
]

for percent_form, decimal_form in test_pairs:
p1 = parse(f"Value: {percent_form}")
p2 = parse(f"Value: {decimal_form}")
is_equal = verify(p1, p2)
print(f"{percent_form} ≡ {decimal_form}: {'✅' if is_equal else '❌'}")
边界情况处理：
# 小数百分比
decimal_percent = parse("The rate is 12.5%")
print(f"小数百分比: {decimal_percent}")

# 科学记数法百分比
scientific_percent = parse("Error rate: 1.23e-2%")
print(f"科学记数法: {scientific_percent}")

# 多个百分比
multiple_percent = parse("Rates are 10%, 20%, and 30%")
print(f"多个百分比: {multiple_percent}")

节点 5：集合与区间表示统一 (Set and Interval Normalization)
功能描述：统一处理各种集合和区间表示法，支持有限集合、无限集合、开闭区间等数学结构。
支持的表示法：
- 有限集合：{1,2,3}, \{a,b,c\}
- 区间表示：[0,1], (0,1), [0,1)
- 集合运算：A ∪ B, A ∩ B, A \ B
- 特殊集合：ℕ, ℤ, ℚ, ℝ
  输入格式：集合或区间的数学表达式
  输出格式：标准化的 SymPy 集合/区间对象
  详细测试样例：
  表示类型
  测试输入
  期望输出
  SymPy类型
  有限集合
  "$\\{1,2,3\\}$"
  FiniteSet(1,2,3)
  FiniteSet
  闭区间
  "$[0,1]$"
  Interval(0,1)
  Interval
  开区间
  "$(0,1)$"
  Interval.open(0,1)
  Interval
  半开区间
  "$[0,1)$"
  Interval.Ropen(0,1)
  Interval
  集合并
  "$\\{1,3\\} \\cup \\{2,4\\}$"
  FiniteSet(1,2,3,4)
  Union
  集合交
  "$\\{1,2,3\\} \\cap \\{2,3,4\\}$"
  FiniteSet(2,3)
  Intersection
  实际例程：
  from math_verify import parse, verify
  import sympy as sp

# 集合表示测试
set_expressions = [
"$\\{1, 2, 3\\}$",                    # 有限集合
"$[0, 1]$",                           # 闭区间
"$(0, 1)$",                           # 开区间  
"$[0, 1)$",                           # 半开区间
"$\\{1,3\\} \\cup \\{2,4\\}$",       # 集合并
"$\\{1,2,3\\} \\cap \\{2,3,4\\}$"   # 集合交
]

for expr in set_expressions:
result = parse(f"The set is {expr}")
print(f"输入: {expr}")
print(f"解析: {result}")
print(f"类型: {type(result[0]) if result else 'None'}\n")

# 集合等价性验证
equivalence_tests = [
("$\\{1,2,3\\}$", "$\\{3,2,1\\}$"),         # 元素顺序无关
("$[0,1] \\cup [1,2]$", "$[0,2]$"),         # 区间合并
("$(0,1) \\cup (1,2)$", "$\\{x: 0<x<2, x \\neq 1\\}$"), # 不连续区间
]

for set1, set2 in equivalence_tests:
s1 = parse(f"Set A: {set1}")
s2 = parse(f"Set B: {set2}")
is_equal = verify(s1, s2, allow_set_relation_comp=True)
print(f"{set1} ≡ {set2}: {'✅' if is_equal else '❌'}")
高级集合操作：
# 无限集合表示
infinite_sets = [
"$mathbb{N}$",     # 自然数集
"$mathbb{Z}$",     # 整数集
"$mathbb{Q}$",     # 有理数集
"$mathbb{R}$",     # 实数集
]

for inf_set in infinite_sets:
result = parse(f"Domain: {inf_set}")
print(f"无限集合 {inf_set}: {result}")

# 集合建构式
constructive_sets = [
"$x in mathbb{R} : x^2 < 4$",
"$n in mathbb{N} : n text{ is prime}$"
]

for const_set in constructive_sets:
result = parse(f"Set: {const_set}")
print(f"建构式 {const_set}: {result}")

节点 6：关系式与不等式处理 (Relation and Inequality Processing)
功能描述：处理各种关系符号和不等式表达式，支持方向翻转、等价变换和复合不等式链式处理。
支持的关系符号：
- 不等号：≥, ≤, >, <, \geq, \leq
- 等号变体：=, ≠, \neq
- 方向翻转：5 ≤ x ↔ x ≥ 5
- 复合关系：a < b < c, x ≥ 0 and x ≤ 1
  输入格式：包含关系符号的数学表达式
  输出格式：标准化的关系表达式或布尔值
  详细测试样例：
  关系类型
  测试输入
  期望输出
  处理说明
  大于等于
  "$x \\geq 5$"
  "x >= 5"
  标准化符号
  翻转等价
  "$5 \\leq x$"
  "x >= 5"
  自动翻转方向
  不等于
  "$x \\neq 3$"
  "x != 3"
  符号转换
  变号翻转
  "$-x > -1$"
  "x < 1"
  处理负号变号
  复合不等式
  "$0 \\leq x \\leq 1$"
  "(x >= 0) & (x <= 1)"
  分解复合关系
  绝对值不等式
  `"$
  x
  < 3$"`
  实际例程：
  from math_verify import parse, verify

# 关系式处理测试
relation_expressions = [
"$x \\geq 5$",           # 标准不等式
"$5 \\leq x$",           # 翻转形式
"$x \\neq 3$",           # 不等于
"$-x > -1$",             # 负号处理
"$0 \\leq x \\leq 1$",   # 复合不等式
"$|x| < 3$"              # 绝对值不等式
]

for expr in relation_expressions:
result = parse(f"Condition: {expr}")
print(f"输入: {expr}")
print(f"标准化: {result}\n")

# 关系等价性验证
equivalence_tests = [
("$x \\geq 5$", "$5 \\leq x$"),       # 方向翻转等价
("$x > 0$", "$0 < x$"),               # 简单翻转
("$-x < 1$", "$x > -1$"),             # 变号翻转
]

for rel1, rel2 in equivalence_tests:
r1 = parse(f"Relation 1: {rel1}")
r2 = parse(f"Relation 2: {rel2}")
is_equal = verify(r1, r2)
print(f"{rel1} ≡ {rel2}: {'✅' if is_equal else '❌'}")
复合关系处理：
# 复杂不等式系统
complex_relations = [
"$x^2 - 4 \\geq 0$",                 # 二次不等式
"$\\frac{1}{x} > 2$",                # 分式不等式
"$\\sqrt{x-1} \\leq 3$",             # 根式不等式
]

for expr in complex_relations:
result = parse(f"Solve: {expr}")
print(f"复杂不等式 {expr}: {result}")

节点 7：矩阵格式识别与统一 (Matrix Format Recognition)
功能描述：识别和统一不同的矩阵表示格式，支持多种LaTeX矩阵环境，自动检测维度和类型。
支持的矩阵环境：
- 括号矩阵：pmatrix, bmatrix, Bmatrix
- 行列式：vmatrix, Vmatrix
- 数组环境：array, matrix
- 特殊矩阵：单位矩阵、零矩阵、对角矩阵
  输入格式：矩阵的LaTeX表达式
  输出格式：标准化的SymPy矩阵对象
  详细测试样例：
  矩阵类型
  测试输入
  期望输出
  矩阵环境
  括号矩阵
  "\\begin{pmatrix}1 & 0 \\\\ 0 & 1\\end{pmatrix}"
  Matrix([[1,0],[0,1]])
  pmatrix
  方括号矩阵
  "\\begin{bmatrix}1 & 2 \\\\ 3 & 4\\end{bmatrix}"
  Matrix([[1,2],[3,4]])
  bmatrix
  花括号矩阵
  "\\begin{Bmatrix}0 & 1 \\\\ -1 & 0\\end{Bmatrix}"
  Matrix([[0,1],[-1,0]])
  Bmatrix
  行列式
  "\\begin{vmatrix}a & b \\\\ c & d\\end{vmatrix}"
  a*d - b*c
  vmatrix
  数组环境
  "\\left[\\begin{array}{cc}1 & 2 \\\\ 3 & 4\\end{array}\\right]"
  Matrix([[1,2],[3,4]])
  array
  列向量
  "\\begin{pmatrix}1 \\\\ 2 \\\\ 3\\end{pmatrix}"
  Matrix([[1],[2],[3]])
  column
  实际例程：
  from math_verify import parse, verify
  import sympy as sp

# 矩阵格式测试
matrix_expressions = [
"$\\begin{pmatrix}1 & 0 \\\\ 0 & 1\\end{pmatrix}$",     # 单位矩阵
"$\\begin{bmatrix}1 & 2 \\\\ 3 & 4\\end{bmatrix}$",     # 2x2矩阵
"$\\begin{vmatrix}a & b \\\\ c & d\\end{vmatrix}$",      # 行列式
"$\\begin{pmatrix}1 \\\\ 2 \\\\ 3\\end{pmatrix}$",      # 列向量
]

for expr in matrix_expressions:
result = parse(f"Matrix: {expr}")
print(f"输入: {expr}")
print(f"解析: {result}")
if result:
matrix = result[0]
print(f"维度: {matrix.shape if hasattr(matrix, 'shape') else 'N/A'}")
print()

# 矩阵运算验证
matrix_operations = [
("$\\begin{pmatrix}1 & 0 \\\\ 0 & 1\\end{pmatrix}$", "Identity matrix"),
("$\\begin{pmatrix}0 & 0 \\\\ 0 & 0\\end{pmatrix}$", "Zero matrix"),
]

for matrix_expr, description in matrix_operations:
result = parse(f"The {description} is {matrix_expr}")
print(f"{description}: {result}")
高级矩阵处理：
# 复杂矩阵表达式
complex_matrices = [
"$A = \\begin{pmatrix} \\cos\\theta & -\\sin\\theta \\\\ \\sin\\theta & \\cos\\theta \\end{pmatrix}$",  # 旋转矩阵
"$B = \\begin{pmatrix} 1 & 0 & 0 \\\\ 0 & \\frac{1}{2} & 0 \\\\ 0 & 0 & 3 \\end{pmatrix}$",           # 对角矩阵
]

for expr in complex_matrices:
result = parse(expr)
print(f"复杂矩阵: {result}")

# 矩阵等价性检验
equiv_matrices = [
("$\\begin{pmatrix}1 & 2\\end{pmatrix}$", "$[1, 2]$"),          # 行向量等价
("$\\begin{bmatrix}1 \\\\ 2\\end{bmatrix}$", "$\\begin{pmatrix}1 \\\\ 2\\end{pmatrix}$"),  # 环境等价
]

for m1, m2 in equiv_matrices:
mat1 = parse(f"Matrix A: {m1}")
mat2 = parse(f"Matrix B: {m2}")
is_equal = verify(mat1, mat2)
print(f"{m1} ≡ {m2}: {'✅' if is_equal else '❌'}")

节点 8：方程式解析与变量分离 (Equation Parsing and Variable Isolation)
功能描述：解析和处理各种方程式，分离变量和常数项，处理等式链和赋值语句。
支持的方程类型：
- 变量赋值：x = 5, k = \frac{1}{3}
- 等式链：x = 2+3 = 5
- 多元方程：2x + 3y = 6
- 参数方程：x(t) = \cos(t)
  输入格式：包含等式的数学表达式
  输出格式：解析后的变量-值对或方程结构
  详细测试样例：
  方程类型
  测试输入
  期望输出
  解析类型
  简单赋值
  "$k = \\frac{1}{3}$"
  {"variable": "k", "value": "1/3"}
  assignment
  等式链
  "$x = 5+5+1 = 7 = 11$"
  {"variable": "x", "value": "11"}
  chain
  多项式方程
  "$2x+4y-3=0$"
  {"equation": "2*x + 4*y - 3 = 0"}
  polynomial
  函数方程
  "$f(x) = x^2 + 1$"
  {"function": "f", "variable": "x", "expression": "x^2 + 1"}
  function
  参数方程
  "$x = r\\cos\\theta$"
  {"variable": "x", "expression": "r*cos(theta)"}
  parametric
  系统方程
  "$\\begin{cases} x+y=3 \\\\ x-y=1 \\end{cases}$"
  {"system": ["x+y=3", "x-y=1"]}
  system
  实际例程：
  from math_verify import parse, verify

# 方程解析测试
equation_expressions = [
"$k = \\frac{1}{3}$",                    # 简单赋值
"$x = 5+5+1 = 11$",                      # 等式链
"$2x + 4y - 3 = 0$",                     # 线性方程
"$f(x) = x^2 + 1$",                      # 函数定义
"$y = mx + b$",                          # 直线方程
]

for expr in equation_expressions:
result = parse(f"Equation: {expr}")
print(f"输入: {expr}")
print(f"解析: {result}\n")

# 方程求解和验证
solve_tests = [
("$x = 3$", "$x - 3 = 0$"),             # 解与方程等价
("$y = 2x + 1$", "$2x - y + 1 = 0$"),   # 函数与方程转换
]

for solution, equation in solve_tests:
sol = parse(f"Solution: {solution}")
eq = parse(f"Equation: {equation}")
# 注意：这里需要特殊的验证逻辑
print(f"解 {solution} 验证方程 {equation}")
复杂方程处理：
# 高次方程和特殊函数
advanced_equations = [
"$x^3 - 2x^2 + x - 1 = 0$",             # 三次方程
"$\\sin(x) = \\frac{1}{2}$",            # 三角方程  
"$e^x = 2$",                             # 指数方程
"$\\ln(x) = 1$",                         # 对数方程
]

for expr in advanced_equations:
result = parse(f"Solve: {expr}")
print(f"高级方程 {expr}: {result}")

# 方程组处理
system_equations = [
"$\\begin{cases} x + y = 5 \\\\ x - y = 1 \\end{cases}$",
"$\\begin{align} 2x + 3y &= 6 \\\\ 4x - y &= 2 \\end{align}$"
]

for system in system_equations:
result = parse(f"System: {system}")
print(f"方程组: {result}")
节点 9：数值精度控制与比较 (Numerical Precision Control)
功能描述：提供灵活的数值比较精度控制，处理浮点数舍入、符号与数值混合比较等复杂场景。
精度控制策略：
- 可配置精度阈值：支持1-15位小数精度
- 浮点数舍入模式：四舍五入、向上取整、向下取整
- 符号数值混合：分数与小数的等价比较
- 科学记数法：极大值和极小值的精确处理
  输入格式：两个数值表达式和精度参数
  输出格式：布尔值表示是否在指定精度内相等
  详细测试样例：
  比较类型
  数值1
  数值2
  精度
  期望结果
  测试说明
  分数小数
  1/3
  0.3333
  4
  True
  4位精度内相等
  高精度分数
  1/12
  0.0833333333333333
  6
  True
  6位精度比较
  坐标对
  (1,9/2)
  (1,4.5)
  6
  True
  坐标分量比较
  科学记数法
  1.23e-10
  0.000000000123
  12
  True
  科学记数法展开
  近似值
  π
  3.14159
  5
  True
  常数近似
  精度边界
  1/3
  0.333
  3
  True
  精度边界测试
  实际例程：
  from math_verify import parse, verify

# 精度控制测试
precision_tests = [
("1/3", "0.3333", 4),
("1/12", "0.0833333333333333", 6),
("π", "3.14159", 5),
("e", "2.71828", 5),
("√2", "1.41421", 5),
]

for val1, val2, precision in precision_tests:
result1 = parse(f"Value 1: {val1}")
result2 = parse(f"Value 2: {val2}")
is_equal = verify(result1, result2, float_rounding=precision)
print(f"{val1} ≈ {val2} (精度{precision}): {'✅' if is_equal else '❌'}")

# 坐标和向量精度比较
coordinate_tests = [
("(1, 9/2)", "(1, 4.5)"),
("(√2, √3)", "(1.414, 1.732)"),
("[1/3, 2/3, 1]", "[0.333, 0.667, 1.0]"),
]

for coord1, coord2 in coordinate_tests:
c1 = parse(f"Coordinate: {coord1}")
c2 = parse(f"Coordinate: {coord2}")
is_equal = verify(c1, c2, float_rounding=3)
print(f"{coord1} ≈ {coord2}: {'✅' if is_equal else '❌'}")
精度策略配置：
# 不同精度策略测试
import math

test_value = "1/3"
approximations = ["0.3", "0.33", "0.333", "0.3333", "0.33333"]

base = parse(f"Exact: {test_value}")

for i, approx in enumerate(approximations, 1):
approx_val = parse(f"Approx: {approx}")
is_equal = verify(base, approx_val, float_rounding=i)
print(f"1/3 ≈ {approx} (精度{i}位): {'✅' if is_equal else '❌'}")

# 边界精度测试
boundary_cases = [
("0.999999", "1.0", 5),      # 接近整数
("1e-10", "0", 9),           # 极小值
("1e10", "10000000000", 0),  # 极大值
]

for val1, val2, precision in boundary_cases:
v1 = parse(f"Value: {val1}")
v2 = parse(f"Value: {val2}")
result = verify(v1, v2, float_rounding=precision)
print(f"边界测试 {val1} ≈ {val2}: {'✅' if result else '❌'}")

节点 10：字符串类型答案提取 (String Type Answer Extraction)
功能描述：提取和处理非数值类型的答案，包括选择题选项、人名、地名等文本标识符。
支持的字符串类型：
- 选择题选项：A、B、C、D等字母选项
- 布尔值：True、False、Yes、No
- 人名地名：专有名词识别
- 自定义模式：用户定义的字符串集合
  输入格式：包含字符串答案的文本
  输出格式：提取的字符串答案列表
  详细测试样例
  字符串类型
  测试输入
  期望输出
  提取模式
  选择题选项
  "The answer is A."
  ["A"]
  choice_letter
  简单选项
  "Final answer is B"
  ["B"]
  choice_letter
  布尔值
  "The statement is True"
  ["True"]
  boolean
  人名
  "Therefore, Evelyn"
  ["Evelyn"]
  proper_name
  地名
  "The capital is Paris"
  ["Paris"]
  location
  是否回答
  "Answer: Yes, it is correct"
  ["Yes"]
  yes_no
  实际例程：
  from math_verify import parse
  from math_verify import StringExtractionConfig

# 选择题答案提取
choice_questions = [
"The answer is A.",
"Select option B from the choices.",
"The correct choice is (C).",
"Answer: D is the best option.",
]

choice_config = StringExtractionConfig(
strings=("A", "B", "C", "D", "E"),
lowercase=False,
try_extract_without_anchor=True
)

for question in choice_questions:
result = parse(question, [choice_config])
print(f"选择题: {question}")
print(f"答案: {result}\n")

# 布尔值答案提取
boolean_questions = [
"The statement is True.",
"This claim is False.",
"Answer: Yes, the condition holds.",
"No, this is incorrect.",
]

boolean_config = StringExtractionConfig(
strings=("True", "False", "Yes", "No"),
lowercase=False,
try_extract_without_anchor=True
)

for question in boolean_questions:
result = parse(question, [boolean_config])
print(f"布尔题: {question}")
print(f"答案: {result}\n")
人名地名提取：
# 专有名词提取配置
name_config = StringExtractionConfig(
strings=("Alice", "Bob", "Charlie", "David", "Evelyn"),
lowercase=False,
try_extract_without_anchor=True
)

location_config = StringExtractionConfig(
strings=("Beijing", "Shanghai", "Guangzhou", "Shenzhen", "Paris", "London"),
lowercase=False,
try_extract_without_anchor=True
)

# 人名问题测试
name_questions = [
"Therefore, Evelyn is the winner.",
"The person responsible is Alice.",
"Bob completed the task successfully.",
]

for question in name_questions:
result = parse(question, [name_config])
print(f"人名题: {question}")
print(f"人名: {result}\n")

# 地名问题测试
location_questions = [
"The capital city is Beijing.",
"The meeting will be held in Paris.",
"Shanghai is the largest city.",
]

for question in location_questions:
result = parse(question, [location_config])
print(f"地名题: {question}")  
print(f"地名: {result}\n")
自定义字符串模式：
# 自定义答案集合
custom_strings = ("优秀", "良好", "及格", "不及格")
custom_config = StringExtractionConfig(
strings=custom_strings,
lowercase=False,
try_extract_without_anchor=True
)

grade_questions = [
"学生的成绩是优秀。",
"评分结果：良好",
"最终等级为及格。",
]

for question in grade_questions:
result = parse(question, [custom_config])
print(f"等级题: {question}")
print(f"等级: {result}\n")

节点 11：复杂表达式等价性验证 (Complex Expression Equivalence)
功能描述：验证复杂数学表达式的等价性，包括代数变换、三角恒等式、微积分表达式等高级数学结构。
支持的等价类型：
- 代数恒等式：多项式展开、因式分解
- 三角恒等式：sin、cos、tan的恒等变换
- 指数对数：指数函数与对数函数互逆
- 微积分：导数积分的等价表达
  输入格式：两个复杂的数学表达式
  输出格式：布尔值表示是否数学等价
  详细测试样例：
  等价类型
  表达式1
  表达式2
  期望结果
  验证说明
  代数展开
  (x+2)/7
  x/7+2/7
  True
  分数展开
  三角恒等式
  tan²(y)+1
  sec²(y)
  True
  三角恒等式
  方程等价
  34x+45y-20z+100=0
  -34x-45y+20z-100=0
  True
  方程变号
  对数性质
  ln(ab)
  ln(a)+ln(b)
  True
  对数运算法则
  指数性质
  a^m * a^n
  a^(m+n)
  True
  指数运算法则
  根式变换
  √(x²)
  `
  x
  `
  实际例程：
  from math_verify import parse, verify

# 代数等价性测试
algebraic_equivalences = [
("(x+2)/7", "x/7+2/7"),                    # 分数展开
("(a+b)²", "a²+2ab+b²"),                   # 完全平方公式
("x²-y²", "(x+y)(x-y)"),                  # 平方差公式
("x³+y³", "(x+y)(x²-xy+y²)"),             # 立方和公式
]

print("=== 代数等价性验证 ===")
for expr1, expr2 in algebraic_equivalences:
e1 = parse(f"Expression 1: {expr1}")
e2 = parse(f"Expression 2: {expr2}")
is_equiv = verify(e1, e2)
print(f"{expr1} ≡ {expr2}: {'✅' if is_equiv else '❌'}")

# 三角函数恒等式
trigonometric_identities = [
("sin²(x)+cos²(x)", "1"),                 # 基本恒等式
("tan²(x)+1", "sec²(x)"),                 # 正切割线关系
("sin(2x)", "2sin(x)cos(x)"),             # 二倍角公式
("cos(2x)", "cos²(x)-sin²(x)"),           # 二倍角公式
]

print("\n=== 三角恒等式验证 ===")
for expr1, expr2 in trigonometric_identities:
e1 = parse(f"Trig 1: {expr1}")
e2 = parse(f"Trig 2: {expr2}")
is_equiv = verify(e1, e2)
print(f"{expr1} ≡ {expr2}: {'✅' if is_equiv else '❌'}")
高级数学结构：
# 微积分等价性
calculus_equivalences = [
("d/dx(x²)", "2x"),                       # 求导
("∫2x dx", "x² + C"),                     # 积分
("d/dx(sin(x))", "cos(x)"),               # 三角函数求导
("∫cos(x) dx", "sin(x) + C"),             # 三角函数积分
]

print("=== 微积分等价性验证 ===")
for expr1, expr2 in calculus_equivalences:
try:
e1 = parse(f"Calc 1: {expr1}")
e2 = parse(f"Calc 2: {expr2}")
is_equiv = verify(e1, e2)
print(f"{expr1} ≡ {expr2}: {'✅' if is_equiv else '❌'}")
except Exception as e:
print(f"{expr1} ≡ {expr2}: ⚠️ 解析错误: {e}")

# 复数等价性
complex_equivalences = [
("i²", "-1"),                             # 虚数单位
("(1+i)²", "2i"),                         # 复数运算
("|3+4i|", "5"),                          # 复数模长
("e^(iπ)", "-1"),                         # 欧拉公式
]

print("\n=== 复数等价性验证 ===")
for expr1, expr2 in complex_equivalences:
try:
e1 = parse(f"Complex 1: {expr1}")
e2 = parse(f"Complex 2: {expr2}")
is_equiv = verify(e1, e2)
print(f"{expr1} ≡ {expr2}: {'✅' if is_equiv else '❌'}")
except Exception as e:
print(f"{expr1} ≡ {expr2}: ⚠️ 解析错误: {e}")

节点 12：超时保护与错误处理 (Timeout Protection and Error Handling)
功能描述：为复杂表达式的解析和验证提供超时保护机制，实现优雅降级和详细错误报告。
保护机制：
- 可配置超时时间：1-60秒可调
- 优雅降级策略：超时后尝试简化解析
- 错误分类记录：语法错误、计算错误、超时错误
- 恢复机制：部分解析结果保留
  输入格式：待处理的表达式和超时时间限制
  输出格式：处理结果或详细错误信息
  详细测试样例：
  测试场景
  输入表达式
  超时时间
  期望行为
  错误类型
  简单表达式
  1+1
  1s
  正常解析
  None
  复杂计算
  大型矩阵运算
  5s
  正常解析或超时
  TimeoutError
  语法错误
  \frac{1}
  1s
  语法错误
  SyntaxError
  无限循环
  递归定义
  2s
  超时保护
  TimeoutError
  内存溢出
  巨大表达式
  3s
  内存保护
  MemoryError
  实际例程：
  from math_verify import parse
  import time

# 超时保护测试
def test_with_timeout(expression, timeout_seconds, description):
"""测试带超时保护的解析"""
print(f"测试: {description}")
print(f"表达式: {expression}")
print(f"超时限制: {timeout_seconds}秒")

    start_time = time.time()
    try:
        # 注意：这里假设parse函数支持timeout参数
        # 实际实现中可能需要使用signal或threading
        result = parse(expression, parsing_timeout=timeout_seconds)
        elapsed = time.time() - start_time
        print(f"✅ 解析成功: {result}")
        print(f"⏱️ 耗时: {elapsed:.3f}秒\n")
        return True, result
    except Exception as e:
        elapsed = time.time() - start_time
        error_type = type(e).__name__
        print(f"❌ 解析失败: {error_type}")
        print(f"📋 错误信息: {str(e)}")
        print(f"⏱️ 耗时: {elapsed:.3f}秒\n")
        return False, str(e)

# 测试用例
timeout_test_cases = [
("$1+1$", 1, "简单表达式"),
("$\\frac{1}{2} + \\frac{1}{3}$", 2, "分数运算"),
("$\\sum_{i=1}^{100} i^2$", 3, "求和表达式"),
("$\\int_0^1 x^2 dx$", 5, "积分表达式"),
]

results = []
for expr, timeout, desc in timeout_test_cases:
success, result = test_with_timeout(expr, timeout, desc)
results.append((expr, success, result))

# 统计结果
success_count = sum(1 for _, success, _ in results if success)
print(f"📊 测试统计: {success_count}/{len(results)} 成功")
错误处理和恢复：
# 错误分类处理
def robust_parse(expression, max_retries=3):
"""带重试机制的鲁棒解析"""

    for attempt in range(max_retries):
        try:
            print(f"🔄 尝试 {attempt + 1}/{max_retries}: {expression}")
            
            # 第一次尝试：完整解析
            if attempt == 0:
                result = parse(expression)
                print(f"✅ 完整解析成功: {result}")
                return result
            
            # 第二次尝试：简化表达式
            elif attempt == 1:
                simplified = expression.replace("\\", "").replace("{", "").replace("}", "")
                result = parse(simplified)
                print(f"⚡ 简化解析成功: {result}")
                return result
                
            # 第三次尝试：提取数值部分
            else:
                import re
                numbers = re.findall(r'-?\d+\.?\d*', expression)
                if numbers:
                    result = [float(num) for num in numbers]
                    print(f"🔧 数值提取成功: {result}")
                    return result
                    
        except Exception as e:
            error_type = type(e).__name__
            print(f"❌ 尝试 {attempt + 1} 失败: {error_type} - {str(e)}")
            
            if attempt == max_retries - 1:
                print(f"💥 所有尝试失败，返回空结果")
                return []
            
    return []

# 测试鲁棒解析
problematic_expressions = [
"$\\frac{1}{2}$",                    # 正常表达式
"$\\frac{1$",                        # 语法错误
"$very_complex_expression$",          # 未定义符号
"some random text with 123 and 456", # 纯文本
]

for expr in problematic_expressions:
print(f"\n{'='*50}")
result = robust_parse(expr)
print(f"最终结果: {result}")

节点 13：多选项答案聚合 (Multiple Choice Answer Aggregation)
功能描述：处理包含多个答案的复杂情况，如多选题、解集、条件答案等，将其聚合为统一格式。
聚合策略：
- 多答案合并：合并多个独立答案
- 解集处理：数学解集的集合表示
- 条件答案：依赖条件的分支答案
- 排序优化：按重要性或逻辑顺序排列
  输入格式：包含多个答案项的文本
  输出格式：聚合后的答案集合或结构化数据
  详细测试样例：
  聚合类型
  测试输入
  期望输出
  聚合方式
  多个数值
  "\\boxed{1} and \\boxed{2} and \\boxed{3}"
  {1,2,3}
  数值集合
  坐标点
  "solutions are (1,2), (3,4)"
  [(1,2), (3,4)]
  坐标列表
  参数解
  "p=5,q=2; p=7,q=2"
  [(5,2), (7,2)]
  参数组合
  区间并集
  "x ∈ [0,1] ∪ [2,3]"
  [0,1] ∪ [2,3]
  区间聚合
  多选答案
  "Answers: A, C, and D"
  ["A", "C", "D"]
  选项列表
  条件分支
  "If x>0: y=1; If x<0: y=-1"
  {condition: result}
  条件映射
  实际例程：
  from math_verify import parse
  import re

# 多数值答案聚合
multi_numeric_cases = [
"\\boxed{1} and \\boxed{2} and \\boxed{3}",
"The solutions are 5, 7, and 11.",
"Answers: $x = 2$, $x = 4$, $x = 6$",
"Results: \\boxed{\\frac{1}{2}}, \\boxed{\\frac{3}{4}}, \\boxed{1}",
]

print("=== 多数值答案聚合 ===")
for case in multi_numeric_cases:
result = parse(f"Multiple answers: {case}")
print(f"输入: {case}")
print(f"聚合: {result}")
print(f"类型: {type(result) if result else 'None'}\n")

#坐标点聚合
coordinate_cases = [
"The intersection points are (1,2) and (3,4)",
"Solutions: $(0,0)$, $(1,1)$, $(2,4)$",
"Points: \\boxed{(0,1)}, \\boxed{(2,3)}, \\boxed{(4,5)}",
]

print("=== 坐标点聚合 ===")
for case in coordinate_cases:
result = parse(f"Coordinates: {case}")
print(f"输入: {case}")
print(f"坐标集: {result}\n")
复杂聚合场景：

```python
# 参数方程解集
parametric_solutions = [
    "For t ∈ [0,2π]: x = cos(t), y = sin(t)",
    "When k = 0,1,2: x = 2k+1",
    "Solutions: (a,b) = (1,2), (3,4), (5,6)",
]

print("=== 参数解集聚合 ===")
for solution in parametric_solutions:
    result = parse(f"Parametric: {solution}")
    print(f"参数解: {solution}")
    print(f"解集: {result}\n")

# 条件分支答案
conditional_answers = [
    "If x > 0: answer is positive; If x < 0: answer is negative",
    "When n is even: f(n) = n/2; When n is odd: f(n) = 3n+1",
    "Case 1: result = A; Case 2: result = B; Case 3: result = C",
]

print("=== 条件分支聚合 ===")
for condition in conditional_answers:
    result = parse(f"Conditional: {condition}")
    print(f"条件: {condition}")
    print(f"分支: {result}\n")

# 自定义聚合函数
def aggregate_multiple_answers(text, answer_type="numeric"):
    """自定义答案聚合函数"""
    
    if answer_type == "numeric":
        # 提取所有数值
        numbers = re.findall(r'-?\d+\.?\d*', text)
        return [float(num) for num in numbers]
        
    elif answer_type == "choice":
        # 提取选择题选项
        choices = re.findall(r'\b[A-E]\b', text)
        return list(set(choices))  # 去重
        
    elif answer_type == "coordinate":
        # 提取坐标点
        coords = re.findall(r'\((-?\d+\.?\d*),\s*(-?\d+\.?\d*)\)', text)
        return [(float(x), float(y)) for x, y in coords]
        
    else:
        return []

# 测试自定义聚合
test_aggregations = [
    ("Numbers: 1, 2, 3, 4, 5", "numeric"),
    ("Choices: A, B, C, A, D", "choice"), 
    ("Points: (1,2), (3,4), (5,6)", "coordinate"),
]

print("=== 自定义聚合测试 ===")
for text, atype in test_aggregations:
    result = aggregate_multiple_answers(text, atype)
    print(f"文本: {text}")
    print(f"类型: {atype}")
    print(f"聚合: {result}\n")
```
聚合优化和去重：
# 智能去重和排序
def smart_aggregate(answers, sort_by="value"):
"""智能答案聚合，支持去重和排序"""

    if not answers:
        return []
    
    # 去重处理
    unique_answers = []
    seen = set()
    
    for answer in answers:
        # 转换为可哈希的格式进行去重
        if isinstance(answer, (list, tuple)):
            key = tuple(answer)
        else:
            key = answer
            
        if key not in seen:
            seen.add(key)
            unique_answers.append(answer)
    
    # 排序处理
    if sort_by == "value" and all(isinstance(a, (int, float)) for a in unique_answers):
        unique_answers.sort()
    elif sort_by == "length" and all(isinstance(a, str) for a in unique_answers):
        unique_answers.sort(key=len)
    
    return unique_answers

# 测试智能聚合
test_cases = [
[1, 3, 2, 1, 4, 2, 5],           # 数值去重排序
["A", "C", "B", "A", "D"],       # 字符串去重
[(1,2), (3,4), (1,2), (5,6)],   # 坐标去重
]

for i, case in enumerate(test_cases, 1):
result = smart_aggregate(case)
print(f"测试 {i}:")
print(f"原始: {case}")
print(f"聚合: {result}\n")
