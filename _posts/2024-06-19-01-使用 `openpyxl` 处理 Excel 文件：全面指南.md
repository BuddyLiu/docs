---
title:      使用 `openpyxl` 处理 Excel 文件：全面指南
date:       2024-06-19
tags:
- python
- openpyxl
--- 

在日常的数据处理和分析中，Excel 文件是我们经常接触的工具。Python 提供了多个库来处理 Excel 文件，其中 `openpyxl` 是一个功能强大且易于使用的选择。在这篇博客中，我将向大家介绍 `openpyxl` 库的各种功能，并通过示例代码展示其具体用法。无论你是数据科学家、数据分析师，还是需要处理大量数据的开发者，相信这篇文章都能帮助你更高效地处理 Excel 文件。

### 基本操作

#### 创建工作簿和工作表

首先，我们来看如何使用 `openpyxl` 创建新的工作簿和工作表。

```python
import openpyxl

# 创建新的工作簿
wb = openpyxl.Workbook()

# 激活默认的工作表
ws = wb.active

# 重命名工作表
ws.title = "MySheet"

# 添加新工作表
ws2 = wb.create_sheet(title="NewSheet")

# 保存工作簿
wb.save('example.xlsx')
```

这段代码展示了如何创建一个新的 Excel 工作簿，并向其中添加和重命名工作表。

#### 打开和保存工作簿

除了创建新工作簿外，我们还可以打开现有的工作簿进行修改，并保存更改。

```python
# 打开现有工作簿
wb = openpyxl.load_workbook('example.xlsx')

# 进行修改

# 保存工作簿
wb.save('example.xlsx')
```

### 读写单元格

#### 写入单元格

写入数据到单元格是我们最常用的操作之一。

```python
# 写入单元格
ws['A1'] = 'Hello, world!'
ws.cell(row=2, column=1, value='Another cell')
```

#### 读取单元格

同样地，我们也可以轻松地读取单元格中的数据。

```python
# 读取单元格
value = ws['A1'].value
print(value)

value2 = ws.cell(row=2, column=1).value
print(value2)
```

### 操作行和列

#### 插入和删除行/列

在处理数据时，经常需要插入或删除行和列。

```python
# 插入一行
ws.insert_rows(2)

# 删除一行
ws.delete_rows(2)

# 插入一列
ws.insert_cols(2)

# 删除一列
ws.delete_cols(2)
```

### 合并和拆分单元格

#### 合并单元格

我们可以合并多个单元格来创建一个大单元格。

```python
# 合并单元格
ws.merge_cells('A1:B2')
ws['A1'] = 'Merged Cell'
```

#### 拆分单元格

合并后的单元格也可以被拆分。

```python
# 拆分单元格
ws.unmerge_cells('A1:B2')
```

### 格式化单元格

#### 设置字体

`openpyxl` 允许我们设置单元格的字体样式。

```python
from openpyxl.styles import Font

# 设置字体
ws['A1'].font = Font(name='Arial', size=14, bold=True, italic=True)
```

#### 设置对齐方式

对齐方式可以帮助我们更好地展示数据。

```python
from openpyxl.styles import Alignment

# 设置对齐方式
ws['A1'].alignment = Alignment(horizontal='center', vertical='center')
```

#### 设置填充颜色

填充颜色可以用来突出显示某些重要数据。

```python
from openpyxl.styles import PatternFill

# 设置填充颜色
ws['A1'].fill = PatternFill(start_color='FFFF00', end_color='FFFF00', fill_type='solid')
```

### 数据验证和条件格式

#### 数据验证

我们可以使用数据验证来限制单元格输入的值。

```python
from openpyxl.worksheet.datavalidation import DataValidation

# 数据验证
dv = DataValidation(type="list", formula1='"Dog,Cat,Bat"', showDropDown=True)
ws.add_data_validation(dv)
dv.add(ws["A1"])
```

#### 条件格式

条件格式可以根据单元格的值动态地改变其格式。

```python
from openpyxl.formatting.rule import CellIsRule

# 条件格式
red_fill = PatternFill(start_color='FFEE1111', end_color='FFEE1111', fill_type='solid')
rule = CellIsRule(operator='greaterThan', formula=['10'], stopIfTrue=True, fill=red_fill)
ws.conditional_formatting.add('A1:A10', rule)
```

### 图表

#### 创建图表

使用 `openpyxl`，我们可以在 Excel 中创建图表以更直观地展示数据。

```python
from openpyxl.chart import LineChart, Reference

# 创建图表
values = Reference(ws, min_col=1, min_row=1, max_col=1, max_row=10)
chart = LineChart()
chart.add_data(values)
ws.add_chart(chart, "E5")
```

### 图片

#### 插入图片

有时候，我们需要在 Excel 中插入图片。

```python
from openpyxl.drawing.image import Image

# 插入图片
img = Image('logo.png')
ws.add_image(img, 'A1')
```

### 高级操作

#### 自动调整列宽

为了让单元格中的内容显示得更好，我们可以自动调整列宽。

```python
def auto_adjust_column_width(ws):
    for col in ws.columns:
        max_length = 0
        col_letter = openpyxl.utils.get_column_letter(col[0].column)
        for cell in col:
            try:
                if len(str(cell.value)) > max_length:
                    max_length = len(str(cell.value))
            except:
                pass
        adjusted_width = (max_length + 2)
        ws.column_dimensions[col_letter].width = adjusted_width

# 调用自动调整列宽函数
auto_adjust_column_width(ws)
```

### 工作表复制

#### 复制工作表

我们还可以复制工作表以便在同一工作簿中创建相似的工作表。

```python
# 复制工作表
source = wb.active
target = wb.copy_worksheet(source)
target.title = "CopiedSheet"
```

### 读取和写入不同类型的数据

#### 读取和写入公式

公式是 Excel 中非常强大的功能，`openpyxl` 也支持对公式的读写。

```python
# 写入公式
ws['A1'] = "=SUM(B1:B10)"

# 读取公式（注意，读取公式的结果依赖于Excel的计算）
formula = ws['A1'].value
print(formula)
```

### 读取和写入数据到多个工作表

#### 读取多个工作表的数据

我们可以遍历工作簿中的所有工作表，并读取其中的数据。

```python
# 读取多个工作表的数据
for sheet in wb:
    print(f"Sheet name: {sheet.title}")
    for row in sheet.iter_rows(values_only=True):
        print(row)
```

#### 写入数据到多个工作表

同样地，我们也可以将数据写入多个工作表。

```python
# 写入数据到多个工作表
for i in range(1, 4):
    sheet = wb.create_sheet(title=f"Sheet{i}")
    sheet['A1'] = f"This is {sheet.title}"
```

通过以上示例，我们可以看到 `openpyxl` 提供了非常丰富的功能来处理 Excel 文件。无论是基础的读写操作，还是高级的格式设置、数据验证、图表创建等，`openpyxl` 都能满足我们的需求。希望这篇博客能帮助你更好地理解和使用 `openpyxl` 处理 Excel 文件，提高工作效率。如果你有任何问题或建议，欢迎在评论区留言。