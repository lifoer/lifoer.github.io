---
title: Linux相关
date: 2018-12-8 18:44:17
tags: linux
toc: true
---

linux相关的一些记录


<!--more-->


1.压缩相关

1> tar.gz
解压：
```
tar -zxvf file.tar.gz
```

压缩：
```
tar -zcvf file.tar.gz file1 file2
```

2> tar
解压：
```
tar -xvf flie.tar
```

压缩：
```
tar -cvf file.tar file1 file2
```

3> zip:
解压：
```
unzip file.zip
```

压缩：
```
zip -r file.zip file
```
zip [参数] [压缩后的文件名] [被压缩文件名] 

4> tar.bz2
解压：
```
tar -jxvf file.tar.bz2
```

压缩：
```
tar -jcvf file.tar.bz2 file1 file2
```

2.centos中文乱码

```
vim /etc/locale.conf
LANG=“zh_CN.UTF-8”
```

3.ssh连接自动断开
timed out waiting for input: auto-logout
Connection closing...Socket close.

```
vim /etc/profile
#export TMOUT=300
export TMOUT=0
```

4.软连接建立

```
ln -s /root/anaconda3/envs/py36/bin/python3.6 python
```
ln –s 源文件 目标文件

5.硬连接建立

```
ln /root/anaconda3/envs/py36/bin/python3.6 python
```

6.linux删除文本中^M

I.^M引起原因：“\r”回车符

II.查看^M

```
cat -A file.txt
```

III.删除^M，^M不可复制，手动按出：ctrl+v+m

```
cat file.txt | tr -d "^M" > newfile.txt
```

IIII.批量处理

* 1). shell批量删除

```
#!/bin/bash
for file in `ls | grep .txt`
do
    newfile=`echo $file | sed "s/\([a-zA-Z0-9]\+\)\(.txt\)/\1-f\2/"`
    cat $file | tr -d "^M" > $newfile
done
```

* 2).批量验证

```
#!/bin/bash
for file in `ls | grep .txt`
do 
    printf "\n%s\n" $file
    cat -A $file
done
```

3).java批量删除

```java
public class Test {
    public static void main(String[] args) {
        File path = new File("d:\\test");
        File[] files = path.listFiles();
        for (File f : files) {
            if (f.isFile() && f.getName().endsWith(".txt")) {
                String enter = "\n";
                String line;
                StringBuilder sb = new StringBuilder();
                String fPath = f.getAbsolutePath();
                File newF = new File(fPath.substring(0, fPath.lastIndexOf(f.getName())) + "bak" + File.separator + f.getName());
                try (BufferedReader br = new BufferedReader(new FileReader(f));
                     BufferedWriter bw = new BufferedWriter(new FileWriter(newF))) {
                    while ((line = br.readLine()) != null) {
                        sb.append(line);
                        sb.append(enter);
                    }
                    bw.write(sb.substring(0, sb.lastIndexOf("\n")));
                    bw.flush();
                } catch (IOException e) {
                    System.out.println(f.getName());
                    e.printStackTrace();
                }

            }
        }
        System.out.println("finished.");
    }
}
```





