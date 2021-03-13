# C语言程序设计

## printf函数

头文件：stdio.h

函数原型：

```c
int printf(const char *format,....)
```

format:格式控制字符串

...    ：可变参数列表

返回值：输出字符的数量，即成功打印字符的个数，\n换行符也算

## scanf函数

头文件：stdio.h

原型：

```c
 int scanf(const char* format,....);
```

...   :可变参数列表

返回值：成功读入参数的个数

0个是合法的 例如：scanf("abc");

EOF(end of file) EOF=-1表示读取数据到换行

```c
while（scanf("%d",&n)!=EOF){ }
```

[^/n] :异或/n ，，getchar() 强行吞字符

gcc编译执行程序：预处理、编译、汇编、链接



### 随堂练习：循环读一个数字，返回其位数

```c
#include<stdio.h>
int main(){
    int n;
    while(scanf("%d",&n)!=EOF){
        printf(" has %d digits\n",printf("%d",n));
    }
    return 0;
}

```

标准输入输出

**scanf** :标准输入stdin ，读数据直到看到空白符（空格符，制表符，回车符）

**printf**:标准输出stdout,

**stderr**:标准错误输出

格式占位符：%4d,占4个位置，不足4位补空格，%04d 占4个位置，不足4位，补零。

%lf :double类型输出。%g： 自动保留位数。

scanf("%lf\n",&n)  : 输入数字回车，在输入。返回缓冲区的第一个值，一般输入后面不加"\n"。

推荐网站：c++reference

sprintf(str,...): 字符串拼接。

fprintf():  向文件中输出，

```c
int main(){
    double n;
    scanf("%lf",&n);//stdin
    printf("%g\n",n);//stdout
    char str[1000]={0};
    sprintf(str,"%d.%d.%d.%d",192,168,0,1);//192.168.0.1
    printf("str=%s\n",str);
	FILE *fout=fopen("output","w”);//输出到文件output中
 	fprintf(fout,"str=%s",p);
 	fprintf(stdout,"stdout=%s\n",p);
 	fprintf(stderr,"stderr=%s\n",p);
  }
```

输出重定向： ./a.out > output 。标准输出重定向

## switch 语句

```c
#include<stdio.h>
int main(){
    char s;
    scanf("%c",&s);
    switch(s){
        case 'h':
            printf("He\n");
            break;
        case 'l':
            printf("Li\n");
            break;
        case 'c':
            printf("Cao\n");
            break;
        case 'd':
            printf("Duan\n");
            break;
        case('w'):
            printf("Wang\n");
            break;
        default:
            printf("Not Here\n");
            break;
    }
    return 0;
}

```



## 判断回文整数

```c
bool isPalindrome(int x){
    //下面这句等价于if(x<0),但是利用内置宏可以提高运算速度，减少ifelse分支消耗时间
    if(__builtin__expect(!!(x<0),0)){
        return false;
    }
    int y=0,z=x;
    while(x){
        y=y*10+x%10;
        x=x/10;
    }
    return z==y;
}
```

```c
//x经常成立，加载条件分支内部的代码
#define likely(x)  __builtin__expect(!!(x),1)
//x不经常成立，加载条件分支外部代码
#define unlikely(x)  __builtin__expect(!!(x),0)

```

## 辗转相除法---欧几里得算法

```c
#include<stdio.h>
int gcd(int a,int b){
    //递归，当b=0时，返回a,b不为零时，求b和a%b最大公因子。
    return (b?gcd(b,a%b):a);
}
int main(){
    int a,b;
    while(~scanf("%d%d",&a,&b)){
        printf("%d\n",gcd(a,b));
    }
    return 0;
}

```

#### 扩展欧几里得算法

用途：用于求解ax+by=gcd(a,b)的整数解

算法思路：利用数学归纳法的思想：

当b=0时，gcd(a,b)=a;此时解为x=1，y为任意值，这也是递归终止的条件。

递归过程中的第ki轮，是求（b,a%b) 时的xx和yy值
$$
bxx+(a\%b)yy=1     即 bx+(a-a/b*b)*yy=1 \\
b(xx-a/b*yy)+ayy=1
$$
**（注意在递归算法中，永远都是先得到下面一个状态的值）**

那么第ki-1轮是（a,b)时对应的x和y的值
$$
ax+by=1\\
$$
对比与第ki轮的公式 有：
$$
x=yy\\
y=xx-a/b*yy
$$


