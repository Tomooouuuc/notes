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
    Node* build(Node *root,int val){  
        if(root==nullptr){  
            return new Node(val);  
        }  
        if(val<root->val){  
            root->left=build(root->left,val);  
        }else{  
            root->right=build(root->right,val);  
        }  
        return root;  
    }  
    void build(vector<int> arr){  
        head=new Node(arr[0]);  
        for(int i=1;i<arr.size();i++){  
            Node *cur=head;  
            Node *node=nullptr;  
            while(cur!=nullptr){  
                node=cur;  
                if(arr[i]<cur->val){  
                    cur=cur->left;  
                }else{  
                    cur=cur->right;  
                }  
            }  
            if(arr[i]<node->val){  
                node->left=new Node(arr[i]);  
            }else{  
                node->right=new Node(arr[i]);  
            }  
        }  
    }  
    void insert_node(Node *&root,int val){  
        if(root==nullptr){  
            root=new Node(val);  
            return;  
        }  
        if(val<root->val){  
            insert_node(root->left,val);  
        }else{  
            insert_node(root->right,val);  
        }  
    }  
    Node* delete_node(Node *root,int val){  
        if(root==nullptr){  
            return root;  
        }  
        if(val<root->val){  
            root->left=delete_node(root->left,val);  
        }else if(val>root->val){  
            root->right=delete_node(root->right,val);  
        }else{  
            if(root->left==nullptr){  
                Node *tmp=root->right;  
                delete root;  
                return tmp;  
            }else if(root->right==nullptr){  
                Node *tmp=root->left;  
                delete root;  
                return tmp;  
            }  
            Node *tmp=findMin(root->right);  
            root->val=tmp->val;  
            root->right=delete_node(root->right,tmp->val);  
        }  
        return root;  
    }  
    Node* findMin(Node *root){  
        while(root&&root->left){  
            root=root->left;  
        }  
        return root;  
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
    bool get_node(Node *root,int val){  
        if(root==nullptr){  
            return false;  
        }  
        if(root->val==val){  
            return true;  
        }  
        return val<root->val?get_node(root->left,val):get_node(root->right,val);     }  
public:  
    Tree():head(nullptr){};  
    Tree(vector<int> arr){  
//        for(int i=0;i<arr.size();i++){  
//            head=build(head,arr[i]);  
//        }  
        build(arr);  
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
    bool get(int val){  
        return get_node(head,val);  
    }  
    void insert(int val){  
        insert_node(head,val);  
    }  
    void del(int val){  
        delete_node(head,val);  
    }
    friend ostream& operator<<(ostream& os,const Tree &t){  
        os<<"======================="<<endl;  
        queue<Node*> q;  
        q.push(t.head);  
        while(!q.empty()){  
            int n=q.size();  
            os<<"--------------------"<<endl;  
            for(int i=0;i<n;i++){  
                Node *node=q.front();  
                q.pop();  
                os<<"("<<node->val<<",";  
                node->left==nullptr?os<<"N,":os<<node->left->val<<",";  
                node->right==nullptr?os<<"N)"<<endl:os<<node->right->val<<")"<<endl;  
                if(node->left){  
                    q.push(node->left);  
                }  
                if(node->right){  
                    q.push(node->right);  
                }  
            }  
        }  
        os<<"======================="<<endl;  
        return os;  
    }  
};  
  
int main(){  
    int arr[7]={4,2,6,1,3,5,7};  
    Tree t(arr,7);  
    t.in_order();  
    cout<<boolalpha<<t.get(6)<<endl;  
    t.del(6);  
    t.in_order();  
    cout<<boolalpha<<t.get(6)<<endl;  
}
```


