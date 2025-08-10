## 1.读入由空格或者其他符号分割的字符串将其分开的操作

```c++
//要加这个头文件
#include<sstream>
string s;
    getline(cin, s);
	stringstream ss;
	ss << s;
	string temp;
	vector<string>ve;
   //下面是切割空格的技巧
	while (ss >> temp) {
		ve.push_back(temp);
	}
//下面是所有字符都能切割 只要改变 getline的第三个参数
    while(getline(ss,temp,',')){
        temp.push_back(temp);
    }
}
```

