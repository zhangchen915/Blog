---
title: "C++ 读取TXT中的数字到vector容器"
categories: ["默认分类"]
tags: ["C++"]
draft: false
slug: "418"
date: "2017-09-09 13:28:00"
---

读取TXT文件中的数字到一个二维vector容器，也可以将vector输出到一个TXT文件中。
这段程序主要有五个函数`readFile`,`split`,`transToNum`,`transVector`,`output`。

1. split分割字符串
2. readFile调用split并返回一个二维字符串向量
3. transToNum将字符串转换为int或double。
4. transVector将二维字符串向量转换为二维int或double向量。
5. output把一维或二维vector输出到txt（文件名默认为output.txt）

### 使用示例
TXT文件：
```
1 3.12 3
-1 1 1 3.123 4.11
```
调用方法：
```cpp
vector<vector<int> > intData;
getTXTNum("input.txt", intData);
output(intData);
```

### 完整程序代码
```cpp
#include <iostream>
#include <vector>
#include <fstream>
#include <stdlib.h>

using namespace std;

vector<vector<string> > readFile(string filePath, char tag = ' ');

vector<string> split(string inputString, char tag);

template<typename T>
void transToNum(string inputString, T *result);

template<typename T>
void transVector(vector<vector<string> > data, vector<vector<T> > &intData);

template<typename T>
void getTXTNum(string filePath, vector<vector<T> > &data);

template<typename T>
void output(vector<vector<T> > data, const  char* outputName="output.txt");

template<typename T>
void output(vector<T> data, const char* outputName="output.txt");


vector<vector<string> > readFile(string filePath, char tag) {
    ifstream fileReader(filePath.c_str(), ios::in);
    if (!fileReader) {
        cerr << filePath << " not exist!\n";
        exit(1);
    }
    vector<vector<string> > data;
    string linestring;
    while (getline(fileReader, linestring)) {
        data.push_back(split(linestring, tag));
    }
    return data;
}

vector<string> split(string inputString, char tag) {
    int length = inputString.length();
    unsigned int start = 0;
    vector<string> line;
    for (unsigned int i = 0; i < length; i++) {
        string sub;
        if (inputString[i] == tag) {
            sub = inputString.substr(start, i - start);
            line.push_back(sub);
            start = i + 1;
        } else if (i == length - 1) {
            sub = inputString.substr(start, i - start + 1);
            line.push_back(sub);
        }
    }
    return line;
}

char *getType(int x) {
    return (char *) "int";
}

char *getType(double x) {
    return (char *) "double";
}

char *getType(float x) {
    return (char *) "float";
}

template<typename T>
void transToNum(string inputString, T *result) {
    const char *p = inputString.c_str();
    char *type = getType(*result);
    if (type == "int")
        *result = atoi(p);
    else
        *result = atof(p);
}

template<typename T>
void transVector(vector<vector<string> > data, vector<vector<T> > &intData) {
    vector<vector<string> >::iterator iter = data.begin();
    for (; iter != data.end(); iter++) {
        vector<string> line = *iter;
        vector<string>::iterator lineIter = line.begin();

        vector<T> intLine;
        for (; lineIter != line.end(); lineIter++) {
            T result;
            transToNum(*lineIter, &result);
            intLine.push_back(result);
        }
        intData.push_back(intLine);
    }
}

template<typename T>
void getTXTNum(string filePath, vector<vector<T> > &data) {
    transVector(readFile(filePath), data);
}

template<typename T>
void output(vector<vector<T> > data, const  char* outputName) {
    ofstream outTxt;
    outTxt.open("output.txt",ios::trunc);
    int sizeA = data.size();
	for(int i=0;i<sizeA;i++){
		int sizeB = data[i].size();
		for(int j=0;j<sizeB;j++){
			outTxt<<data[i][j]<<" ";
		}
		outTxt<<endl;
	}	
}

template<typename T>
void output(vector<T> data, const char* outputName) {
    ofstream outTxt;
    outTxt.open("output.txt",ios::trunc);
    int sizeA = data.size();
	for(int i=0;i<sizeA;i++){
		outTxt<<data[i]<<" ";
	}
	outTxt<<endl;
}
```

参考文章：
[c++ 读取数值文件到数组中][1]


  [1]: http://www.cnblogs.com/fengfenggirl/archive/2013/04/04/2999696.html
