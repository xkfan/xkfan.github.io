# AWK

data.txt

```bash
cat data.txt
```

```
1
2
3
```

## 1 Calculate sum

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

## 2 Calculate average

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

## ３ Calculate variance

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
