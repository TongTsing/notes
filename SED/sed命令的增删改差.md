`sed` 是一种流式文本编辑器，常用于对文本进行处理、转换和过滤。它可以在文本流中进行搜索、替换、删除和添加操作，是处理文本的强大工具。

以下是 `sed` 命令的基本用法和示例：

### 增加行：

```bash
# 在匹配行之后添加新行
sed '/pattern/a\new line' file.txt

# 在匹配行之前添加新行
sed '/pattern/i\new line' file.txt
```

### 删除行：

```bash
# 删除匹配行
sed '/pattern/d' file.txt

# 删除行范围内的所有行
sed '2,5d' file.txt
```

### 修改行：

```bash
# 替换匹配行中的文本
sed 's/pattern/new_text/' file.txt

# 替换匹配行中的第一个匹配项
sed 's/pattern/new_text/' file.txt

# 替换匹配行中的所有匹配项
sed 's/pattern/new_text/g' file.txt
```

### 查询行：

```bash
# 显示匹配行
sed -n '/pattern/p' file.txt

# 显示不匹配行
sed -n '/pattern/!p' file.txt
```

以上是 `sed` 命令的一些基本用法和示例，可以根据具体的需求和情况进行调整和组合使用。