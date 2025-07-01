
1、使用了DBLACK来标识被删除的位置，用来进行删除调整
```cpp
#include<bits/stdc++.h>  
using namespace std;  
  
enum Color{RED,BLACK,DBLACK};  
  
struct Node{  
    int val;  
    Color color;  
    Node *left,*right;  
    Node(int val=0,Color color=RED):val(val),color(color){};  
};  
  
class RBTree{  
    Node *root,*nil;  
    bool one_red(Node *node){  
        return node->left->color^node->right->color;  
    }  
    Node* rotate_L(Node *node){  
        Node *tmp=node->right;  
        node->right=tmp->left;  
        tmp->left=node;  
        return tmp;  
    }  
    Node* rotate_R(Node *node){  
        Node *tmp=node->left;  
        node->left=tmp->right;  
        tmp->right=node;  
        return tmp;  
    }  
    Node* insert_balance(Node *node){  
        if(!one_red(node)&&node->left->color==RED){  
            node->color=RED;  
            node->left->color=node->right->color=BLACK;  
        }else if(one_red(node)){  
            if(node->left->color==RED&&one_red(node->left)){  
                if(node->left->right->color==RED){  
                    node->left=rotate_L(node->left);  
                }  
                node=rotate_R(node);  
                node->color=RED;  
                node->left->color=node->right->color=BLACK;  
            }  
            if(node->right->color==RED&&one_red(node->right)){  
                if(node->right->left->color==RED){  
                    node->right=rotate_R(node->right);  
                }  
                node=rotate_L(node);  
                node->color=RED;  
                node->left->color=node->right->color=BLACK;  
            }  
        }  
        return node;  
    }  
    Node* insert(Node *node,int val){  
        if(node==nil){  
            Node *newNode=new Node(val);  
            newNode->left=newNode->right=nil;  
            return newNode;  
        }  
        if(val<node->val){  
            node->left=insert(node->left,val);  
        }else{  
            node->right=insert(node->right,val);  
        }  
        return insert_balance(node);  
    }  
    bool has_red(Node *node){  
        return node->left->color==RED||node->right->color==RED;  
    }  
    Node* delete_balance(Node *node){  
        if(node->left->color!=DBLACK&&node->right->color!=DBLACK){  
            return node;  
        }  
        if(has_red(node)){  
            node->color=RED;  
            if(node->left->color==RED){  
                node=rotate_R(node);  
                node->right=delete_balance(node->right);  
            }else{  
                node=rotate_L(node);  
                node->left=delete_balance(node->left);  
            }  
            node->color=BLACK;  
            return node;  
        }  
        if(node->left->color==DBLACK&&!has_red(node->right)||node->right->color==DBLACK&&!has_red(node->left)){  
            node->color=(node->color==RED)?BLACK:DBLACK;  
            node->left->color=(node->left->color==DBLACK)?BLACK:RED;  
            node->right->color=(node->right->color==DBLACK)?BLACK:RED;  
            return node;  
        }  
        if(node->right->color==DBLACK){  
            node->right->color=BLACK;  
            if(node->left->left->color!=RED){  
                node->left=rotate_L(node->left);  
            }  
            node->left->color=node->color;  
            node=rotate_R(node);  
            node->left->color=node->right->color=BLACK;  
        }else{  
            node->left->color=BLACK;  
            if(node->right->right->color!=RED){  
                node->right=rotate_R(node->right);  
            }  
            node->right->color=node->color;  
            node=rotate_L(node);  
            node->left->color=node->right->color=BLACK;  
        }  
        return node;  
    }  
    Node* del(Node *node,int val){  
        if(node==nil){  
            return node;  
        }  
        if(val<node->val){  
            node->left=del(node->left,val);  
        }else if(val>node->val){  
            node->right=del(node->right,val);  
        }else{  
            if(node->left==nil||node->right==nil){  
                Node *tmp=(node->right==nil)?node->left:node->right;  
                if(node->color==BLACK){  
                    tmp->color=(tmp->color==RED)?BLACK:DBLACK;  
                }  
                delete node;  
                return tmp;  
            }  
            Node *min_node=find_min(node->right);  
            node->val=min_node->val;  
            node->right=del(node->right,min_node->val);  
        }  
        return delete_balance(node);  
    }  
    Node* find_min(Node *node){  
        while(node!=nil&&node->left!=nil){  
            node=node->left;  
        }  
        return node;  
    }  
    void in(Node *node){  
        if(node==nil){  
            return;  
        }  
        in(node->left);  
        cout<<node->val<<" ";  
        in(node->right);  
    }  
public:  
    RBTree(){  
        nil=new Node(0,BLACK);  
        nil->left=nil->right=nil;  
        root=nil;  
    }  
    RBTree(vector<int> arr):RBTree(){  
        for(int num:arr){  
            insert_node(num);  
        }  
    }  
    void insert_node(int val){  
        root=insert(root,val);  
        root->color=BLACK;  
    }  
    void delete_node(int val){  
        root=del(root,val);  
        root->color=BLACK;  
    }  
    void in_order(){  
        in(root);  
        cout<<endl;  
    }  
    bool check_red(Node *node){  
        if(node==nil){  
            return true;  
        }  
        if(node->color==RED){  
            if(node->left->color==RED||node->right->color==RED){  
                return false;  
            }  
        }  
        return check_red(node->left)&&check_red(node->right);  
    }  
    int check_black(Node *node){  
        if(node==nil){  
            return 0;  
        }  
        int left=check_black(node->left);  
        int right=check_black(node->right);  
        if(left==-1||right==-1){  
            return -1;  
        }  
        if(left!=0&&right!=0&&left!=right){  
            return -1;  
        }  
        return left+(node->color==BLACK?1:0);  
    }  
    bool check_bst(Node *node,int min_val,int max_val){  
        if(node==nil){  
            return true;  
        }  
        if(node->val<min_val||node->val>max_val){  
            return false;  
        }  
        return check_bst(node->left,min_val,node->val)&&check_bst(node->right,node->val,max_val);  
    }  
    bool check(){  
        if(root->color==RED){  
            cout<<"违反了根节点为黑色的性质"<<endl;  
            return false;  
        }  
        if(!check_red(root)){  
            cout<<"违反了两个红色节点不能在一起的性质"<<endl;  
            return false;  
        }  
        if(check_black(root)==-1){  
            cout<<"违反了黑色节点的数量相等的性质"<<endl;  
            return false;  
        }  
        if(!check_bst(root,INT_MIN,INT_MAX)){  
            cout<<"违反了平衡树的性质"<<endl;  
            return false;  
        }  
        return true;  
    }  
    friend ostream& operator<<(ostream& os,const RBTree &tree){  
        os<<"======================"<<endl;  
        queue<Node*> q;  
        q.push(tree.root);  
        while(!q.empty()){  
            int n=q.size();  
            os<<"-----------------------"<<endl;  
            for(int i=0;i<n;i++){  
                Node *node=q.front();  
                q.pop();  
                os<<"("<<node->val<<",";  
                os<<(node->color==RED?"R":"B")<<"|";  
                os<<(node->left==tree.nil?"N":to_string(node->left->val))<<",";  
                os<<(node->right==tree.nil?"N":to_string(node->right->val))<<")";  
                os<<endl;  
                if(node->left!=tree.nil){  
                    q.push(node->left);  
                }  
                if(node->right!=tree.nil){  
                    q.push(node->right);  
                }  
            }  
        }  
        os<<"======================"<<endl;  
        return os;  
    }  
};
```


