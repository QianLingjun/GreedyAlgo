# GreedyAlgo
# 小白算法带你学--贪心算法GreedyAlgo，欢迎关注公众号：小白算法


贪心算法（Greedy Algorithm) 简介
贪心算法，又名贪婪法，是寻找最优解问题的常用方法，这种方法模式一般将求解过程分成若干个步骤，但每个步骤都应用贪心原则，选取当前状态下最好/最优的选择（局部最有利的选择），并以此希望最后堆叠出的结果也是最好/最优的解。{看着这个名字，贪心，贪婪这两字的内在含义最为关键。这就好像一个贪婪的人，他事事都想要眼前看到最好的那个，看不到长远的东西，也不为最终的结果和将来着想，贪图眼前局部的利益最大化，有点走一步看一步的感觉。}

贪婪法的基本步骤：
步骤1：从某个初始解出发；
步骤2：采用迭代的过程，当可以向目标前进一步时，就根据局部最优策略，得到一部分解，缩小问题规模；
步骤3：将所有解综合起来。

事例一：找零钱问题
假设你开了间小店，不能电子支付，钱柜里的货币只有 25 分、10 分、5 分和 1 分四种硬币，如果你是售货员且要找给客户 41 分钱的硬币，如何安排才能找给客人的钱既正确且硬币的个数又最少？

这里需要明确的几个点：
1.货币只有 25 分、10 分、5 分和 1 分四种硬币；
2.找给客户 41 分钱的硬币；
3.硬币最少化

思考，能使用我们今天学到的贪婪算法吗？怎么做？
（回顾一下上文贪婪法的基本步骤，1，2，3）

1.找给顾客sum_money=41分钱，可选择的是25 分、10 分、5 分和 1 分四种硬币。能找25分的，不找10分的原则，初次先找给顾客25分；
2.还差顾客sum_money=41-25=16。然后从25 分、10 分、5 分和 1 分四种硬币选取局部最优的给顾客，也就是选10分的，此时sum_money=16-10=6。重复迭代过程，还需要sum_money=6-5=1,sum_money=1-1=0。至此，顾客收到零钱，交易结束；
3.此时41分，分成了1个25，1个10，1个5，1个1，共四枚硬币。

编程实现
#include<iostream>
using namespace std;

#define ONEFEN    1
#define FIVEFEN    5
#define TENFEN    10
#define TWENTYFINEFEN 25

int main()
{
    int sum_money=41;
    int num_25=0,num_10=0,num_5=0,num_1=0;

    //不断尝试每一种硬币
    while(money>=TWENTYFINEFEN) { num_25++; sum_money -=TWENTYFINEFEN; }
    while(money>=TENFEN) { num_10++; sum_money -=TENFEN; }
    while(money>=FIVEFEN)  { num_5++;  sum_money -=FIVEFEN; }
    while(money>=ONEFEN)  { num_1++;  sum_money -=ONEFEN; }

    //输出结果
    cout<< "25分硬币数："<<num_25<<endl;
    cout<< "10分硬币数："<<num_10<<endl;
    cout<< "5分硬币数："<<num_5<<endl;
    cout<< "1分硬币数："<<num_1<<endl;

    return 0;
}
事例二:背包最大价值问题
有一个背包，最多能承载重量为 C=150的物品，现在有7个物品（物品不能分割成任意大小），编号为 1~7，重量分别是 wi=[35,30,60,50,40,10,25]，价值分别是 pi=[10,40,30,50,35,40,30]，现在从这 7 个物品中选择一个或多个装入背包，要求在物品总重量不超过 C 的前提下，所装入的物品总价值最高。

这里需要明确的几个点：
1.每个物品都有重量和价值两个属性；
2.每个物品分被选中和不被选中两个状态（后面还有个问题，待讨论）；
3.可选物品列表已知，背包总的承重量一定。

所以，构建描述每个物品的数据体结构 OBJECT和背包问题定义为：

//typedef是类型定义的意思

//定义待选物体的结构体类型
typedef struct tagObject
{
    int weight;
    int price;
    int status;
}OBJECT;

//定义背包问题
typedef struct tagKnapsackProblem
{
    vector<OBJECT>objs;
    int totalC;
}KNAPSACK_PROBLEM;
这里采用定义结构体的形式，主要是可以减少代码的书写量,可以实现代码的复用性和可扩展性,简化，提高可读性。就是贪图简单方便，规避繁琐。

如下,实例化objects


OBJECT objects[] = { { 35,10,0 },{ 30,40,0 },{ 60,30,0 },{ 50,50,0 },
                    { 40,35,0 },{ 10,40,0 },{ 25,30,0 } };
思考：如何选，才使得装进背包的价值最大呢？
策略1：价值主导选择，每次都选价值最高的物品放进背包；
策略2：重量主导选择，每次都选择重量最轻的物品放进背包；
策略3：价值密度主导选择，每次选择都选价值/重量最高的物品放进背包。

（贪心法则：求解过程分成若干个步骤，但每个步骤都应用贪心原则，选取当前状态下最好的或最优的选择（局部最有利的选择），并以此希望最后堆叠出的结果也是最好或最优的解）

