```cpp
#include<bits/stdc++.h>  
using namespace std;  
  
struct Node{  
    int val,h=1;  
    Node *left=nullptr,*right=nullptr;  
    Node(int val):val(val){};  
};  
  
class AVLTree {  
    Node *head=nullptr;  
    void destroy(Node *node){  
        if(!node){  
            return;  
        }  
        destroy(node->left);  
        destroy(node->right);  
        delete node;  
    }  
    int get_h(Node *node){  
        return node?node->h:0;  
    }  
    void update_h(Node *node){  
        node->h=max(get_h(node->left),get_h(node->right))+1;  
    }  
    int get_balance(Node *node){  
        return node?get_h(node->left)-get_h(node->right):0;  
    }  
    Node* rotate_L(Node *node){  
        Node *tmp=node->right;  
        node->right=node->right->left;  
        tmp->left=node;  
        update_h(node);  
        update_h(tmp);  
        return tmp;  
    }  
    Node* rotate_R(Node *node){  
        Node *tmp=node->left;  
        node->left=node->left->right;  
        tmp->right=node;  
        update_h(node);  
        update_h(tmp);  
        return tmp;  
    }  
    Node* balance(Node *node){  
        int diff=get_balance(node);  
        if(abs(diff)<=1){  
            return node;  
        }  
        if(diff>1){  
            if(get_balance(node->left)<-1){  
                node->left=rotate_L(node->left);  
            }  
            node=rotate_R(node);  
        }  
        if(diff<-1){  
            if(get_balance(node->right)>1){  
                node->right=rotate_R(node->right);  
            }  
            node=rotate_L(node);  
        }  
        return node;  
    }  
    Node* insert(Node *node,int val){  
        if(!node){  
            return new Node(val);  
        }  
        if(val<node->val){  
            node->left=insert(node->left,val);  
        }else{  
            node->right=insert(node->right,val);  
        }  
        update_h(node);  
        return balance(node);  
    }  
    Node* find_min(Node *node){  
        while(node&&node->left){  
            node=node->left;  
        }  
        return node;  
    }  
    Node* del(Node *node,int val){  
        if(!node){  
            return node;  
        }  
        if(val<node->val){  
            node->left=del(node->left,val);  
        }else if(val>node->val){  
            node->right=del(node->right,val);  
        }else{  
            if(!node->left){  
                Node *tmp=node->right;  
                delete node;  
                return tmp;  
            }else if(!node->right){  
                Node *tmp=node->left;  
                delete node;  
                return tmp;  
            }  
            Node *min_node=find_min(node->right);  
            node->val=min_node->val;  
            node->right=del(node->right,min_node->val);  
        }  
        update_h(node);  
        return balance(node);  
    }  
    bool get(Node *node,int val){  
        if(!node){  
            return false;  
        }  
        if(node->val==val){  
            return true;  
        }  
        return val<node->val?get(node->left,val):get(node->right,val);  
    }  
    void in(Node *node){  
        if(!node){  
            return;  
        }  
        in(node->left);  
        cout<<node->val<<" ";  
        in(node->right);  
    }  
public:  
    AVLTree()=default;  
    AVLTree(initializer_list<int> arr):AVLTree(vector<int>(arr)){};  
    AVLTree(int *arr,int n):AVLTree(vector<int>(arr,arr+n)){};  
    AVLTree(vector<int> arr){  
        for(int num:arr){  
            head=insert(head,num);  
        }  
    }  
    ~AVLTree(){  
        destroy(head);  
    }  
    void insert_node(int val){  
        head=insert(head,val);  
    }  
    void delete_node(int val){  
        head=del(head,val);  
    }  
    bool get_node(int val){  
        return get(head,val);  
    }  
    void in_order(){  
        in(head);  
        cout<<endl;  
    }  
    friend ostream& operator<<(ostream& os,const AVLTree &t){  
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
                node->left?os<<node->left->val<<",":os<<"N,";  
                node->right?os<<node->right->val<<")"<<endl:os<<"N)"<<endl;  
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
    AVLTree t{1,2,3,4,5,6,7};  
    t.in_order();  
    cout<<t<<endl;  
    t.delete_node(4);  
    t.in_order();  
    cout<<t<<endl;  
}
```