2、使用parent指针，不使用DBLACK
```cpp
#include <iostream>  
#include <queue>  
#include <random>  
#include <string>  
#include <vector>  
  
using namespace std;  
  
enum Color {  
    RED,  
    BLACK  
};  
  
struct Node {  
    int val;  
    Color color;  
    Node *left, *right, *parent;  
    Node(int val, Color color = RED) : val(val), color(color), left(nullptr), right(nullptr), parent(nullptr) {};  
};  
  
class RBTree {  
    Node *root, *nil;  
    void clear(Node* node) {  
        if (node == nil) {  
            return;  
        }  
        clear(node->left);  
        clear(node->right);  
        delete node;  
    }  
    Node* insert(Node* node, Node* new_node) {  
        if (node == nil) {  
            return new_node;  
        }  
        if (new_node->val < node->val) {  
            node->left = insert(node->left, new_node);  
            node->left->parent = node;  
        } else {  
            node->right = insert(node->right, new_node);  
            node->right->parent = node;  
        }  
        return node;  
    }  
    void insert_fix(Node* node) {  
        if (node->parent->color == BLACK) {  
            return;  
        }  
        Node* parent = node->parent;  
        Node* grand_parent = parent->parent;  
        Node* bro;  
        if (grand_parent->left == parent) {  
            bro = grand_parent->right;  
            if (bro->color == BLACK) {  
                if (parent->right == node) {  
                    parent = rotate_L(parent);  
                }  
                grand_parent = rotate_R(grand_parent);  
            }  
        } else {  
            bro = grand_parent->left;  
            if (bro->color == BLACK) {  
                if (parent->left == node) {  
                    parent = rotate_R(parent);  
                }  
                grand_parent = rotate_L(grand_parent);  
            }  
        }  
        grand_parent->color = RED;  
        grand_parent->left->color = grand_parent->right->color = BLACK;  
        insert_fix(grand_parent);  
    }  
    Node* rotate_L(Node* node) {  
        Node* tmp = node->right;  
        node->right = tmp->left;  
        tmp->left = node;  
        if (node->parent == nil) {  
            root = tmp;  
        } else if (node->parent->left == node) {  
            node->parent->left = tmp;  
        } else if (node->parent->right == node) {  
            node->parent->right = tmp;  
        }  
        if (node->right != nil) {  
            node->right->parent = node;  
        }  
        tmp->parent = node->parent;  
        node->parent = tmp;  
        return tmp;  
    }  
    Node* rotate_R(Node* node) {  
        Node* tmp = node->left;  
        node->left = tmp->right;  
        tmp->right = node;  
        if (node->parent == nil) {  
            root = tmp;  
        } else if (node->parent->left == node) {  
            node->parent->left = tmp;  
        } else if (node->parent->right == node) {  
            node->parent->right = tmp;  
        }  
        if (node->left != nil) {  
            node->left->parent = node;  
        }  
        tmp->parent = node->parent;  
        node->parent = tmp;  
        return tmp;  
    }  
    Node* find_min_node(Node* node) {  
        while (node->left != nil) {  
            node = node->left;  
        }  
        return node;  
    }  
    bool has_red(Node* node) {  
        return node->left->color == RED || node->right->color == RED;  
    }  
    void erase_fix(Node* node, Node* parent) {  
        if (node == root || node->color == RED) {  
            return;  
        }  
        if (parent->left == node) {  
            Node* bro = parent->right;  
            if (bro->color == BLACK) {  
                if (has_red(bro)) {  
                    Color parent_color = parent->color;  
                    if (bro->right->color == RED) {  
                        parent = rotate_L(parent);  
                    } else {  
                        bro = rotate_R(bro);  
                        parent = rotate_L(parent);  
                    }  
                    parent->color = parent_color;  
                    parent->left->color = parent->right->color = BLACK;  
                } else {  
                    bro->color = RED;  
                    parent->color = BLACK;  
                    erase_fix(parent, parent->parent);  
                }  
            } else {  
                parent = rotate_L(parent);  
                parent->color = BLACK;  
                parent->left->color = RED;  
                erase_fix(node, node->parent);  
            }  
        } else {  
            Node* bro = parent->left;  
            if (bro->color == BLACK) {  
                if (has_red(bro)) {  
                    Color parent_color = parent->color;  
                    if (bro->left->color == RED) {  
                        parent = rotate_R(parent);  
                    } else {  
                        bro = rotate_L(bro);  
                        parent = rotate_R(parent);  
                    }  
                    parent->color = parent_color;  
                    parent->left->color = parent->right->color = BLACK;  
                } else {  
                    bro->color = RED;  
                    parent->color = BLACK;  
                    erase_fix(parent, parent->parent);  
                }  
            } else {  
                parent = rotate_R(parent);  
                parent->color = BLACK;  
                parent->right->color = RED;  
                erase_fix(node, node->parent);  
            }  
        }  
    }  
    Node* erase(Node* node, int val) {  
        if (node == nil) {  
            return node;  
        }  
        if (val < node->val) {  
            node->left = erase(node->left, val);  
            int a = 1;  
        } else if (val > node->val) {  
            node->right = erase(node->right, val);  
            int b = 1;  
        } else {  
            if (node->left == nil || node->right == nil) {  
                Node* tmp = node->left == nil ? node->right : node->left;  
                if (tmp != nil) {  
                    tmp->parent = node->parent;  
                    tmp->color = BLACK;  
                }  
                if (node->color == BLACK && tmp == nil) {  
                    erase_fix(node, node->parent);  
                }  
                delete node;  
                return tmp;  
            } else {  
                Node* min_node = find_min_node(node->right);  
                node->val = min_node->val;  
                node->right = erase(node->right, min_node->val);  
            }  
        }  
        return node;  
    }  
    public:  
    RBTree() {  
        nil = new Node(0, BLACK);  
        nil->left = nil->right = nil->parent = nil;  
        root = nil;  
    }  
    RBTree(vector<int> arr) : RBTree() {  
        for (int num : arr) {  
            insert_node(num);  
        }  
    }  
    ~RBTree() {  
        clear(root);  
        delete nil;  
    }  
    void insert_node(int val) {  
        Node* new_node = new Node(val);  
        new_node->left = new_node->right = new_node->parent = nil;  
        root = insert(root, new_node);  
        insert_fix(new_node);  
        root->color = BLACK;  
    }  
    void erase_node(int val) {  
        erase(root, val);  
        root->color = BLACK;  
    }  
    friend ostream& operator<<(ostream& os, const RBTree& tree) {  
        os << "===========================" << endl;  
        queue<Node*> q;  
        q.push(tree.root);  
        while (!q.empty()) {  
            int n = q.size();  
            os << "---------------------------" << endl;  
            for (int i = 0; i < n; i++) {  
                Node* node = q.front();  
                q.pop();  
                os << "(" << node->val << ",";  
                os << (node->color == RED ? "R" : "B") << "|";  
                os << (node->left == tree.nil ? "N" : to_string(node->left->val)) << ",";  
                os << (node->right == tree.nil ? "N" : to_string(node->right->val)) << ",";  
                os << (node->parent == tree.nil ? "N" : to_string(node->parent->val)) << ")" << endl;  
                if (node->left != tree.nil) {  
                    q.push(node->left);  
                }  
                if (node->right != tree.nil) {  
                    q.push(node->right);  
                }  
            }  
        }  
        os << "===========================" << endl;  
        return os;  
    }  
    bool check() {  
        if (root->color == RED) {  
            cout << "违反了根节点是红色的性质" << endl;  
            return false;  
        }  
        if (!check_red(root)) {  
            cout << "违反了子父节点不能都是红色的性质" << endl;  
            return false;  
        }  
        int black_num = check_black(root);  
        if (black_num == -1) {  
            cout << "违反了根节点到叶子节点黑色节点数量相等的性质" << endl;  
            return false;  
        }  
        if (!check_order(root)) {  
            cout << "违反了节点有序的性质" << endl;  
            return false;  
        }  
        return true;  
    }  
    private:  
    bool check_red(Node* node) {  
        if (node == nil) {  
            return true;  
        }  
        if (node->color == RED) {  
            if (node->left->color == RED || node->right->color == RED) {  
                return false;  
            }  
        }  
        return check_red(node->left) && check_red(node->right);  
    }  
    int check_black(Node* node) {  
        if (node == nil) {  
            return 0;  
        }  
        int left = check_black(node->left);  
        int right = check_black(node->right);  
        if (left == -1 || right == -1) {  
            return -1;  
        }  
        if (left != 0 && right != 0 && left != right) {  
            return -1;  
        }  
        return left + (node->color == BLACK ? 1 : 0);  
    }  
    bool check_order(Node* node) {  
        if (node == nil) {  
            return true;  
        }  
        if (node->left != nil && node->left->val > node->val) {  
            return false;  
        }  
        if (node->right != nil && node->right->val < node->val) {  
            return false;  
        }  
        return check_order(node->left) && check_order(node->right);  
    }  
};  
  
int main() {  
    mt19937 seed(time(nullptr));  
    uniform_int_distribution<int> gen(1, 9);  
    vector<int> arr{1, 2, 3, 4, 5, 6, 7, 8, 9};  
    RBTree tree(arr);  
    cout << tree << endl;  
    vector<int> arr1{8};  
    for (int i = 0; i < arr1.size(); i++) {  
        cout << "开始删除：" << arr1[i] << endl;  
        tree.erase_node(arr1[i]);  
        tree.check();  
        cout << tree << endl;  
    }  
    return 0;  
}
```