策略1：价值主导选择，每次都选价值最高的物品放进背包
根据这个策略最终选择装入背包的物品编号依次是 4、2、6、5，此时包中物品总重量是 130，总价值是 165。

//遍历没有被选的objs,并且选择price最大的物品,返回被选物品的编号
int Choosefunc1(std::vector<OBJECT>& objs, int c)
{
    int index = -1;  //-1表示背包容量已满
    int max_price = 0;
    //在objs[i].status == 0的物品里，遍历挑选objs[i].price最大的物品
    for (int i = 0; i < static_cast<int>(objs.size()); i++)
    {
        if ((objs[i].status == 0) && (objs[i].price > max_price ))//objs没有被选,并且price> max_price 
        {
            max_price  = objs[i].price;
            index = i;
        }
    }

    return index;
}
策略2：重量主导选择，每次都选择重量最轻(小)的物品放进背包
根据这个策略最终选择装入背包的物品编号依次是 6、7、2、1、5，此时包中物品总重量是 140，总价值是 155。

int Choosefunc2(std::vector<OBJECT>& objs, int c)
{
    int index = -1;
    int min_weight= 10000;
    for (int i = 0; i < static_cast<int>(objs.size()); i++)
    {
        if ((objs[i].status == 0) && (objs[i].weight < min_weight))
        {
            min_weight= objs[i].weight;
            index = i;
        }
    }

    return index;
}
策略3：价值密度主导选择，每次选择都选价值/重量最高(大)的物品放进背包
物品的价值密度 si 定义为 pi/wi，这 7 件物品的价值密度分别为 si=[0.286,1.333,0.5,1.0,0.875,4.0,1.2]。根据这个策略最终选择装入背包的物品编号依次是 6、2、7、4、1，此时包中物品的总重量是 150，总价值是 170。

int Choosefunc3(std::vector<OBJECT>& objs, int c)
{
    int index = -1;
    double max_s = 0.0;
    for (int i = 0; i < static_cast<int>(objs.size()); i++)
    {
        if (objs[i].status == 0)
        {
            double si = objs[i].price;
            si = si / objs[i].weight;
            if (si > max_s)
            {
                max_s = si;
                index = i;
            }
        }
    }

    return index;
}
有了物品，有了方法，下面就是将两者结合起来的贪心算法GreedyAlgo
void GreedyAlgo(KNAPSACK_PROBLEM *problem, SELECT_POLICY spFunc)
{
    int idx;
    int sum_weight_current = 0;
    //先选
    while ((idx = spFunc(problem->objs, problem->totalC- sum_weight_current)) != -1)
    {   //再检查，是否能装进去
        if ((sum_weight_current + problem->objs[idx].weight) <= problem->totalC)
        {
            problem->objs[idx].status = 1;//如果背包没有装满，还可以再装,标记下装进去的物品状态为1
            sum_weight_current += problem->objs[idx].weight;//把这个idx的物体的重量装进去，计算当前的重量
        }
        else
        {
            //不能选这个物品了，做个标记2后重新选剩下的
            problem->objs[idx].status = 2;
        }
    }
    PrintResult(problem->objs);//输出函数的定义，查看源代码
}
注意：这里对objs[idx].status定义了三种状态，分别是待选择为0（初始所有状态均为0），装进包里变为1，判断不符合变为2，这样最后只需要拿去状态为1的即可。
主函数部分
OBJECT objects[] = { { 35,10,0 },{ 30,40,0 },{ 60,30,0 },{ 50,50,0 },
                    { 40,35,0 },{ 10,40,0 },{ 25,30,0 } };
int main()
{
    KNAPSACK_PROBLEM problem;

    problem.objs.assign(objects, objects + 7);//assign赋值，std::vector::assign
    problem.totalC = 150;

    cout << "Start to find the best way ,NOW" << endl;
    GreedyAlgo(&problem, Choosefunc3);

    system("pause");
    return 0;
}
查看策略3的输出结果：



但是，我们再回顾一下第一个事例问题
现在问题变了，还是需要找给顾客41分钱，现在的货币只有 25 分、20分、10 分、5 分和 1 分四种硬币；该怎么办？

按照贪心算法的三个步骤：
1.41分，局部最优化原则，先找给顾客25分；
2.此时，41-25=16分，还需要找给顾客10分，然后5分，然后1分；
3.最终，找给顾客一个25分，一个10分，一个5分，一个1分，共四枚硬币。

是不是觉得哪里不太对，如果给他2个20分，加一个1分，三枚硬币就可以了呢？^_^;

总结：贪心算法的优缺点
优点：简单，高效，省去了为了找最优解可能需要穷举操作，通常作为其它算法的辅助算法来使用；
缺点：不从总体上考虑其它可能情况，每次选取局部最优解，不再进行回溯处理，所以很少情况下得到最优解。
完整代码：https://github.com/QianLingjun/GreedyAlgo 



下期预告：小白带你学算法，下期学习算法为---回溯算法；想get更多有趣算法知识？请加微信公众号“小白算法”，回复215，还可以免费获取著名世界计算机教材《算法设计》一书。