```c
#include<stdio.h>
//扩展欧几里得算法
int ex_gcd(int a, int b,int *x,int *y){
    if(b==0){
        *x=1;
        *y=0;
        return a;//到达边界开始向上一层返回
    }
    //xx.yy为上一层的值，
    int xx,yy,ret=ex_gcd(b,a%b,&xx,&yy);
    *x=yy;
    *y=xx-a/b*yy;
    return ret;
}

int main(){
    int a,b,x,y;
    while(~scanf("%d%d",&a,&b)){
        printf("gcd(%d,%d)=%d\n",a,b,ex_gcd(a,b,&x,&y));
        printf("%d*%d+%d*%d=%d\n",a,x,b,y,a*x+b*y);
    }
    return 0;
}

```

## 判断是否是质数

### 法一：最简单，时间复杂度O(N)

```c
int is_prime(int n){
    for(int i=2;i<n;i++){
        if(n%i==0){
            return 0;
            break;
        }
    }
    return 1;
}
```

### 法二：时间复杂度 O（sqrt(n))

```c
int is_prime(int n){
//小于等于SQRT(N),,,注意还有等号
	for(int i=2;i<=sqrt(n);i++){
		if(n%i==0){
			return 0;
			break;
		}
	}
	return 1;
}
```

### 法三：时间复杂度O(sqrt(n)/3)

证明：令x≥1，将大于等于5的自然数表示如下：

··· 6x-1，6x，6x+1，6x+2，6x+3，6x+4，6x+5，6(x+1），6(x+1)+1 ···

可以看到，不和6的倍数相邻的数为6x+2，6x+3，6x+4，由于2(3x+1)，3(2x+1)，2(3x+2)，所以它们一定不是素数，再除去6x本身，显然，素数要出现只可能出现在6x的相邻两侧。因此在5到sqrt(n)中每6个数只判断2个，时间复杂度O(sqrt(n)/3)

```c
//返回1表示判断为质数，0为非质数，在此没有进行输入异常检测
int isPrime(int n)
{	
	float n_sqrt;
	if(n==2 || n==3) return 1;
	if(n%6!=1 && n%6!=5) return 0;
	n_sqrt=floor(sqrt((float)n));
	for(int i=5;i<=n_sqrt;i+=6)
	{
	    if(n%(i)==0 | n%(i+2)==0) return 0;
	}
        return 1;
} 
```



## 素数筛算法

算法思想：

1、标记一个范围内的数字是否是合数,1的为合数,0为素数。

2、算法的空间复杂度为O(N),时间复杂度为O(N*loglogN)

3、总体思想是用素数去标记点不是素数的数字，例如知道了i是素数，则2*1，3*i不是素数。

```c
#include<stdio.h>
#define MAX 100
int prime[MAX+5]={0};
//定义cnt统计素数个数
int cnt=0;
void init()
{
    for(int i=2;i<=MAX;i++){
        if(prime[i]) continue;
        //prime[cnt]=i;
        //cnt+=1;
        //素数塞后置加一
        prime[++prime[0]]=i;
        //优化从i*i开始查找
        for(int j=i*i;j<=MAX;j+=i){
            prime[j]=1;
        }
    }

}
int main(){
    init();
   /* for(int i=0;i<cnt;i++){
          printf("%d\n",prime[i]);}*/
    for(int i=1;i<=prime[0];i++){
        printf("%d\n",prime[i]);
    }
    return 0;
}


```

随堂练习：by listing the first six prime 2,3,5,7

### 随堂练习1--求一个范围内所有数字的最小素因子

```c
#include<stdio.h>
#define MAX_N 100
int prime[MAX_N+5]={0};
void init(){
    for(int i=2;i<=MAX_N;i++){
        if(prime[i]) continue;
        for(int j=i; j <= MAX_N;j+=i){
            //如果被标记过 跳出此次循环，进入下次
            if(prime[j]) continue;
            prime[j]=i;
        }
    }
    return ;
}
int main(){
    init();
    for(int i=2;i<=MAX_N;i++){
        printf("min factor(%d)=%d\n",i,prime[i]);
    }
    return 0;
}

```

### 随堂练习2--求一个范围内所有数字的最大素因子

```c
#include<stdio.h>
#define MAX_N 100
int prime[MAX_N+5]={0};
void init(){
    for(int i=2;i<=MAX_N;i++){
        if(prime[i]) continue;
        for(int j=i; j <= MAX_N;j+=i){
        //找到最后一个被标记的素因子
            prime[j]=i;
        }
    }
    return ;
}
int main(){
    init();
    for(int i=2;i<=MAX_N;i++){
        printf("max_factor(%d)=%d\n",i,prime[i]);
    }
    return 0;
}

```

随堂练习：

```c
#include<stdio.h>
#include<stdio.h>                         
#define MAX_N 1000  
int prime[MAX_N+5]={1,1,0};
//printf("%d\n",prime[0]);
void init(){
        for(int i=2;i<=MAX_N;i++){
        if(prime[i]) continue;
        for(int j=i*i;j<=MAX_N;j+=i){
            prime[j]=1;   
        }
    }
}
int prime_sum(int first,int second){
    init();
    int sum=0;
    for(int i=first;i<=second;i++){
        if(prime[i]) continue;
        sum+=i;
    }
    return sum;
}

int main(){
    int start_num,end_num;
    scanf("%d%d",&start_num,&end_num);
    int prime_sums=prime_sum(start_num,end_num);
    printf("%d\n",prime_sums);
    return 0;
}

```



### 线性筛

1. 标记一个范围内的数字是否是合数，没被标记的则为素数

2. 算法的空间复杂度为O(N)，时间复杂度为O(N)

3. 总体思想是用一个整数M去标记合数N，其中N和M具有如下性质：

   > (1).N中最小的素数为p
   >
   > (2).N可以表示为p*M
   >
   > (3).p一定小于等于M中的最小素因子
   >
   > (4).利用M*p' (所有不大于M中最小素数的集合)标记为N1,N2,N3.......

**练习：**

> 若N=30,则算法中的P=2,M=15
>
> 若N=8，则算法中的P=2,M=4
>
> 若N=45，则算法中的p=3,M=15
>
> 若M=4，则算法标记的N=4*2=8
>
> 若M=25，则算法标记的N=25X2=50,25X3=75,25X5=125
>
> 若M=45，则算法标记的N=45*2=90，45x3=135.
>
> 能标记90的M为45（45x2)

