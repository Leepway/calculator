# calculator
 easy cmd calculator
## 最近学习Coursera上的算法课，刚开始接触分治算法（Divide & Conquer），第一周的测试题为两个大位整数的乘法，结合自己自学的弱爆的C++，各种查资料还调了一整天的Bug才写出来个不完整的东西。只怪自己平时代码敲得少，不熟练。很多bug遇到过就会对此很敏感，所以多去造轮子，多尝试自己去实现才是最快的学习方法。哈哈哈，一堆废话，自勉之~

## 下面是自己写的源码，其中还有许多不妥的地方，第一次写（捂脸跑~）完好兴奋呀。。。

```
#include <stdio.h>
#include <iostream>
#include <list>
#include <iterator>
#include <string>

using namespace std;


void PrintList(list<char>& m); //计算结果输出函数
void PrintMenu();   //功能菜单
list<char> TransForm(string &s); //转换函数
list<char> BigAdd(list<char>&a, list<char>&b); //大数加法
list<char> BigSub(list<char>&a, list<char>&b); //大数减法
list<char> BigMul(list<char>&a, list<char>&b); //大数乘法
void BigDev();   //大数除法，没实现
//list<char> result;  //设为全局变量,则可以返回其引用以节省内存开销，    但在递归时不知道怎么处理。。。
int main(int argc, char *argv[])
{
    list<char> num1, num2;
    list<char> result;
    char choice;
    string input1, input2;
    while (true)
    {
        cout << "**********************************" << endl;
        cout << "   大整数计算器 VER1.0    " << endl;
        cout << "**********************************" << endl;
        PrintMenu();
        result.clear();
        cin >> choice;
        fflush(stdin);  //清空缓冲区，删去多余输入
        switch (choice)
        {
        case '1':
            cout << "请输入第一个数  " << endl;
            cin >> input1;
            num1 = TransForm(input1);
            cout << "请输入第二个数  " << endl;
            cin >> input2;
            num2 = TransForm(input2);
            result = BigAdd(num1, num2);
            PrintList(num1);
            cout << " + ";
            PrintList(num2);
            cout << " = ";
            PrintList(result);//
            cout << endl << endl;
            break;
        case '2':
            cout << "请输入第一个数  " << endl;
            cin >> input1;
            num1 = TransForm(input1);
            cout << "请输入第二个数  " << endl;
            cin >> input2;
            num2 = TransForm(input2);
            result = BigSub(num1, num2);
            PrintList(num1);
            cout << " - ";
            PrintList(num2);
            cout << " = ";
            PrintList(result);//
            cout << endl << endl;
            break;
        case '3':
            cout << "请输入第一个数  " << endl;
            cin >> input1;
            num1 = TransForm(input1);
            cout << "请输入第二个数  " << endl;
            cin >> input2;
            num2 = TransForm(input2);
            result = BigMul(num1, num2);
            PrintList(num1);
            cout << " * ";
            PrintList(num2);
            cout << " = ";
            PrintList(result);//
            cout << endl << endl;
            break;
        case '4':
            return 0;
        }

    }


    system("pause");
    return 0;
}

list<char> BigAdd(list<char>& a, list<char>& b)  //大数加法
{

    int len1, len2;
    int carry = 0;//carry为进位
    list<char> result;
    len1 = a.size(), len2 = b.size();

    //补全位置
    if (len1 <= len2)
    {
        for (int i = 0; i<len2 - len1; i++)
        {
            a.push_front('0');
        }
    }
    else
    {

        for (int i = 0; i<len1 - len2; i++)
        {
            b.push_front('0');
        }
    }

    auto iter1 = a.rbegin(), iter2 = b.rbegin();//从低位遍历，使用反序迭代器很方便
    int i = 0;
    for (; iter1 != a.rend(); ++iter1, ++iter2)
    {
        i = ((*iter1 - '0') + (*iter2 - '0')) + carry;
        result.push_front((i % 10 + '0'));
        carry = i / 10;

    }
    if (carry)
    {
        result.push_front(carry + '0');
    }

    return result;

}

list<char> BigSub(list<char>&a, list<char>&b)    //大数减法
{
    list<char> result;
    int len1 = a.size(), len2 = b.size();

    bool flag;       //判断a,b的大小
    if (len1<len2)
        flag = false;
    else if (len1 == len2)
    {
        auto iter_1 = a.begin(), iter_2 = b.begin();
        for (; iter_1 != a.end(); iter_1++, iter_2++)
        {
            if ((*iter_1)<(*iter_2))
            {
                flag = false;
                break;
            }
            flag = true;
        }

    }
    else
        flag = true;

    if (flag)  //a>b时
    {
        for (int i = 0; i<len1 - len2; i++)
        {
            b.push_front('0');  //补全位置
        }
        auto iter1 = a.rbegin(), iter2 = b.rbegin(); //大赞反向迭代器！
        for (int j = 0; iter1 != a.rend(); )        //容器的遍历尽量使用其迭代器控制，不要用int i和a.size()来控制
        {
            if (((*iter1) - (*iter2))<0)
            {
                j = 10 + *iter1 - *iter2;
                result.push_front(j + '0');
                (*(++iter1))--, ++iter2;
            }
            else
            {
                j = *iter1 - *iter2;
                result.push_front(j + '0');
                ++iter1, ++iter2;
            }
        }

        return result;
    }
    else     //a<b,先计算b-a,在结果前加'-'
    {
        for (int i = 0; i<len2 - len1; i++)
        {
            a.push_front('0');
        }
        auto iter1 = a.rbegin(), iter2 = b.rbegin();
        for (int j = 0; iter1 != a.rend(); )
        {
            if ((*iter2 - *iter1)<0)
            {
                j = 10 + *iter2 - *iter1;
                result.push_front(j + '0');
                (*(++iter2))--, ++iter1;
            }
            else
            {
                j = *iter2 - *iter1;
                result.push_front(j + '0');
                ++iter2, ++iter1;
            }
        }

        result.push_front('-');

        return result;
    }
}

list<char> BigMul(list<char> &a, list<char> &b)  //大数乘法
{

    list<char> AC, BD, A, B, C, D; //ABCD分别为两数字的高位段、低位段
    list<char> result;
    int k;
    if ((a.size() == 1) && (b.size() == 1))
    {
        k = (*a.begin() - '0')*(*b.begin() - '0');  //k最小为0，最大为81
        int i, j;
        i = k % 10, j = k / 10;
        result.push_front(i + '0');
        if (j)          //如果k<10,还是不要进0了
            result.push_front(j + '0');
    }
    else if ((a.size() == 1) && (b.size()>1))
    {
        int m = b.size() / 2;// n =(b.size()+1)/2;
        auto iter = b.rbegin();
        list<char> HighRes, LowRes;
        for (int i = b.size(); i>m; i--)
        {
            D.push_front(*iter);
            iter++;
        }
        for (; iter != b.rend();)
        {
            C.push_front(*iter);
            iter++;
        }
        HighRes = BigMul(a, C);
        for (int i = 0; i<(b.size() - m); i++)
        {
            HighRes.push_back('0');
        }
        LowRes = BigMul(a, D);
        result = BigAdd(HighRes, LowRes);
    }
    else if ((b.size() == 1) && (a.size()>1))
    {
        int m = a.size() / 2;// n =(b.size()+1)/2;
        auto iter = a.rbegin();
        list<char> HighRes, LowRes;
        for (int i = a.size(); i>m; i--)
        {
            B.push_front(*iter);
            iter++;
        }
        for (; iter != a.rend();)
        {
            A.push_front(*iter);
            iter++;
        }
        HighRes = BigMul(b, A);
        for (int i = 0; i<(a.size() - m); i++)
        {
            HighRes.push_back('0');
        }
        LowRes = BigMul(b, B);
        result = BigAdd(HighRes, LowRes);
    }
    else
    {
        list<char> A, C, B, D;
        int m = a.size() / 2, n = b.size() / 2;
        auto iter1 = a.rbegin(), iter2 = b.rbegin();
        for (int i = a.size(); i>m; i--)
        {
            B.push_front(*iter1);  //写入num1低位分段
            iter1++;
        }
        for (; iter1 != a.rend();)
        {
            A.push_front(*iter1);  //写入num1高位分段
            iter1++;
        }
        for (int j = b.size(); j>n; j--)
        {
            D.push_front(*iter2);  //写入num2低位分段
            iter2++;
        }
        for (; iter2 != b.rend();)
        {
            C.push_front(*iter2);  //写入num2高位分段
            iter2++;
        }
        AC = BigMul(A, C);
        for (int i = 0; i<(a.size() - m); i++)
        {
            AC.push_back('0');
        }
        for (int j = 0; j<(b.size() - n); j++)
        {
            AC.push_back('0');
        }
        BD = BigMul(B, D);
        list<char> AD, BC;
        AD = BigMul(A, D);   //其实还有一种算法可以少计算一次乘法:
                                ```math
                                (a*10^n+b)*(c*10^n+d) = ac*10^2n+bd+(ac+bd)*10^n
                                其中 ac 和 bd共需要两次乘法，考虑 ac + bd = （a+b）*(c+d)-ac-bd, ac及bd上一步已求出，故可少做一次乘法!!!
                                ```
        ）
        for (int i = 0; i<(a.size() - m); i++)
        {
            AD.push_back('0');  //补齐低位
        }
        BC = BigMul(B, C);
        for (int j = 0; j<(b.size() - n); j++)
        {
            BC.push_back('0');
        }
        result = BigAdd(AC, BD);
        result = BigAdd(result, AD);
        result = BigAdd(result, BC);
        //midres=BigSub( BigSub( BigMul( BigAdd(Num1High,Num1Low), BigAdd(Num2High,Num2Low)),BigMul(Num1High,Num2High) ), BigMul(Num1Low,Num2Low));
        //result=BigAdd( BigAdd(BigMul(Num1High,Num2High),lowres), midres);
        //这里由于函数返回类型并非 list <char>& 类型，所以不能将函数本身作为参数！
        //在含有递归的函数里，如何才能返回引用类型呢？？？求解。。。
    }

    return result;
}



void PrintMenu()

{
    cout << endl;
    cout << "请输入要进行的运算： " << endl;
    cout << endl;
    cout << "1.  " << "加法运算" << endl;
    cout << "2.  " << "减法运算" << endl;
    cout << "3.  " << "乘法运算" << endl;
    cout << "4.  " << "退出" << endl;
    cout << endl;
}
list<char> TransForm(string &s)
{
    list<char> result;
    for (int i = 0; i<s.length(); i++)
    {
        result.push_back(s[i]);
    }
    return result;
}

void PrintList(list<char>& m)
{
    bool flag = false;  //记录数字前面是否有多余的0
    list<char>::iterator iter = m.begin();
    if (*iter == '-')
    {
        cout << '-';
        m.pop_front();
        iter = m.begin();
    }

    while (iter != m.end())
    {
        if ((*iter == '0') && !flag)
        {
            m.pop_front(); iter = m.begin(); //过滤掉多余的0
            continue;
        }
        else
        {
            flag = true;
            cout << *iter;
            iter++;
        }
    }
}

```
