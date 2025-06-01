```cpp
#include<bits/stdc++.h>  
using namespace std;  
  
struct Node{  
    int val;  
    Node *left,*right;  
    Node(int val):val(val),left(nullptr),right(nullptr){};  
};  
  
class Tree{  
    Node *head;  
    Node* build(vector<int> arr,int i){  
        if(i>=arr.size()){  
            return nullptr;  
        }  
        Node *node=new Node(arr[i]);  
        node->left=build(arr,2*i+1);  
        node->right=build(arr,2*i+2);  
        return node;  
    }  
    Node* build(vector<int> arr){  
        Node *head=new Node(arr[0]);  
        queue<Node*> q;  
        q.push(head);  
        for(int i=0;i<arr.size()/2;i++){  
            Node *node=q.front();  
            q.pop();  
            if(2*i+1<arr.size()){  
                node->left=new Node(arr[2*i+1]);  
                q.push(node->left);  
            }  
            if(2*i+2<arr.size()){  
                node->right=new Node(arr[2*i+2]);  
            }  
        }  
        return head;  
    }  
    void destroy(Node *head){  
        if(head==nullptr){  
            return;  
        }  
        destroy(head->left);  
        destroy(head->right);  
        delete head;  
    }  
    void pre(Node *head){  
        if(head==nullptr){  
            return;  
        }  
        cout<<head->val<<" ";  
        pre(head->left);  
        pre(head->right);  
    }  
    void in(Node *head){  
        if(head==nullptr){  
            return;  
        }  
        in(head->left);  
        cout<<head->val<<" ";  
        in(head->right);  
    }  
    void post(Node *head){  
        if(head==nullptr){  
            return;  
        }  
        post(head->left);  
        post(head->right);  
        cout<<head->val<<" ";  
    }  
public:  
    Tree():head(nullptr){};  
    Tree(vector<int> arr){  
//        head=build(arr,0);  
        head=build(arr);  
    }  
    Tree(initializer_list<int> arr):Tree(vector<int>(arr)){};  
    Tree(int *arr,int n):Tree(vector<int>(arr,arr+n)){};  
    ~Tree(){  
        destroy(head);  
    }  
    void pre_order(){  
        pre(head);  
        cout<<endl;  
    }  
    void in_order(){  
        in(head);  
        cout<<endl;  
    }  
    void post_order(){  
        post(head);  
        cout<<endl;  
    }  
    void order(){  
        queue<Node*> q;  
        q.push(head);  
        while(!q.empty()){  
            Node *node=q.front();  
            q.pop();  
            cout<<node->val<<" ";  
            if(node->left){  
                q.push(node->left);  
            }  
            if(node->right){  
                q.push(node->right);  
            }  
        }  
        cout<<endl;  
    }  
};  
  
int main(){  
    int arr[5]={1,2,3,4,5};  
    Tree t(arr,5);  
    t.order();  
    t.pre_order();  
    t.in_order();  
    t.post_order();  
}
```

