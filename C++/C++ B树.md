```cpp
#include<bits/stdc++.h>  
using namespace std;  
  
struct Node{  
    int n;  
    int *val;  
    Node **next;  
    Node(int m,int n=0):n(n),val(new int[m]()),next(new Node*[m+1]()){};  
    ~Node(){  
        delete[] val;  
        delete[] next;  
    }  
};  
  
class BTree{  
    Node *root;  
    int m;  
    void clear(Node *node){  
        if(!node){  
            return;  
        }  
        for(int i=0;i<=node->n;i++){  
            clear(node->next[i]);  
        }  
        delete node;  
    }  
    Node* insert_fix(Node *node,Node *child,int pos){  
        if(child->n<m){  
            return node;  
        }  
        int k=m/2,idx=0;  
        Node *node1=new Node(m,k);  
        Node *node2=new Node(m,m-k-1);  
        for(int i=0;i<k;i++){  
            node1->val[i]=child->val[idx];  
            node1->next[i]=child->next[idx++];  
        }  
        node1->next[k]=child->next[idx++];  
        for(int i=0;i<m-k-1;i++){  
            node2->val[i]=child->val[idx];  
            node2->next[i]=child->next[idx++];  
        }  
        node2->next[m-k-1]=child->next[idx];  
        for(int i=node->n;i>pos;i--){  
            node->val[i]=node->val[i-1];  
            node->next[i+1]=node->next[i];  
        }  
        node->val[pos]=child->val[k];  
        node->next[pos]=node1;  
        node->next[pos+1]=node2;  
        node->n++;  
        delete child;  
        return node;  
    }  
    void insert(Node *node,int val){  
        int pos=node->n;  
        if(!node->next[0]){  
            for(;pos>0&&node->val[pos-1]>val;pos--){  
                node->val[pos]=node->val[pos-1];  
            }  
            node->val[pos]=val;  
            node->n++;  
            return;  
        }  
        while(pos>0&&node->val[pos-1]>val){  
            pos--;  
        }  
        insert(node->next[pos],val);  
        node=insert_fix(node,node->next[pos],pos);  
        return;  
    }  
    void rotate_L(Node *node,int pos){  
        Node *left=node->next[pos],*right=node->next[pos+1];  
        int ln=left->n,rn=right->n;  
        left->val[ln]=node->val[pos];  
        left->next[ln+1]=right->next[0];  
        left->n++;  
        node->val[pos]=right->val[0];  
        for(int i=1;i<right->n;i++){  
            right->val[i-1]=right->val[i];  
            right->next[i-1]=right->next[i];  
        }  
        right->next[rn-1]=right->next[rn];  
        right->n--;  
    }  
    void rotate_R(Node *node,int pos){  
        Node *left=node->next[pos],*right=node->next[pos+1];  
        int ln=left->n,rn=right->n;  
        right->next[rn+1]=right->next[rn];  
        for(int i=rn;i>0;i--){  
            right->val[i]=right->val[i-1];  
            right->next[i]=right->next[i-1];  
        }  
        right->val[0]=node->val[pos];  
        right->next[0]=left->next[ln];  
        right->n++;  
        node->val[pos]=left->val[ln-1];  
        left->n--;  
    }  
    void merge(Node *node,int pos){  
        Node *left=node->next[pos],*right=node->next[pos+1];  
        int ln=left->n,rn=right->n;  
        Node *new_node=new Node(m,ln+rn+1);  
        int idx=0;  
        for(int i=0;i<ln;i++){  
            new_node->val[idx]=left->val[i];  
            new_node->next[idx++]=left->next[i];  
        }  
        new_node->val[idx]=node->val[pos];  
        new_node->next[idx++]=left->next[ln];  
        for(int i=0;i<rn;i++){  
            new_node->val[idx]=right->val[i];  
            new_node->next[idx++]=right->next[i];  
        }  
        new_node->next[idx]=right->next[rn];  
        for(int i=pos+1;i<node->n;i++){  
            node->val[i-1]=node->val[i];  
            node->next[i]=node->next[i+1];  
        }  
        node->next[pos]=new_node;  
        node->n--;  
        delete left;  
        delete right;  
    }  
    Node* erase_fix(Node *node,int pos){  
        if(node->next[pos]->n>=(m-1)/2){  
            return node;  
        }  
        if(pos>0&&node->next[pos-1]->n>(m-1)/2){  
            rotate_R(node,pos-1);  
        }else if(pos<node->n&&node->next[pos+1]->n>(m-1)/2){  
            rotate_L(node,pos);  
        }else{  
            if(pos>0){  
                merge(node,pos-1);  
            }else{  
                merge(node,pos);  
            }  
        }  
        return node;  
    }  
    void erase(Node *node,int val){  
        int pos=0;  
        while(pos<node->n&&node->val[pos]<val){  
            pos++;  
        }  
        if(!node->next[0]){  
            if(pos<node->n&&node->val[pos]==val){  
                for(int i=pos+1;i<node->n;i++){  
                    node->val[i-1]=node->val[i];  
                }  
                node->n--;  
            }  
            return;  
        }  
        if(pos<node->n&&node->val[pos]==val){  
            Node *tmp=find_max_node(node->next[pos]);  
            int key=tmp->val[tmp->n-1];  
            tmp->val[tmp->n-1]=node->val[pos];  
            node->val[pos]=key;  
        }  
        erase(node->next[pos],val);  
        node=erase_fix(node,pos);  
        return;  
    }  
    Node* find_max_node(Node *node){  
        while(node&&node->next[node->n]){  
            node=node->next[node->n];  
        }  
        return node;  
    }  
public:  
    BTree(int m=5):m(m){  
        root=new Node(m);  
    }  
    BTree(vector<int> arr,int m=5):BTree(m){  
        for(int num:arr){  
            insert_node(num);  
        }  
    }  
    ~BTree(){  
        clear(root);  
    }  
    void insert_node(int val){  
        insert(root,val);  
        if(root->n>=m){  
            Node *new_root=new Node(m);  
            new_root->next[0]=root;  
            root=insert_fix(new_root,root,0);  
        }  
    }  
    void erase_node(int val){  
        erase(root,val);  
        if(root->n==0){  
            Node *new_root=root->next[0];  
            delete root;  
            root=new_root;  
        }  
    }  
    bool check(){  
        queue<Node*> q;  
        q.push(root);  
        while(!q.empty()){  
            int n=q.size();  
            bool f1=true,f2=true;  
            for(int i=0;i<n;i++){  
                Node *node=q.front();  
                q.pop();  
                if(n==1){  
                    if(!check_root_num(node)){  
                        cout<<"根节点违反了节点的值的数量的性质"<<endl;  
                        return false;  
                    }  
                }else{  
                    if(!check_num(node)){  
                        cout<<"违反了节点的值的数量的性质"<<endl;  
                        return false;  
                    }  
                }  
                if(i==0){  
                    f1=node->next[0]==nullptr;  
                }else{  
                    f2=node->next[0]==nullptr;  
                    if(f1!=f2){  
                        cout<<"违反了叶子节点都在同一层的性质"<<endl;  
                        return false;  
                    }  
                }  
                if(!check_val(node)){  
                    cout<<"根节点违反了节点的值有序的性质"<<endl;  
                    return false;  
                }  
                if(!check_order(node)){  
                    cout<<"违反了节点有序的性质"<<endl;  
                    return false;  
                }  
                for(int j=0;j<=node->n;j++){  
                    if(node->next[j]){  
                        q.push(node->next[j]);  
                    }  
                }  
            }  
        }  
        return true;  
    }  
    friend ostream& operator<<(ostream& os,const BTree &tree){  
        os<<"=================="<<endl;  
        queue<Node*> q;  
        q.push(tree.root);  
        while(!q.empty()){  
            int n=q.size();  
            os<<"-----------------------"<<endl;  
            for(int i=0;i<n;i++){  
                Node *node=q.front();  
                q.pop();  
                os<<"(";  
                for(int j=0;j<node->n;j++){  
                    os<<node->val[j];  
                    if(j!=node->n-1){  
                        os<<",";  
                    }  
                }  
                os<<"|";  
                for(int j=0;j<=node->n;j++){  
                    os<<((node->next[j])?to_string(node->next[j]->val[0]):"N");  
                    if(j!=node->n){  
                        os<<",";  
                    }  
                }  
                os<<")"<<endl;  
                for(int j=0;j<=node->n;j++){  
                    if(node->next[j]){  
                        q.push(node->next[j]);  
                    }  
                }  
            }  
        }  
        os<<"=================="<<endl;  
        return os;  
    }  
private:  
    bool check_num(Node *node){  
        return node->n<=m-1&&node->n>=(m-1)/2;  
    }  
    bool check_root_num(Node *node){  
        return node->n<=m-1&&node->n>=1;  
    }  
    bool check_val(Node *node){  
        for(int i=1;i<node->n;i++){  
            if(node->val[i-1]>node->val[i]){  
                return false;  
            }  
        }  
        return true;  
    }  
    bool check_order(Node *node){  
        if(!node->next[0]){  
            return true;  
        }  
        for(int i=0;i<node->n;i++){  
            int key=node->val[i],ln=node->next[i]->n;  
            if(key<node->next[i]->val[ln-1]&&key>node->next[i+1]->val[0]){  
                return false;  
            }  
        }  
        return true;  
    }  
};  
  
int main(){  
    int size=10000;  
    int test_time=10000;  
    int speed=100;  
    mt19937 seed(time(nullptr));  
    uniform_int_distribution<int> gen(0,size);  
    uniform_int_distribution<int> gen1(0,size);  
    for(int i=0;i<test_time;i++){  
        if(i%speed==0){  
            cout<<i<<" ";  
        }  
        if(i%(speed*10)==0){  
            cout<<endl;  
        }  
        vector<int> arr;  
        for(int j=0;j<=size;j++){  
            arr.push_back(gen(seed));  
        }  
        BTree tree(arr);  
        bool flag=tree.check();  
        if(!flag){  
            break;  
        }  
        for(int i=0;i<size/2;i++){  
            tree.erase_node(arr[gen1(seed)]);  
        }  
        flag=tree.check();  
        if(!flag){  
            break;  
        }  
    }  
    return 0;  
}
```