# python自动化办公之excel



### 安装第三方模块`openpyxl`

```python
pip3 install openpyxl
```

关于`openpyxl`模块的[参考文档](https://openpyxl.readthedocs.io/en/stable/usage.html#read-an-existing-workbook).

### 使用`python`操作excel表格，代码如下：

```python
# 引入模块
import openpyxl
import datetime

# 创建工作簿并创建一个工作表
wb = openpyxl.Workbook()
ws = wb.active

# 可以自定义工作表名字，默认Sheet
ws.title = '工作表1'
# A1 单元格写入 520
ws['A1'] = 520
# A2 单元格写入一个列表
ws.append([1,2,3])
# A3 单元格写入当前时间
ws['A3'] = datetime.datetime.now()
# 保存为 demo.xlsx
wb.save('demo.xlsx')
```

### 打开excel表格

```python
import openpyxl
wb = openpyxl.load_workbook(r'D:\code2022\auto-work\demo.xlsx')
print(type(wb)) # <class 'openpyxl.workbook.workbook.Workbook'>
```

### 查看工作表

```python
print(wb.sheetnames) # ['Sheet']
```

### 创建新的工作表并打印当前所有工作表

```python
nws = wb.create_chartsheet(title = 'demo', index = 0)
print(wb.sheetnames) # ['demo', 'Sheet']
```

### 删除一个工作表

```python
# 先查看所有工作表
print(wb.sheetnames) # ['demo', 'Sheet']
# 删除 demo 工作表
rem = wb.remove(wb['demo'])
print(wb.sheetnames) # ['Sheet']
```

### 定位单元格

```python
ws = wb.active
c = ws['A1']
# 打印行
print(c.row) # 1
# 打印列
print(c.column) # 1
# 打印当前坐标
print(c.coordinate) # A1
# 输出 A1 单元格值
print(ws['A1'].value) # 520
# 输出 A1 单元格的值
print(c.value) # 520
# 当前位置（A1）向下移动两个单元格，列不变。
d = c.offset(2,0)
# 输出当前坐标
print(d.coordinate) # A3 
# 输出当前坐标所对应的值
print(d.value)
```

### 访问多个工作表

```python
# 从（1，1） 到（4，2）范围内所有的第一列的内容
for each_row in ws.iter_rows(min_row=1, min_col=1, max_row=4, max_col=2):
    print(each_row[0].value)

# 输出从A1 到 C4 范围内的内容
for each_item in ws['A1':'C4']:
    for each_cell in each_item:
        print(each_cell.value, end = ' ')
    print('\n')
```

### 拷贝工作表

```python
ws = wb.active
new = wb.copy_worksheet(ws)
wb.save(r'D:\code2022\auto-work\demo.xlsx')
```

### 个性化工作表

更改默认工作表颜色

```python
import openpyxl
wb = openpyxl.Workbook()
ws1 = wb.create_sheet(title= '001')
ws2 = wb.create_sheet(title= '003')
ws3 = wb.create_sheet(title= '005')
ws4 = wb.create_sheet(title= '007')

ws1.sheet_properties.tabColor = "FF0000"
ws2.sheet_properties.tabColor = "00FF00"
ws3.sheet_properties.tabColor = "0000FF"
ws4.sheet_properties.tabColor = "880088"

wb.save(r"D:\code2022\auto-work\工作表变颜色.xlsx")
```

### 调整行高和列宽

```python
ws1.row_dimensions[2].height = 100
ws1.column_dimensions['C'].width = 50
wb.save(r'D:\code2022\auto-work\工作表变颜色.xlsx')
```

### 合并和拆分单元格

```python
# 合并从 A1 到 D2 范围内的所有单元格
ws2.merge_cells('A1:D2')
wb.save(r'D:\code2022\auto-work\工作表变颜色.xlsx')
# 拆分单元格,要和合并的单元格范围一致
ws2.unmerge_cells = None
wb.save(r'D:\code2022\auto-work\工作表变颜色.xlsx')
```

### 冻结窗口

指的是无论横向还是纵向滚动单元格，最左侧和最上侧单元格不会跟随滚动而变化。

```python
# 冻结窗口
import openpyxl
wb = openpyxl.load_wordbook(r'D:\code2022\auto-work\demo.xlsx')
ws = wb.active
ws.freeze_panes = 'B8'
wb.save(r'D:\code2022\auto-work\demo.xlsx')
# 解冻窗口
ws.freeze_panes = None
wb.save(r'D:\code2022\auto-work\demo.xlsx')
```

### 


















