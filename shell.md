## less

* d - Scroll down half a page
* u - Scroll up half a page
* j / k - Scroll down or up a line. You can also use the arrow keys for this
* q - Quit
* / \<search\> - Search for text
* n - When searching, find the next occurrence
* N - When searching, find the previous occurrence

## 2>&1

* Take the file with descriptor 2 - which is standard error
* Redirect it with the redirect symbol > - we saw this in the earlier section
* Redirect it into the file with descriptor (&) 1 - which is standard output

```shell
 echo "I am in the $PWD folder" | sed 's/folder/directory/'
 cat ~/effective-shell/text/simpsons-characters.txt | sort | tee sorted.txt | uniq | grep '^A'
 cat data.dat | sort | uniq | grep -v '^#' | wc -l
```

## VIM

在2~50行首添加”#”注释
:2,50s/^/#      
将行首的 "#" 注释删除
:2,50s/^#//

## merge csv
```shell
#!/bin/bash
root_dir=$1
for dir in $(find "${root_dir}" -type d); do
    if [ -f "${root_dir}/${dir}.csv" ]; then
        cat "${dir}/*.csv" > "${dir}.csv"
    fi
done
```

## 在当前目录下递归找到含有字符串“bfeats_v2_2/libsvm”的所有文件
```shell
 find . -type f -exec grep -l "bfeats_v2_2/libsvm" {} +
```










