## less

`d` - Scroll down half a page
`u` - Scroll up half a page
`j/k` - Scroll down or up a line. You can also use the arrow keys for this
`q` - Quit
`/` - \<search\> - Search for text
`n` - When searching, find the next occurrence
`N` - When searching, find the previous occurrence


## VIM

```shell
# 在2~50行首添加”#”注释
:2,50s/^/#      
# 将行首的 "#" 注释删除
:2,50s/^#//
```

## `tar`

`z` - 使用 gzip 进行压缩或解压
`c` - 创建新的归档文件
`x` - 提取归档文件
`v` - 详细模式，显示操作过程
`f` - 指定归档文件名
`C` - 处理归档文件之前更改到指定的目录

```shell
# 使用 gzip 压缩
tar -zcvf output_name.tar.gz directory_or_file_to_archive
# 使用 gzip 压缩
tar -zxvf archive_name.tar.gz
# 从 /path/to/source_directory 创建一个归档，但当前不在那个目录中
tar -czvf archive_name.tar.gz -C /path/to/source_directory .
```

## `grep`
`grep`命令的名称源于 UNIX 编辑器 ed 中的命令 g/re/p，表示 "global/regular expression/print"，即全局地根据正则表达式搜索并打印匹配的行
```shell
# 在当前目录下递归找到含有字符串“dws_customer”的所有文件
grep -rl "xieliangliang/b12p6_evals/offline_batch_score/etron_sample/raw" . | grep -v ".log$"
```

## `sed`
`sed` (流编辑器) 是一个非常强大的文本处理工具，用于对输入流（来自文件、管道或标准输入）进行基于模式的文本转换。其名称sed来源于“stream editor”
`i` - 直接在文件上进行编辑，而不是输出到标准输出
`n` - 安静模式。默认情况下，sed 会打印每一行。使用 -n 选项会使 sed 只打印那些被编辑或处理的行
```shell
# 默认情况下，只有每行中的第一个匹配项会被替换。使用全局标志 g 可以替换每行中的所有匹配项
sed -i 's/fbi_edw_dws.dws_customer_apply_detail_xjd_all_dd/dxm_fbi_edw_dws.dws_customer_apply_detail_xjd_all_dd/g' file
# 使用d命令可以删除与模式匹配的行
sed '/pattern/d' file
# 插入、追加和更改: sed 提供了插入 (i)、追加 (a) 和更改 (c) 命令来修改文件的内容
sed '/pattern/i text_to_insert' file
```

```shell
grep -rl "fbi_edw_dws.dws_customer_apply_detail_xjd_all_dd" . | xargs sed -i 's/fbi_edw_dws.dws_customer_apply_detail_xjd_all_dd/dxm_fbi_edw_dws.dws_customer_apply_detail_xjd_all_dd/g'
```

## `$*  $@`

`$*` - 在双引号中将所有的参数作为一个单一的字符串返回，参数之间由第一个字符的IFS值（默认是空格）分隔。
`$@` - 在双引号中返回每个参数作为单独的字符串

## 2>&1

* Take the file with descriptor 2 - which is standard error
* Redirect it with the redirect symbol > - we saw this in the earlier section
* Redirect it into the file with descriptor (&) 1 - which is standard output

```shell
 echo "I am in the $PWD folder" | sed 's/folder/directory/'
 cat ~/effective-shell/text/simpsons-characters.txt | sort | tee sorted.txt | uniq | grep '^A'
 cat data.dat | sort | uniq | grep -v '^#' | wc -l
```

## 表达式

#### stdout和退出状态码

`stdout` 是命令产生的输出，您可以看到它、重定向它到文件或通过管道传递给其他命令。
`退出状态码` 是一个简单的数字，表示命令的成功或失败。你无法直接看到它，但可以在 shell 脚本或命令行中使用它来做决策

#### `$()` - 命令替换/子进程替换

这是执行一个命令并捕获返回其stdout。例如，result=$(ls) 将执行 ls 命令，并将其输出存储在变量 result 中。
这是 `command`（反引号）的更现代和推荐的替代方法

#### `[[ ... ]]` - 扩展的测试命令

目的：进行字符串、数字和文件测试，返回退出状态码
使用：提供了多种操作符，包括字符串比较 (==, !=), 整数比较 (-eq, -ne, -gt, -lt, -ge, -le), 文件测试 (-e, -f, -d, 等等) 和逻辑操作 (&&, ||).
示例：[[ "$string" == "hello" ]] 检查 $string 是否等于 "hello"。
特点：它提供了一个更灵活的方式来进行比较和测试，支持正则表达式和模式匹配，而 [ ... ] (基础测试命令) 不支持

#### `bc`

`bc`是一个任意精度的计算器语言，可以进行浮点数运算
result=$(echo "5.5 + 3.3" | bc)

## 管道子进程

你可能期望 `echo $some_var` 的输出是 "new value"，但实际上它是 "initial value"。这是因为`read`命令是在一个子进程中执行的（因为它是一个管道的一部分），所以它对`some_var`的修改不会影响到父进程
```shell
some_var="initial value"
echo "new value" | read some_var
echo $some_var
```

## 关联数组

在 Bash 关联数组中，赋值和引用的语法是有所不同的
```shell
# 赋值
key="some_key"
task_config["$key"]="some_value"
# 引用
echo ${task_config[some_key]}
key="some_key"
echo ${task_config[$key]}
```

## `& wait`

`&` - 将作为一个子进程开始运行，但不会阻塞当前的 shell 或脚本继续执行其他命令
`wait` - 用于等待一个或多个后台进程完成，然后返回
当使用 `&` 在后台启动一个进程并且没有使用 `wait`，那么该进程会继续运行，即使其父进程（例如您的主脚本）已经退出。这种后台进程被称为“孤儿进程













