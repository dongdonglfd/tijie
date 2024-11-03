# 西邮linux兴趣小组2022纳新题
>本题目只作为西邮 Linux 兴趣小组 2022 纳新面试的有限参考。
为节省版面，本试题的程序源码省去了#include 指令。
本试题中的程序源码仅用于考察 C 语言基础，不应当作为 C 语言「代码风格」的范例。
所有题目编译并运行于 x86_64 GNU/Linux 环境。




# 0. 我的计算器坏了？！
>2^10 = 1024对应于十进制的4位，那么2^10000对应于十进制的多少位呢?

2的10次方对应十进制的4位，计算为lg2^10+1=4；
2^10000  ：lg2^10000+1=3011
# 1. printf还能这么玩？
> 尝试着解释程序的输出。
```c
int main(void) {
    if ((3 + 2 < 2) > (3 + 2 > 2))
        printf("Welcome to Xiyou Linux Group\n");
    else
        printf("%d\n", printf("Xiyou Linux Group - 2%d", printf("")));
}
```

==考点==：printf的返回值
```c
Xiyou Linux Group - 2022
```
首先看最里面的printf里面是一个空格，先什么也不打出，然后返回0，而不是1，这是为1,这时为Xiyou Linux Group - 20,以此类推，其字符长度为22,即打印Xiyou Linux Group - 2022
# 2. 你好你好你好呀！
>程序的输出有点奇怪，请尝试解释一下程序的输出吧。
请谈谈对sizeof()及strlen()的理解吧。
```c
int main(void) {
    char p0[] = "Hello,Linux";
    char *p1 = "Hello,Linux";
    char p2[11] = "Hello,Linux";
    printf("p0 == p1: %d, strcmp(p0, p2): %d\n", p0 == p1, strcmp(p0, p2));
    printf("sizeof(p0): %zu, sizeof(p1): %zu, sizeof(*p2): %zu\n",
           sizeof(p0), sizeof(p1), sizeof(*p2));
    printf("strlen(p0): %zu, strlen(p1): %zu\n", strlen(p0), strlen(p1));
}
```
p0 == p1: %d, strcmp(p0, p2): %d\n
p0 == p1 比较的是两个指针的地址。p0是一个字符数组，其地址是数组的首地址。p1是一个指向字符串常量的指针。由于p0和p1指向不同的内存位置，所以p0 == p1的结果为0。
strcmp(p0, p2) 比较的是两个字符串的内容。因为p0和p2都包含字符串"Hello,Linux"，所以strcmp(p0, p2)的结果为0。
sizeof(p0) 计算的是数组p0的大小，包括结尾的空字符\0，所以结果是12。
sizeof(p1) 计算的是指针p1的大小，指针大小是4字节8字节（64位系统）。所以结果是8。
sizeof(*p2) 实际上计算的是p2[0]的大小，即一个char的大小，结果是1。
strlen(p0) 和 strlen(p1) 计算的都是字符串的长度，不包括结尾的空字符\0。因为两个字符串都是"Hello,Linux"，所以长度都是11。

==sizeof()==

    功能：sizeof() 是一个编译时运算符，用于获取变量或数据类型所占用的内存大小（以字节为单位）。
    返回值：对于数组，sizeof() 返回整个数组所占用的内存大小；对于指针，sizeof() 返回指针本身的大小（通常是 4 或 8 字节，取决于平台）。

==strlen()==

    功能：strlen() 是一个运行时函数，用于计算字符串的长度，直到但不包括第一个空字符 '\0'。
    返回值：返回字符串的长度（不包括终止的空字符）。


# 3. 换个变量名不行吗？
>请结合本题，分别谈谈你对C语言中「全局变量」和「局部变量」的「生命周期」理解。
```c
int a = 3;
void test() {
    int a = 1;
    a += 1;
    {
        int a = a + 1;
        printf("a = %d\n", a);
    }
    printf("a = %d\n", a);
}
int main(void) {
    test();
    printf("a= %d\n", a);
}
```

