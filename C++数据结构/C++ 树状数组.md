```cpp
#include<bits/stdc++.h>  
using namespace std;  
  
class BIT{  
    vector<int> tree;  
    int n;  
public:  
    BIT(vector<int> arr):n(arr.size()){  
        tree.resize(n+1,0);  
        for(int i=1;i<=arr.size();i++){  
            update(i,arr[i-1]);  
        }  
    }  
    int lowbit(int x){  
        return x&(-x);  
    }  
    int query(int x){  
        int ans=0;  
        while(x>0){  
            ans+=tree[x];  
            x-=lowbit(x);  
        }  
        return ans;  
    }  
    int query_range(int x1,int x2){  
        return query(x2)-query(x1-1);  
    }  
    void update(int x,int num){  
        while(x<=n){  
            tree[x]+=num;  
            x+=lowbit(x);  
        }  
    }  
};  
  
int main(){  
    vector<int> arr{1,2,3,4,5};  
    BIT bit(arr);  
    cout<<bit.query_range(3,5)<<endl;  
    return 0;  
}
```