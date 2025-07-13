# **<font face="仿宋" color=black>Markdown</font>**
## 一.语法
1. **标题**
```
# 一级标题
```
1. **引用**
> 编辑文档
>> 分级
1. **列表**
   1. 无序列表
   - 列表1
   + 列表2
   1. 有序列表
   2. 嵌套
   3. TodoList
      - [x] a
      - [ ] b
      - [ ] c
2. **表格**
    | 左对齐 | 居中 | 右对齐 |
    | :- | :-: | -: |
    | a | b | c |  
      
3. **段落**
    - 换行? 两个以上空格后回车或空一行  
    - 分割线 三个***
    ***
    - 字体  
    ctrl + b 加粗 <br> ctrl + i 斜体 <br>Alt + s 删除线
        | 字体 | 代码 |
        | :--: | :--: |
        | *斜体* | * * |
        |==高亮== | == == |
        |**粗体** | ** ** |
        |***斜粗体*** | *** *** |
        |~~删除~~ | ~~ ~~ |
        |<u>下划线</u>|``` <u> </u> ```|
    - 脚注
      Hello [^1] 
4. **代码**
    ```c++ {.line-numbers} //可以指明代码语言 {.line-numbers} 显示代码行数
    #include<iostream>
    using namespace std;
    int main()
    {
        cout << "Hello World !" << endl;
        return 0;
    }
    ```
    `运行结果: Hello World !`  
    <a id="user-content-set1"></a>
5. **超链接**
    - [参考网站](https://www.runoob.com/markdown/md-link.html)
    - [点击][教程]  
6. **图片**
    - 使用图床保存图片并实现插入
        [路过图床] https://imgse.com/
    - 使用Markdown语法插入
    [![pkkvTNn.jpg](https://s21.ax1x.com/2024/05/03/pkkvTNn.jpg)](https://imgse.com/i/pkkvTNn)
    - 使用html语言实现调整图片大小和位置功能   
    - align=center居中,left居左,right居右
    <a href="https://imgse.com/i/pkkvTNn"><div align=center><img src="https://s21.ax1x.com/2024/05/03/pkkvTNn.jpg" alt="pkkvTNn.jpg" border="0" width="50%"/></div></a>
7. **文档内跳转**  
标题跳转  
[跳转到语法](#一语法)

自定义锚点跳转  
[自定义锚点跳转](#user-content-set1)


### 二. 其他操作
   - **插入latex公式**
     - 行内显示公式:  $f(x)=ax+b$
     - 块内显示公式 //以下为latex语法
     $$
     \begin{Bmatrix}
     a & b \\
     c & d
     \end{Bmatrix}
     $$

   - **html/css语法**  
      - ctrl+shift+p 搜索
      "Markdown Preview Enhanced:Customize CSS"在style中使用css语法改标题格式等
   - **个性化设置**
    File-Preferences-Settings
#### 三. 导出
 - 转到浏览器打印导出为PDF

          

[教程]: https://www.runoob.com/markdown/md-link.html
[^1]: 这是一个脚注
    