==全局变量==的生命周期
全局变量是在函数外部声明的变量，它们在整个程序运行期间都保持其值，直到程序结束。全局变量的生命周期从程序开始执行时开始，一直持续到程序结束。
==局部变量==的生命周期
局部变量是在函数内部或代码块内部声明的变量。它们的生命周期从声明点开始，到包含该变量的代码块执行完毕时结束。一旦局部变量超出其作用域，它的值就不再有效，并且该内存位置可能会被其他变量使用。

```c
int a=3;
void test() {  
    int a = 1; // 第一个局部变量a  
    a += 1;  
    {  
        int a = a + 1; // 第二个局部变量a
        printf("a = %d\n", a); // 打印第二个局部变量a的值，此时为3  
    } // 第二个局部变量a的作用域结束，它的值不再有效  
    printf("a = %d\n", a); // 打印第一个局部变量a的值，此时为2  
}
int main(void) {
    test();
    printf("a= %d\n", a);// 打印全局变量a的值，此时为3
}
```
# 4. 内存对不齐
>union与struct各有什么特点呢，你了解他们的内存分配模式吗。
```c
typedef union {
    long l;
    int i[5];
    char c;
} UNION;
typedef struct {
    int like;
    UNION coin;
    double collect;
} STRUCT;
int main(void) {
    printf("sizeof (UNION) = %zu\n", sizeof(UNION)); 
    printf("sizeof (STRUCT) = %zu\n", sizeof(STRUCT));
}
```
==考点==：结构体和联合体的对齐

union 的至少大小是其最大成员的大小，当最大成员大小不是最大对其数的整数倍时，就要对齐到最大对其数的整数倍，所以这里本应该为20，但要对齐所以为24
结构体对齐规则类似，这里为40。
# 5. Bitwise
>请使用纸笔推导出程序的输出结果。
请谈谈你对位运算的理解。
```c
int main(void) {
    unsigned char a = 4 | 7;
    a <<= 3;
    unsigned char b = 5 & 7;
    b >>= 3;
    unsigned char c = 6 ^ 7;
    c = ~c;
    unsigned short d = (a ^ c) << 3;
    signed char e = -63;
    e <<= 2;
    printf("a: %d, b: %d, c: %d, d: %d\n", a, b, c, (char)d);
    printf("e: %#x\n", e);
}
```

变量 a 的计算：
unsigned char a = 4 | 7;
二进制：4 是 0100，7 是 0111
按位或操作：0100 | 0111 = 0111，结果是 7
a <<= 3;，即左移 3 位：0111 << 3 = 111000，结果是 56（十进制）
变量 b 的计算：
unsigned char b = 5 & 7;
二进制：5 是 0101，7 是 0111
按位与操作：0101 & 0111 = 0101，结果是 5
b >>= 3;，即右移 3 位：0101 >> 3 = 0000，结果是 0
变量 c 的计算：
unsigned char c = 6 ^ 7;
二进制：6 是 0110，7 是 0111
按位异或操作：0110 ^ 0111 = 0001，结果是 1
c = ~c;，即按位取反：~0001 = 1110，结果是 14（十进制，但因为是 unsigned char，这里只保留低 8 位）
变量 d 的计算：
unsigned short d = (a ^ c) << 3;
a 是 56（00111000），c 是 14（00001110）
按位异或操作：00111000 ^ 00001110 = 00110110，结果是 54（十进制）
左移 3 位：00110110 << 3 = 11011000，结果是 216（十进制）
变量 e 的计算：
signed char e = -63;
-63 在补码表示法中是 11000001（假设使用 8 位表示）
e <<= 2;，即左移 2 位：11000001 << 2 = 110000000（注意这里已经超出了 signed char 的范围，会发生截断）
截断后结果是 10000000，即 -128

# 6. 英译汉
>请说说下面数据类型的含义，谈谈const的作用。
```c
1.char *const p。
2.char const *p。
3.const char *p。
```

==考点==：
|指针常量|常量指针|
|-------|-------|
|类型*const 指针名|const 类型*指针名|
|数值可以改变，地址不能改变|数值不可以改变，地址能改变|

3与2一样

# 7. 汉译英
>请用变量p给出下面的定义:
含有10个指向int的指针的数组。
指向含有10个int数组的指针。
含有3个「指向函数的指针」的数组，被指向的函数有1个int参数并返回int。

