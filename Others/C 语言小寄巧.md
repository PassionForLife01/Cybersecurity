## 生成随机数

```
#include<stdlib.h> -- rand(), srand()
#include<time.h>   -- time(NULL)
```

```c
srand((unsigned)time(NULL));
const int n = 9;
int a[n];

for (int i = 0; i < n; i++)
	a[i] = rand() % 10;
```

## 传二维数组
`main()`
```c
const int m = 3;
b[m][m];
```

`func()`
```c
void func(int (*b)[3]);
```

不能用 `int **b`，会出错。