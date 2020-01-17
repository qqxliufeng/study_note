## Linux学习笔记

### 用户/用户组管理

### 压缩/解压

- tar

  > tar -zcvf   a.tar.gz a.txt 把文件a.txt 压缩成 a.tar.gz

  ```shell
  tar -zcvf a.tar.gz a.txt
  ```

  > tar -zxvf a.tar.gz  [-C 指定目录] 解压到当前目录下

  ```shell
  tar -zxvf a.tar.gz -C 指定目录
  ```

  