```c
//线性筛的代码演示
#include<stdio.h>
#define MAX_N 100
int prime[MAX_N+5]={0};
void init(){
    for(int i=2;i <= MAX_N;i++){
        //将所有素数存在prime数组中，i即为上面的M
         if(!prime[i]) prime[++prime[0]]=i;
        //j即为上面的p
         for(int j=1;j<prime[0];j++){
            if(i*prime[j]>MAX_N) break;
             prime[prime[j]*i]=1;
         //每个合数只会被它的最小素因子筛掉，就把复杂度降到了O(N)。
             //*****核心代码****
            if(i%prime[j]==0) break;
        }
    }
    return ;
}
int main(){
    init();
    for(int i=1 ; i < prime[0];i++){
        printf("%d\n",prime[i]);
    }
    return 0;

}
```

**素数筛和线性筛的不同：**

一般的素数筛存在重复筛选，比如6既可以倍2筛掉，又可以被3筛掉

线性筛是按照一个数的最小素因子筛去，没有重复筛掉同一个数



## 二分查找

```c
int binary_research(int *arr,int n,int x){
	int head=0;tail=n-1;mid=0;
	while(head<=tail){
	mid=(head+tail)/2;
	if(arr[mid]==x) {
		return mid;
	}else if(arr[mid]>x){
		tail=mid-1;
	}else {
		head=mid+1;
	}
  }
	return -1;
}
```

## 牛顿迭代法：

```c
//求解x*x-n=0的根。
#include<stdio.h>
#include<math.h>
double F(double x,double n){
    return x*x-n;
}
double f(double x){
    return 2*x;
}
double Newton(double(*F)(double,double),double(*f)(double),double n){
    double x=n/2.0;
    #define EPSL 1e-6
    while(fabs(F(x,n))>EPSL){
        x-=F(x,n)/f(x);
    }
    #undef EPSL
    return x;
}

double my_sqrt(double n){
    return Newton(F,f,n);
}
int main(){
    double n;
    while(~scanf("%lf",&n)){
        printf("%g\n",my_sqrt(n));
    }
    return 0;
}
```

数据预处理命令-宏定义

## 数组代码演示

```c
//二维数组传参
int arr[100][200][300];
void func(int (*a)[200][300]);
```

## 预处理命令-宏定义

