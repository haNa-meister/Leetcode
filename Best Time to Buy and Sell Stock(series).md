# 121.122.123.188.309 Best Time to Buy and Sell Stock
## Problem1:
Say you have an array for which the ith element is the price of a given stock on day i.  
If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.  
Example 1:  
Input: [7, 1, 5, 3, 6, 4]  
Output: 5  
max. difference = 6-1 = 5 (not 7-1 = 6, as selling price needs to be larger than buying price)
## Problem2:
Say you have an array for which the ith element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).  
## Problem3:
Say you have an array for which the ith element is the price of a given stock on day i.  
Design an algorithm to find the maximum profit. You may complete at most two transactions.  
## Problem4:
Say you have an array for which the ith element is the price of a given stock on day i.  
Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times) with the following restrictions:  
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).  
After you sell your stock, you cannot buy stock on next day. (ie, cooldown 1 day)  
Example:  
prices = [1, 2, 3, 0, 2]  
maxProfit = 3  
transactions = [buy, sell, cooldown, buy, sell]  
## Problem5:
>Say you have an array for which the ith element is the price of a given stock on day i.  
>Design an algorithm to find the maximum profit. You may complete at most k transactions.  
## Solution:
P1:这个题非常简单，因为我们只能买卖一次股票，并且我们的最后一次操作一定是卖出股票，所以我们只需要记录i天之前最小值，这就是我们可能的最大收入的买入时机。  
并且计算出假设我们在最佳买入时机买入，并在第i天卖出，可能的最大收入，并且一直更新最大收入即可。  
### Code:
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n=prices.size();
        int profit=0;
        if(n==0) return profit;
        int min=INT_MAX;
        for(int i=0;i<n;i++){
            if(prices[i]<min){
                min=prices[i];
            }
            else{
                profit=(prices[i]-min)>profit?(prices[i]-min):profit;
            }
        }
        return profit;
    }
};
```
P2, P4:这两个题是差不多性质的，他们的特点是可以无限次的买入和卖出，因此他们的最大利润一定是考虑每天是否买入或者卖出（并且题目中说明了只能同时拥有一直股票）。。  
假设我们维护4个变量pre_buy,buy,pre_sell,sell  
其中：  
pre_buy: 代表i天之前最后一次操作是买入股票的最大收益总额  
buy：代表i天之前包括i天最后一次操作是买入股票的最大收益总额  
pre_sell: 代表i天之前最后一次操作是买入股票的最大收益总额  
sell：代表i天之前包括i天最后一次操作是买入股票的最大收益总额  
因此如果我们选在在第i天尝试买入：buy=max(pre_sell-prices[i],pre_buy);//i天之前包括i天最后一次操作是买入股票的最大收益总额=(代表i天之前最后一次操作是买入股票的最大收益总额-买入第i天的股票)和(代表i天之前最后一次操作是买入股票的最大收益总额)  
同理：sell=max(pre_buy+prices[i],pre_sell);  
用数学归纳法的的思想来看这个问题：  
如果我们知道i-1天时持有股票时的利润，和i-1天时不持有股票时的利润，  
就可以知道i天之后持有该股票的利润和i天后不持有该股票的的利润。  
  
对于P4，最大的区别就在于有一个一天的冷冻期，意味着不能出现[sell,buy]这种行为  
因此唯一需要改动的就是将buy中的pre_sell用pre_pre_sell来代替即第i天后持有股票的最大利润不能用i-1天后的不持有股票利润计算还是用i-2天后的不持有该股票来计算  

### Code:
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int buy=INT_MIN;int pre_buy=0;int pre_sell=0;int sell=0;
        for(int price:prices){
            pre_buy=buy;
            pre_sell=sell;
            buy=max(pre_sell-price,pre_buy);
            sell=max(pre_buy+price,pre_sell);
        }
        return sell;
    }
};
```
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int buy=INT_MIN;int pre_buy=0;int pre_sell=0;int sell=0;
        for(int price:prices){
            pre_buy=buy;
            buy=max(pre_sell-price,pre_buy);
            pre_sell=sell;
            sell=max(pre_buy+price,pre_sell);
        }
        return sell;
    }
};
```

P3，这个问题的特点是至多只有两次买入卖出，因此关键点在于两次操作的分割线在哪里，因此方法是遍历第i天为两次操作的分割线。  
例如如果第i天时两次操作的分割线，那么意味着第一次操作在0，i天之间，第二次操作在i，prices.size()-1天之间。  
因此我们用两个数组left,right记录0，i天的一次交易的最大利润，和i，prices.size()-1天之间一次交易的最大利润。  
然后我们就能很容易的得到如果以第i天为分割最大的收益为left[i]+right[i];  

### Code:
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.size()<2) return 0;
        vector<int> left(prices.size(),0);
        vector<int> right(prices.size(),0);
        int min1=prices[0];
        for(int i=1;i<left.size();i++){
            left[i]=max(left[i-1],prices[i]-min1);
            min1=min(min1,prices[i]);
        }
        int max1=prices[prices.size()-1];
        for(int i=right.size()-2;i>-1;i--){
            right[i]=max(right[i+1],max1-prices[i]);
            max1=max(max1,prices[i]);
        }
        int maxprofit=0;
        for(int i=0;i<left.size();i++){
            maxprofit=maxprofit>(left[i]+right[i])?maxprofit:(left[i]+right[i]);
        }
        return maxprofit;
    }
};
```
p5, 这个问题，我们一看题就知道很显然地是要用动态规划的思路（或者用堆），  
如果我们知道第i天之前最后一次操作为买的最大资产为buy  
如果我们知道第i天之前最后一次操作为买的最大资产为sell  
并且buy[j]代表第i天之前第j次买的最大资产  
并且sell[j]代表第i天之前第j次卖的最大资产  
可以得到动态规划方程：  
buy[j]=max(sell[j-1]-prices,buy[j])  
sell[j]=max(buy[j]+prices,sell[j])  

### Code:
```cpp
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if(prices.size()==0) return 0;
        int ans=0;
        if(k>prices.size()/2){
            for(int i=1;i<prices.size();i++){
                ans+=max(prices[i]-prices[i-1],0);
            }
            return ans;
        }
        else{
            vector<int> buy(k+1,INT_MIN);
            vector<int> sell(k+1,0);
            for(int price:prices){
                for(int j=1;j<=k;j++){
                    buy[j]=max(sell[j-1]-price,buy[j]);
                    sell[j]=max(buy[j]+price,sell[j]);
                }
            }
            return sell[k];
        }
    }
};
```