```c
int *p[10]//含有10个指向int的指针的数组。
int (*p)[10]//指向含有10个int数组的指针。
int(*p[3])(int)含有3个「指向函数的指针」的数组，被指向的函数有1个int参数并返回int。
```

# 8. 混乱中建立秩序
>你对排序算法了解多少呢?
请谈谈你所了解的排序算法的思想、稳定性、时间复杂度、空间复杂度。
提示：动动你的小手敲出来更好哦~


冒泡排序
```c
void bubble_sort(int arr[],int sz)
{
	int i=0;
	for(i=0;i<sz-1;i++)
	{
		int j =0;
		for(j=0;j<=sz-1-i;j++)
		{
			if(arr[j]>arr[j+1])
			{
				int tmp= arr[j];
				arr[j]= arr[j+1];
				arr[j+1]=tmp;
			}
		}
	}
}
```

插入排序
```c
void insertsort(int arr[],int length)
{
	for(int i=1;i<length;i++)
	{
		for(int j=i;j>=i&&arr[j]<arr[j-1];j--)
		{
			int temp=arr[j];
			arr[j]=arr[j-1];
			arr[j-1]=temp;
		}
	}
}
```
选择排序
```c
void selectsort(int arr[],int length)
{
	int i=0;
	for(i=0;i<length;i++)
	{
		int k=i;
		int j=0;
		for(j=i+1;j<length;j++)	
		{
			if(arr[j]<arr[k])
			k=j;
		}
		int t=arr[i];
		arr[i]=arr[k];
		arr[k]=t;
	} 
}
```

# 9. 手脑并用
>请实现ConvertAndMerge函数：
拼接输入的两个字符串，并翻转拼接后得到的新字符串中所有字母的大小写。
提示:你需要为新字符串分配空间。
```c
char* convertAndMerge(/*补全签名*/);
int main(void) {
    char words[2][20] = {"Welcome to Xiyou ", "Linux Group 2022"};
    printf("%s\n", words[0]);
    printf("%s\n", words[1]);
    char *str = convertAndMerge(words);
    printf("str = %s\n", str);
    free(str);
}
```
==考点==
以下函数定义在ctype.h头文件中
islower判断传入的字母是不是小写字母
isupper判断传入的字母是不是大写字母
toupper将小写字母转换为大写字母
tolower将大写字母转换为小写字母
```c
char* convertAndMerge(char words[][20])
{
    char* str = (char*)malloc(sizeof(char) * 40);
    strcpy(str, words[0]);
    strcat(str, words[1]);
    int len = (int)strlen(str);
    for (int i = 0; i < len; i++)
    {
        if (islower(str[i]))
        {
            str[i] = toupper(str[i]);
        }
        else if (isupper(str[i]))
        {
            str[i] = tolower(str[i]);
        }
    }
    return str;
}
```
# 10. 给你我的指针，访问我的心声
>程序的输出有点奇怪，请尝试解释一下程序的输出吧。
```c
int main(int argc, char **argv) {
    int arr[5][5];
    int a = 0;
    for (int i = 0; i < 5; i++) {
        int *temp = *(arr + i);
        for (; temp < arr[5]; temp++) *temp = a++;
    }
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            printf("%d\t", arr[i][j]);
        }
    }
}
```

假设二维数组的原型为：
```c
00000
00000
00000
00000
00000
```
```c
for (int i = 0; i < 5; i++) {
        int *temp = *(arr + i);//arr表示第一行首元素的地址，加i表示向下移动几行
        for (; temp < arr[5]; temp++) *temp = a++; //arr[5]已经超出数组范围，所以每一次给数组赋值都要把整个数组全部赋值一遍
    }
```
第一次从第一行开始，赋值全部数组，并a++，第二次从第二行开始赋值全部数组，依次类推
```c
0       1       2       3       4
25      26      27      28      29
45      46      47      48      49
60      61      62      63      64
70      71      72      73      74
```
# 11. 奇怪的参数
>你了解argc和argv吗？

