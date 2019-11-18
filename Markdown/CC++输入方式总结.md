# C/C++输入方式总结

## 一. C++方式

### 1.1 cin

- 头文件：` #include <iostream> `
- 功能：接收一个字符或者字符串
- 遇到<font color=red>空格、TAB、回车</font>即停止返回
- 示例代码

```c++
#include <iostream>
using namespace std;

int main(void)
{
    char str[521] = {0};
    
    cin >> a;
    cout << a;
    
    return 0;
}
```

### 1.2 cin.get()

- 头文件：` #include <iostream> `
- 用法：cin.get(字符变量名) 接受一个字符，cin.get(字符数组名，接收长度)，接收字符串，<font color=red>可以接收空格</font>
- 示例代码

```c++

#include<iostream>
using namespace std;
int main(){
	char ch;
	ch=cin.get();//或者cin.get(ch)
	cout<<ch; 
	return 0;
}
```

输入：abcd          输出：a

```c++
#include<iostream>
using namespace std;
int main(){
	char ch[20];
	cin.get(ch,20);
	cout<<ch; 
	return 0;
}
```

输入：abc def       输出：abc def

输入：abcdeabcdeabcdeabcdeabcde（25个字符）       输出：abcdeabcdeabcdeabcd（19个字符+1个‘\0'）

### 1.3 cin.getline()

- 语法：cin.getline(字符指针(char*),字符个数N(int),结束符(char))，当结束符省略时，默认为'\0'
- 用法：接受一个字符串，可以接收空格并输出。输入过程中达到N个数或者提前遇到结束符时，输入结束
- 示例代码

```c++
#include<iostream>
using namespace std;
int main(){
	char ch[20];
	cin.getline(ch,5);
	cout<<ch; 
	return 0;
} 
```

输入：abcdef        输出：abcd

```c++
#include<iostream>
using namespace std;
int main(){
	char ch[20],b;
	cin.getline(ch,5,'b');
	cout<<ch; 
	return 0;
}
```

输入：abcdef       输出：a

### 1.4 getline()

- 头文件：#include<string>
- 用法：可以接收空格并输出，和cin.getline()类似，但cin.getline()属于istream流，getline()属于string流，getline(cin,s)获取一行字符串。它不同于cin>>s，遇空格则停

```c++
#include<iostream>
#include<string>
using namespace std;
int main(){
    string s;
    getline(cin,s);//遇空格不停
    cout<<s;
    return 0;
}
```


输入：abc def      输出：abc def

如果只是cin>>s，会输出abc（遇空格则停）

## 二. C语言方式

### 2.1 scanf

- 头文件：` #include <stdio.h>
- 功能：通过键盘给程序中的变量赋值
- 注意：遇到<font color=red>空格、回车</font>停

### 2.2 gets()

- 头文件：#include <stdio.h>
- 功能：读取字符串
- 注意：遇空格不停，遇到回车或者EOF停。可以无限读取，注意buffer溢出
- 这个在C++11中好像被移除？vs2015不支持这个玩意，取而代之的是gets_s()