# 贪心算法几大经典问题：

## 1.活动时间安排的问题

设有N个活动时间集合，每个活动都要使用同一个资源，比如说会议场，而且同一时间内只能有一个活动使用，每个活动都有一个使用活动的开始si和结束时间fi，即他的使用区间为（si,fi）,现在要求你分配活动占用时间表，即哪些活动占用该会议室，哪些不占用，使得他们不冲突，要求是尽可能多的使参加的活动最大化，即所占时间区间最大化！

[image][picture/6-6-1.jpg](https://github.com/libai18/algorithm-codebase/blob/main/picture/6-6-1.jpg)

### ①代码参考

```c++
#include <iostream>
using namespace std;

void GreedyChoose(int len,int *s,int *f,bool *flag);

int main(int argc, char* argv[])
{
	int s[11] ={1,3,0,5,3,5,6,8,8,2,12};
	int f[11] ={4,5,6,7,8,9,10,11,12,13,14};

	bool mark[11] = {0};
	 
	GreedyChoose(11,s,f,mark);
	for(int i=0;i<11;i++)
		if(mark[i])
			cout<<i<<" ";
	system("pause");
	return 0;

}

void GreedyChoose(int len,int *s,int *f,bool *flag)
{
	flag[0] = true;
	int j = 0;
	for(int i=1;i<len;++i)
		if(s[i] >= f[j])
		{
			flag[i] = true;
			j = i;
		}
}
```

上图为每个活动的开始和结束时间，我们的任务就是设计程序输出哪些活动可以占用会议室！

得出结果是 0 3 7 10，也就是对应的时间段

值得说明一下，虽然贪心算法不是一定可以得到最好的解 ，但是对于这种活动时间的问题，他却得到的总是最优解，这点可以用数学归纳法证明，在这里，体现出来的贪心策略是：每一个活动时间的挑选总是选择最优的，就是刚好匹配的，这样得出的结果也就是最优的了！

类似这种题还有个区间覆盖问题，就是说很多个区间，其中有些是相互覆盖着的，要求去除多余的区间，使剩下的区间占用长度最大，实际就是这个题，只是问法变换了而已！接下来让我们看线性覆盖的问题，跟上面的相反！

### ②代码参考

```c++
#include<cstdio>
#include<iostream> 
#include<algorithm> 
using namespace std;    
int N;
struct Act
{
	int start;
	int end;
}act[100010];

bool cmp(Act a,Act b)  
{  
    return a.end<b.end;  
} 

int greedy_activity_selector()  
{  
	int num=1,i=1;   
    for(int j=2;j<=N;j++)  
    {  
        if(act[j].start>=act[i].end)  
        {  
            i=j;  
            num++;  
        }  
    }  
    return num;
}

int main()  
{  
	int t;
	scanf("%d",&t);
	while(t--)
	{
		scanf("%d",&N);
		for(int i=1;i<=N;i++)
		{
			scanf("%lld %lld",&act[i].start,&act[i].end);
		}
		act[0].start=-1;
		act[0].end=-1;
	 	sort(act+1,act+N+1,cmp); 
    	int res=greedy_activity_selector();
		cout<<res<<endl;  
	}
}  
```

其中各项活动按照结束时间单调递增排序。我们的贪心策略应该是每次选取结束时间最早的活动。直观上也很好理解，按这种方法选择相容活动为未安排活动留下尽可能多的时间。这也是把各项活动按照结束时间单调递增排序的原因。

## 2.钱币找零问题

### ①例题

这个问题在我们的日常生活中就更加普遍了。假设1元、2元、5元、10元、20元、50元、100元的纸币分别有c0, c1, c2, c3, c4, c5, c6张。现在要用这些钱来支付K元，至少要用多少张纸币？用贪心算法的思想，很显然，每一步尽可能用面值大的纸币即可。在日常生活中我们自然而然也是这么做的。在程序中已经事先将Value按照从小到大的顺序排好。

```c++
#include<iostream>
#include<algorithm>
using namespace std;
const int N=7; 
int Count[N]={3,0,2,1,0,3,5};
int Value[N]={1,2,5,10,20,50,100};
  
int solve(int money) 
{
	int num=0;
	for(int i=N-1;i>=0;i--) 
	{
		int c=min(money/Value[i],Count[i]);
		money=money-c*Value[i];
		num+=c;
	}
	if(money>0) num=-1;
	return num;
}
 
int main() 
{
	int money;
	cin>>money;
	int res=solve(money);
	if(res!=-1) cout<<res<<endl;
	else cout<<"NO"<<endl;
}
```

### ②例题

假设1元、2元、5元、10元、20元、50元、100元的纸币分别有c0, c1, c2, c3, c4, c5, c6张。现在要用这些钱来找给顾客K元，怎么用数目最少的钱来找零？

贪心准则:在不超过要找的零钱总数的条件下，每一次都选择面值尽可能大的纸币，直到凑成的零钱总数等于要找的零钱总数。

```c
#include<iostream>

using namespace std;

int min(int a, int b) {
	return a < b ? a : b;
}
int main()
{
	//人民币面值集合
	int values[] = { 1, 2, 5, 10, 20, 50, 100 };
	//各种面值对应数量集合
	int counts[] = { 3, 1, 2, 1, 1, 3, 5 };
	//求442元人民币需各种面值多少张
	//用来记录需要的各种面值张数
	int money = 442;
	int len = sizeof(values) / sizeof(values[0]);
	int* result = new int[len];
	for (int i = len - 1; i >= 0; i--) {
		int num = 0; //当前面值纸币的数量
		num = min(money / values[i], counts[i]); //当前纸币可以找的最大数量
		money = money - num*values[i];
		result[i] = num;
	}
	//输出最后结果
	for (int i = 0; i < len; i++) {
		if(result[i])
			cout << "需要面额为" << values[i] << "的人名币" << result[i] << "张\n";
	}
	cout << endl;

	system("pause");
	return 0;
}

```