```c
//简单的符号替换,只能在一行，定义代码段时加连接符"\"
//定义符号常量
#define PI 3.1415926
#define MAX_N 100000
//定义傻瓜表达式
#define MAX(a,b) (a)>(b)?(a):(b)
#define S(a,b) a*b
//定义代码段
#define p(a){\
	printf("%d\n",a);\
}
```

### 预定义的宏

![image-20201011095542782](E:\Study_Notes\预处理的宏)



没有bug的MAX宏

```c
#include<stdio.h>
//代码演示
//__typeof作用：
#define MAX(a,b)({  \
    __typeof(a) _a=a;\
    __typeof(b) _b=b;\
    _a>_b? _a:_b;\
 })
//#的作用：

#define P(a){\
    printf("%s=%d\n",#a,a);\
}
int main(){
    int a=7;
    P(MAX(2,3));
    P(5+(MAX(2,3)));
    P(MAX(2,MAX(3,4)));
    P(MAX(2,3>4?3:4));
    P(MAX(a++,6));
    P(a);
    return 0;

}
```

随堂练习：

实现一个打印LOG函数，需要输出所在函数及函数等信息

注：

> \_\_FILE__以字符串形式返回所在文件名称
>
> \_\_func__以字符串形式返回所在函数名称
>
> \_\_LINE__以整数形式返回代码行号

```c
#include<stdio.h>
#ifdef DEBUG
#define log(frm, argc...){\
    printf("[%s:%d]",__func__,__LINE__);\
    printf(frm,##argc);\
    printf("\n");\
}
#else
#define log(frm,argc...);
#endif
#define contact(a,b) a##b
void func(int a){
    a+=1;
    log("%d",a);
    return ;
}
int main(){
    int a=123,abcdef=0;
    func(a);
    log("%d",a);
    log("hello world");
    contact(abc,def)=24;
    log("%d",abcdef);
    return 0;
}
```

## 字符串

定义字符串数组：char str[size];

初始化字符数组：

```c
char str[ ]="hello world";

char str[size]={'h','e','l','l','o'}
```

'\0'对应十进制的值为0，标记字符串末尾

‘0’对应十进制的值为48

> ”hello world" 
>
> strlen:字符串长度去找‘\0’,字符串长度为11，
>
> sizeof: 存储字符串大小为12，包含末尾的‘\0'.

### 字符串的相关操作

![image-20201011112712950](E:\Study_Notes\字符串相关操作)

> memset :按字节设置memset(arr ,0 ,sizeof(arr))
>
> memset(arr ,1, sizeof(arr))错误
>
> memset(arr ,-1 sizeof(arr))正确

```c
//头文件：stdio.h
sscanf(str1,format,...)  :从字符串str1读入内容

sprintf(str1,format,...):将内容输出到str1中
```

随堂练习-4：

请使用字符串相关操作，计算一个整型16进制表示的位数。

```c
#include<stdio.h>
#include<string.h>
int main(){
    int n;
    char str[12];
    while(~scanf("%d",&n)){
        sprintf(str,"%X",n);
        printf("%s has %lu digits!",str,strlen(str));
    }
    return 0;
}

```

## 结构体

```c
struct person{
    char name[20];//性别
    int age;//年龄
    char gender;//性别
    float height;//身高
};
//占用空间大小20+4+1+4=29个字节
//空间对齐：实际上有32个字节，int占用空间最大为4，申请为4的整数倍
struct node1{
    char a;
    char b;
    int c;
};//8个字节
struct node2{
    char a;
    int c;
    char b;
};//12个字节
//宏强行改变结构体的对齐方式。
```

## 共用体：union

大端机：数字低位放在高地址位

小端机：数字低位挡在低地址位

主机字节序：

网络字节序：

在IP地址结构中存放地址总是以（大端法）网络字节序存放的。

## 指针和变量

int *p;int 类型指针占4个字节，double类型指针占8个字节，int类型（p+1）跳4个字节，double类型（p+1）占8个字节。

指针变量也是变量，

指针变量64位系统占用8个字节，32位操作系统占用4个字节。

等价形式转化

*p=a（原始变量）;

p+1==&p[1]

p->filed ==(*p).filed ==a.filed

```c
struct data{
	int x,y;
};
struct data a[2],*p=a;
//表示a[1].x,
*(p+1).x;
*(a+1).x;
(p+1)->x;

```

### 函数指针和main函数

#### 函数指针

```c
变量：int （*add)(int,int );
//typedef 类型重定义，将变量提升至类型
函数指针类型：typedef int (*add)(int ,int );
```

