# AWK
参考：[AWK 简明教程](https://coolshell.cn/articles/9070.html)、[AWK程序设计](https://awk.readthedocs.io/en/latest/index.html)、[AWK](https://aicode.cc/san-shi-fen-zhong-xue-huiawk.html)、[Awk Tutorial](https://www.tutorialspoint.com/awk/index.htm)

## 数据准备
##### netstat.txt
```
Proto Recv-Q Send-Q  Local-Address          Foreign-Address        (state)
tcp4       0      0  bogon.55008            117.122.217.19.https   ESTABLISHED
tcp4       0      0  bogon.54946            114.118.16.159.https   ESTABLISHED
tcp4       0      0  bogon.54945            101.201.173.208.https  ESTABLISHED
tcp4       0      0  bogon.54943            119.39.146.129.https   ESTABLISHED
tcp4       0      0  bogon.54874            119.39.204.33.https    ESTABLISHED
tcp4       0      0  bogon.54873            ti-in-f138.1e100.https SYN_SENT
tcp4       0      0  bogon.54868            ti-in-f138.1e100.https SYN_SENT
tcp4      37      0  bogon.53911            111.206.37.70.https    CLOSE_WAIT
```

#### score.txt
```
Troye           21342 88 99 100
Lennon          23134 77 66 78
Kikoxxxi        14010 99 88 98
Tom             12943 39 23 54
Eric            38947 34 23 55
Paul            89329 54 78 85
```

## 格式化输出
- `awk '{printf "%-8s %-22s\n",$1,$5}' netstat.txt`
```
$ awk '{printf "%-8s %-22s\n",$1,$5}' netstat.txt
Proto    Foreign-Address
tcp4     117.122.217.19.https
tcp4     114.118.16.159.https
tcp4     101.201.173.208.https
tcp4     119.39.146.129.https
tcp4     119.39.204.33.https
tcp4     ti-in-f138.1e100.https
tcp4     ti-in-f138.1e100.https
tcp4     111.206.37.70.https
```

## 过滤记录
- `awk '$2==0 && $6=="ESTABLISHED" || NR==1 {printf "%-15s %-25s %s\n",$4,$5,$6}' netstat.txt`
```
$ awk '$2==0 && $6=="ESTABLISHED" || NR==1 {printf "%-15s %-25s %s\n",$4,$5,$6}' netstat.txt
Local-Address   Foreign-Address           (state)
bogon.55008     117.122.217.19.https      ESTABLISHED
bogon.54946     114.118.16.159.https      ESTABLISHED
bogon.54945     101.201.173.208.https     ESTABLISHED
bogon.54943     119.39.146.129.https      ESTABLISHED
bogon.54874     119.39.204.33.https       ESTABLISHED
```

## 内建变量

变量 | 说明
:---:|:---:
$0  | 当前记录（这个变量中存放着整个行的内容）
$1~$n | 当前记录的第n个字段，字段间由FS分隔
FS/OFS  | 输入/输出字段分隔符 默认是空格（或Tab）
RS/ORS  | 输入/输出的记录分隔符， 默认为换行符
NF  | 当前记录中的字段个数，就是有多少列
NR  | 当前记录数，就是行号，从1开始，如果有多个文件话，这个值也是不断累加中。
FNR | 当前记录数，与NR不同的是这个值是各个文件自己的行号

## 指定输入/输出分隔符
- `awk -F. '{print $2,$4,$5}' netstat.txt`
- `awk -F'[.]' '{print $2,$4,$5}' netstat.txt`
- `awk -F. '{print $2,$4,$5}' OFS="\t" netstat.txt`
```
$ awk -F. '{print $2,$4,$5}' netstat.txt
55008            117 217 19
54946            114 16 159
54945            101 173 208
54943            119 146 129
54874            119 204 33
54873            ti-in-f138 https SYN_SENT
54868            ti-in-f138 https SYN_SENT
53911            111 37 70
```

## 字符串匹配
- `awk '$6 ~/SYN/ || NR==1 {print NR,$4,$5,$6}' OFS="\t" netstat.txt`
- `awk '/SYN/||NR==1' netstat.txt`
- 取反：`awk '$6 !~/SYN/||NR==1{print NR,$4,$5,$6}' OFS="\t" netstat.txt`
```
$ awk '$6 ~/SYN/ || NR==1 {print NR,$4,$5,$6}' OFS="\t" netstat.txt
1   Local-Address   Foreign-Address (state)
7   bogon.54873 ti-in-f138.1e100.https  SYN_SENT
8   bogon.54868 ti-in-f138.1e100.https  SYN_SENT
```

## 拆分文件
- `awk 'NR!=1{print > $6}' netstat.txt`
- `awk 'NR!=1{print $4,$5 >$6}' netstat.txt`
- `awk 'NR!=1{if($6 ~/ESTABLISHED|SYN/) print > "1.txt"; else print > "2.txt"}' netstat.txt`

## 统计
- 计算.txt文件大小总和：`ls -l *.txt | awk '{sum+=$5} END {print sum}'`
- 计算每个用户的进程所占内存：`ps aux | awk 'NR!=1 {a[$1]+=$6} END{for(i in a) print i ", " a[i] "KB"}'`
```
$ ps aux | awk 'NR!=1 {a[$1]+=$6} END{for(i in a) print i ", " a[i] "KB"}'
root, 661716KB
hades, 5453276KB
```
- `awk 'NR!=1 {a[$6]+=1} END{for(i in a) print i ", " a[i]}' netstat.txt`
```
$ awk 'NR!=1 {a[$6]+=1} END{for(i in a) print i ", " a[i]}' netstat.txt
SYN_SENT, 2
CLOSE_WAIT, 1
ESTABLISHED, 5
```

## 脚本

#### cal.awk
```
#!/bin/awk -f
BEGIN {
    printf "NAME     No.      MATH  ENGLISH  COMPUTER   TOTAL\n"
    printf "----------------------------------------------------\n"
}
{
    math+=$3
    english+=$4
    computer+=$5
    printf "%-8s %-6s %4d %8d %8d %8d\n", $1,$2,$3,$4,$5,$3+$4+$5
}
END {
    printf "----------------------------------------------------\n"
    printf "TOTAL: %14d %8d %8d \n",math,english,computer
    printf "AVERAGE: %14.2f %8.2f %8.2f\n",math/NR,english/NR,computer/NR
}
```
- `awk -f cal.awk score.txt`
```
$ awk -f cal.awk score.txt
NAME     No.      MATH  ENGLISH  COMPUTER   TOTAL
----------------------------------------------------
Troye    21342    88       99      100      287
Lennon   23134    77       66       78      221
Kikoxxxi 14010    99       88       98      285
Tom      12943    39       23       54      116
Eric     38947    34       23       55      112
Paul     89329    54       78       85      217
----------------------------------------------------
TOTAL:            391      377      470
AVERAGE:          65.17    62.83    78.33
```