直接运行程序argc的值为什么是1？
程序会出现死循环吗？
```c
#include <stdio.h>
int main(int argc, char **argv) {
    printf("argc = %d\n", argc);
    while (1) {
        argc++;
        if (argc < 0) {
            printf("%s\n", (char *)argv[0]);
            break;
        }
    }
}
```
==argc==：这是一个整数，表示传递给程序的命令行参数的数量。它至少为1，因为第一个命令行参数总是程序的名称或路径。
==argv==：这是一个字符指针数组，其中每个元素都指向一个参数字符串。argv[0] 是程序的名称，argv[1] 是第一个参数，依此类推。argv[argc] 是一个空指针（NULL），用来标示数组的结束。
当你直接运行一个程序（比如通过命令行输入程序名），没有任何额外的参数，程序仍然会接收到一个参数，即程序自身的名称或路径。因此，argc 的值为 1。
argc增加到负数时，跳出循环
```c
argc = 1
D:\c语言\linux\未命名1.exe
```

# 12. 奇怪的字符
>程序的输出有点奇怪，请尝试解释一下程序的输出吧。
```c
int main(int argc, char **argv) {
    int data1[2][3] = {{0x636c6557, 0x20656d6f, 0x58206f74},
                       {0x756f7969, 0x6e694c20, 0x00000000}};
    int data2[] = {0x47207875, 0x70756f72, 0x32303220, 0x00000a32};
    char *a = (char *)data1;
    char *b = (char *)data2;
    char buf[1024];
    strcpy(buf, a);
    strcat(buf, b);
    printf("%s \n", buf);
}
```
 strcpy(buf, a),是将a复制到buf，strcat(buf, b)是将两个字符串连接起来，最后一%s打印，这些数以小端存储，对照ASCII值依次打印
 ```c
 Welcome to Xiyou Linux Group 2022
```

# 13. 小试宏刀
>请谈谈你对#define的理解。
请尝试着解释程序的输出。
```c
#define SWAP(a, b, t) t = a; a = b; b = t
#define SQUARE(a) a *a
#define SWAPWHEN(a, b, t, cond) if (cond) SWAP(a, b, t)
int main() {
    int tmp;
    int x = 1;
    int y = 2;
    int z = 3;
    int w = 3;
    SWAP(x, y, tmp);
    printf("x = %d, y = %d, tmp = %d\n", x, y, tmp);
    if (x>y) SWAP(x, y, tmp);
    printf("x = %d, y = %d, tmp = %d\n", x, y, tmp);
    SWAPWHEN(x, y, tmp, SQUARE(1 + 2 + z++ + ++w) == 100);
    printf("x = %d, y = %d\n", x, y, tmp);
    printf("z = %d, w = %d, tmp = %d\n", z, w, tmp);
}
```

```c
x = 2, y = 1, tmp = 1
x = 1, y = 2, tmp = 2
x = 2, y = 2
z = 5, w = 5, tmp = 2
```

==考点==：define定义宏只是简单的文本替换

前两个printf，就是用swap交换值，
```c
将上面代码可以替换成下面代码
if (1 + 2 + z++ + ++w * 1 + 2 + z++ + ++w == 100)
    tmp = x;
    x = y;//以下代码都会执行
    y = tmp;//所以x = 2, y = 2
```
z与w都++两次所以变为5

# 14. GNU/Linux命令 (选做)
>你知道以下命令的含义和用法吗：
注：
嘿！你或许对Linux命令不是很熟悉，甚至你没听说过Linux。
但别担心，这是选做题，不会对你的面试产生很大的影响！
了解Linux是加分项，但不了解也不扣分哦！
ls
rm
whoami
请问你还了解哪些GNU/Linux的命令呢。





==ls==
ls：列出当前目录下的文件和子目录。
ls /path/to/directory：列出指定目录下的文件和子目录。
ls -l：以长格式列出文件和目录，包括权限、所有者、大小和修改时间等信息。
ls -a：列出包括隐藏文件（以 . 开头的文件）在内的所有文件和目录。
ls -lh：结合 -l 和 -h 选项，以人类可读的格式（如KB、MB）显示文件大小。
==rm==
rm file：删除指定的文件。
rm -r directory：递归地删除指定的目录及其内容。
rm -f file：强制删除文件，不提示确认。
rm -rf directory：递归且强制地删除目录及其内容，不提示确认（这是一个非常危险的命令，使用时需谨慎）。
==whoami==
whoami：简单地输出当前登录用户的用户名。