#### typedef的用法

内建类型重命名：

```c
typedef long long lint;
typedef char* pchar;
```

结构体类型重命名：

```c
typedef struct __node{
	int x,y;
}Node,*PNode;

```

函数指针命名：

```c
typedef int (*func)(int)
```

#### main 函数参数

```c
int main();
//argc:传进来参数个数，argv传进来的参数字符串
int main(int argc,char *argv[]);
//**env二维数组，环境变量
int main(int argc,char *argv[],char **env);
```

#### 代码演示：

```c
#include<stdio.h>
#define offset(T,a)(long)
int main(){
    
}
```



头文件与静态链接库

添加到系统路径 -I

g++  -I./

所有文件：module

头文件：include

源文件：src

源文件生成对象文件打包：.lib

> 举一个例子:
>
> module文件夹包含include、src、lib三个文件夹 ，并列目录下有一个test.cpp

> include：header1.h   header2.h  header3.h  
>
> src: header1.cc    header2.cc   header3 .cc
>
> bin: 最终可执行文件

> ```shell
> g++ -I../include -c header1.cc//生成.o文件
> g++ -I../include -c header2.cc
> g++ -I../include -c header3.cc
> 
> ar -r libhaizei.a header1.o header2.o header3.o
> mv libhaizei.a ../lib/
> 
> g++ -I./module/include -c test.cpp
> g++ test.o -L./module/lib -lhaizei
> ./a.out
> ```





## makefile讲解

多文件编译

```shell
touch makefile  //   module/makefile
vim makefile
//makefile内容：
.PHONY clean //虚拟空间
all:header1.o header2.o header3.o test.o
	g++ header1.o header2.o header3.o test.o
header1.o: ./src/header1.cc
	g++ -I./include -c src/header1.cc
header2.o: ./src/header2.cc
	g++ -I./include -c src/header2.cc
header3.o: ./src/header3.cc
	g++ -I./include -c src/header3.cc
test.o:test.cpp
	g++ -I./include -c test.cpp
clean:
	rm -rf *.o a.out

```



## gtest框架

单元测试框架：

```c
//main.cpp
#include<stdio.h>
#include<gtest/gtest.h>

int add(int a ,int b){
	return a+b;
}

//TEST为宏，不是函数，因为没有返回值，参数列表没有参数类型
//EXPECT_EQ断言，单元测试,宏或者函数
TEST(func,add){
	EXPECT_EQ(add(3,4),7);
	EXPECT_EQ(add(3,2),5);
	EXPECT_EQ(add(1,2),3);
	EXPECT_EQ(add(1,2),4);
}

int main(){
	testing::InitGoogleTest(&argc,argv);
    //RUN_ALL_TESTS函数
	return RUN_ALL_TESTS();
}
```

```c
//test.h
#ifdef _TEST_H
#define _TEST_H
#define TEST(a,b) \
//后面函数优于主函数执行，改变函数运行的优先级
__attribute__((constructor))\
void a##b()
#define EXPECT_EQ(a,b) printf("%s==%s ?%s\n",#a,#b,(a)==(b)?"True":"False");
int RUN_ALL_TESTS();
#endif 
```

```
//test.cc
int RUN_ALL_TESTS(){
	return 0;
}
```

```shell
//makefile
.PHONY:clean
all main.o haizei/test.o haizei/test.h
	g++ -I./ main.o haizei/test.o -o ./bin/haizei
main.o:main.cpp haizei/test.h
	g++ -I./ -c main.cpp
haizei/test.o: haizei/test.cc haizei/test.h
	g++ -I./ -c haizei/test.cc -o haizei/test.o
clean:
	rm -ef bin/haizei main.o haizei/test.o
```

### 自制Gtest简易框架

