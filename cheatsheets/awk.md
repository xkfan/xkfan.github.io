# AWK


## 1 Some basics

```bash
awk '{print $5}' file              # 打印文件中以空格分隔的第五列
awk -F ',' '{print $5}' file       # 打印文件中以逗号分隔的第五列
awk '/str/ {print $2}' file        # 打印文件中包含 str 的所有行的第二列
awk -F ',' '{print $NF}' file      # 打印逗号分隔的文件中的每行最后一列
awk '{s+=$1} END {print s}' file   # 计算所有第一列的合
awk 'NR%3==1' file                 # 从第一行开始，每隔三行打印一行
```

## 2 Improved

data.txt

```bash
cat data.txt
```

```
1
2
3
```

### 2.1 Calculate sum

```bash
cat sum.sh
```

```bash
#!/bin/bash
cat data.txt | awk 'BEGIN{sum=0;} {sum+=$1;} END{print sum;}'
```

```bash
./sum.sh
```

```
6
```

### 2.2 Calculate average

```bash
cat average.sh
```

```bash
#!/bin/bash
cat data.txt | awk 'BEGIN{sum=0;} {sum+=$1;} END{print sum/NR;}'
```

```bash
./average.sh
```

```
2
```

### 2.３ Calculate variance

```bash
cat variance.sh
```

```bash
#!/bin/bash
cat data.txt | awk 'BEGIN {n=0; sum=0; ss=0;} \
                    {x[NR]=$1; n++; sum+=$1;} \
                    END { \
                      average=sum/NR; \
                      for (i=1; i<=n; ++i) \
                        {ss+=(x[i]-average)^2;} \
                      print ss/NR; \
                    }'
```

```bash
./variance.sh
```

```
0.666667
```

### 2.4 Calculate geomean

```bash
cat geomean.sh
```

```bash
#!/bin/bash
cat data.txt | awk 'BEGIN {prod = 1;} \
                    {prod *= $1;} \
                    END {print prod ^ (1/NR);}'
```

```bash
./geomean.sh
```

```
1.81712
```
