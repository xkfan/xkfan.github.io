# String manipulation

## 1 Removing Matched Substring

```bash
filename="prefix.name.suffix"
```

### 1.1 Remove suffix
'%' matches the shortest substring from the end
```bash
echo ${filename%.*}
prefix.name
```

### 1.2 Remove long suffix
'%%' matches the longest substring from the end
```bash
echo ${filename%%.*}
prefix
```

### 1.3 Remove prefix
'#' matches the shortest substring from the beginning
```bash
echo ${filename#*.}
name.suffix
```

### 1.4 Remove long prefix
'##' matches the longest substring from the beginning
```bash
echo ${filename##*.}
suffix
```