```c
//haizei/test.h
#ifndef _TEST_H
#define _TEST_H
#define TEST(a,b)\
void a##__haizei__##b();\
__attribute__((constructor))\
void add##__haizei__##a##__haizei__##b(){\
    add_function(a##__haizei__##b,#a "__haizei__" #b);}\
void a##__haizei__##b()

#define CORLOR(a,b) "\033[1;" #b "m" a "\033[0m"

#define Green(a) CORLOR(a,32)
#define Red(a) CORLOR(a,31)
#define Bule(a) CORLOR(a,34)
#define Yellow(a) CORLOR(a,33)

//#define EXPECT_EQ(a,b) printf("%s==%s ? %s\n",#a,#b,(a)==(b)?Green("True"):Red("False"))
//泛型宏
#define TYPE(a) _Generic((a),\
    int : "%d",\
    double: "%lf",\
    float: "%f",\
    long long : "%lld"\
)

#define P(a,color){\
    char frm[1000];\
    sprintf(frm,color("%s"),TYPE(a));\
    printf(frm,a);\
}

#define EXPECT(a,b,comp){\
    haizei_test_info.total+=1;\
    if( a comp b ){\
    haizei_test_info.success+=1;\
    }else{\
        printf("\n");\
        printf(Yellow( "\t%s:%d: failed\n"),__FILE__,__LINE__);\
        haizei_test_info.failed+=1;\
        printf(Yellow("\t\texpect: " #a " "#comp " " #b "\n\t\t" "actual: "));\
        P(a,Yellow);\
        printf(Yellow(" VS "));\
        P(b,Yellow);\
        printf("\n");\
    }\
    printf(Green("[---------]") " " Yellow(#a #comp #b) " ");\
    printf(" %s\n",(a)comp(b)? Green(" True"):Red(" False"));\
}
    
#define EXPECT_EQ(a,b) EXPECT(a,b,==)
#define EXPECT_NE(a,b) EXPECT(a,b,!=)
#define EXPECT_GT(a,b) EXPECT(a,b,>)
#define EXPECT_LT(a,b) EXPECT(a,b,<)
#define EXPECT_LE(a,b) EXPECT(a,b,<=)
#define EXPECT_GE(a,b) EXPECT(a,b,>=)

//定义函数指针
typedef void (*TestFunc)();
typedef struct Function{
    TestFunc func;
    const char *str;
}Function;
struct FunctionInfo{
    int total,success,failed;
};
extern struct FunctionInfo haizei_test_info;
int RUN_ALL_TESTS();
void add_function(TestFunc,const char*);
#endif

```

```c
//haizei/test.c
#include<stdio.h>
#include<string.h>
#include<haizei/test.h>
Function func_arr[100];
struct FunctionInfo haizei_test_info;
int func_cnt=0;
int RUN_ALL_TESTS(){
    for(int i=0;i<func_cnt;i++){
        printf(Green("[===RUN===]") Red (" %s") "\n",func_arr[i].str);
        haizei_test_info.total=0;
        haizei_test_info.success=0;
        haizei_test_info.failed=0;
        func_arr[i].func();
        printf(Green("[ ") "%6.2lf%%" Green(" ]"),100.0*haizei_test_info.success/haizei_test_info.total);
        printf(Bule(" Total: ") "%d " Green("Success:") "%d "  Red("Failed: ")"%d\n",
        haizei_test_info.total,
        haizei_test_info.success,
        haizei_test_info.failed);
    }
    return 0;
}

void add_function(TestFunc func,const char*str){
    func_arr[func_cnt].func=func;
    func_arr[func_cnt].str=strdup(str);
    func_cnt++;
    return ;
}

```

```c
//main.c
#include<stdio.h>
#include<haizei/test.h>

int add(int a,int b){
    return a+b;
}
TEST(testfunc,add){
    EXPECT_EQ(add(3,4),7);
    EXPECT_NE(add(1,2),5);
    EXPECT_EQ(add(3,2),5);
    EXPECT_LT(add(3,2),6);
    EXPECT_GT(add(3,3),4);
}

TEST(testfunc,add2){
    EXPECT_LT(add(3,3),7);
    EXPECT_GT(add(1,2),5);
    EXPECT_EQ(add(3,2),5);
    EXPECT_NE(add(3,3),6);
    EXPECT_EQ(add(3,1),4);
}

TEST(test,funcadd){
    EXPECT_LT(add(3,3),7);
    EXPECT_GT(add(1,2),5);
    EXPECT_LE(add(3,2),5);
    EXPECT_EQ(add(3,3),6);
    EXPECT_EQ(add(3,1),4);
}
int main(int argc,char* argv[]){
    return RUN_ALL_TESTS();
}
```

```shell
//makefile文件编写
.PHONY:clean
all:main.o haizei/test.o haizei/test.h
	gcc -I./ main.o haizei/test.o -o ./bin/haizei
main.o:main.c haizei/test.h
	gcc -I./ -c main.c
haizei/test.o:haizei/test.c haizei/test.h 
	gcc -I./ -c haizei/test.c -o haizei/test.o
clean:
	rm -rf bin/haizei main.o haizei/test.o
```

