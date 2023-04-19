# CMD

## dir

## chmod

## find

+ -type：按文件类型搜索 d/p/s/c/b/l/f

  ```sh
  # ./ 表示当前目录
  # 默认递归查找，-maxdepth指定深度
  find ./ -maxdepth 1 -type f
  ```

+ -name：按文件名搜索

  ```sh
  find ./ -maxdepth 1 -name 'r*'
  ```

+ -maxdepth：指定搜索深度（默认做第一参数）

  ```sh
  find ./ -maxdepth 1 -type f
  ```

+ -size：按文件大小搜索. 单位：k、M、G

  ```sh
  find ./ -size +20M -size -50M
  ```

+ -atime、-mtime、-ctime：天

  -amin、-mmin、-cmin：分钟

+ -exec：将find的结果集执行某一指定命令。

  ```sh
  find ./ -type f -exec ls -l {} \;
  ```

+ -ok：以交互式的方式，将find搜索结果集执行某一指定命令

  ```sh
  find ./ -type f -ok ls -l {} \;
  ```

## grep

```sh
# -r：递归 -n：显示行号
# 递归检索当面目录
grep -r 'copy' ./ -n
# 检索指定文件
grep 'set' CMakeLists.txt -n
# 通过管道符将ps命令结果传给grep进行查找
ps aux | grep 'cupsd'
```

## xargs

> xargs默认以空格分割结果集单个项目
>
> 当结果集数量过大时，xargs可以对结果集进行分片映射

```sh
find ./ -maxdepth 1 -type f -exec ls -l {} \;
# 将find搜索的结果集执行某一指定命令。当结果集数量过大时，可以分片映射。下式不加xargs结果会出错。
find ./ -maxdepth 1 -type f | xargs ls -l
# -print0以0替代空格对结果集进行分割
find ./ -maxdepth 1 -type f -print0 | xargs -0 ls -l
```

## rpm

## yum

```sh
yum install sl
yum remove sl
yum list
yum search sl
yum update
yum update sl
```

## tar

+ 压缩

  ```sh
  # z：压缩方式 gzip
  # c：create
  # v：显示压缩过程
  # f：文件
  tar zcvf test.tar.gz file1 dir2 #使用gzip方式压缩
  tar jcvf test.tar.gz file1 dir2 #使用bzip2方式压缩
  ```

+ 解压

  ```sh
  tar zxvf test.tar.gz file1 dir2 #使用gzip方式解压缩
  tar jxvf test.tar.gz file1 dir2 #使用bzip2方式解压缩
  ```

  

# VIM

## 命令

+ 插入：a 光标后、i 光标前、o 下一行
+ 撤销：u、ctrl+r
+ 删除（实为剪切）：
  + 单个字符：x
  + 一个单词(向后删除)：dw
  + d+home，d+end
  + 删除括号内容：di( 
  + 删除块：v+hjkl+d （v进入可视模式）
  + 删除行：dd，ndd（n行）
+ 复制粘贴：
  + 复制：yy
  + 粘贴：p
+ 查找替换：
  + /+sth，回车，n下一个
  + 光标选中按 *，再按下一个
  + 替换字符：r+s
+ 跳转：
  + 跳转文件首：gg
  + 跳转文件尾：G
  + 跳转指定行：88G、:88
  + 大括号跳转：%
+ 分屏：
  + 横分：sp
  + 竖分：vsp
+ 挪动光标：hjkl
+ 自动格式化程序：gg=G

## 配置
