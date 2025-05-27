```cpp
#include<bits/stdc++.h>  
using namespace std;  
  
struct Node{  
    int val,h;  
    Node *left,*right;  
    Node(int val):val(val),h(1),left(nullptr),right(nullptr){};  
};  
  
class Tree{  
    Node *head;  
    int get_h(Node *root){  
        return root?root->h:0;  
    }  
    void update_h(Node *root){  
        root->h=max(get_h(root->left),get_h(root->right))+1;  
    }  
    int get_balance(Node *root){  
        return root!=nullptr?get_h(root->left)-get_h(root->right):0;  
    }  
    Node* rotateL(Node *root){  
        Node *tmp=root->right;  
        root->right=root->right->left;  
        tmp->left=root;  
        update_h(root);  
        update_h(tmp);  
        return tmp;  
    }  
    Node* rotateR(Node *root){  
        Node *tmp=root->left;  
        root->left=root->left->right;  
        tmp->right=root;  
        update_h(root);  
        update_h(tmp);  
        return tmp;  
    }  
    Node* balance(Node *root){  
        int tmp=get_balance(root);  
        if(abs(tmp)<=1){  
            return root;  
        }  
        if(tmp>1){  
            if(get_balance(root->left)<-1){  
                root->left=rotateL(root->left);             }  
            root=rotateR(root);  
        }  
        if(tmp<-1){  
            if(get_balance(root->right)>1){  
                root->right=rotateR(root->right);  
            }  
            root=rotateL(root);  
        }  
        return root;  
    }  
    Node* insert_node(Node *root,int val){  
        if(root==nullptr){  
            return new Node(val);  
        }  
        if(val<root->val){  
            root->left=insert_node(root->left,val);  
        }else{  
            root->right=insert_node(root->right,val);  
        }  
        update_h(root);  
            return balance(root);  
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
        update_h(root);  
        return balance(root);  
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
        for(int i=0;i<arr.size();i++){  
            insert(arr[i]);  
        }  
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
        head=insert_node(head,val);  
    }  
    void del(int val){  
        head=delete_node(head,val);  
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
    Tree t{1,2,3,4,5,6,7};  
    cout<<t<<endl;  
    t.insert(8);  
    t.insert(9);  
    cout<<t<<endl;  
    t.del(5);  
    cout<<t<<endl;  
}
```

