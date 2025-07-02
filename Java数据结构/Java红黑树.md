```java
import java.util.ArrayDeque;  
import java.util.ArrayList;  
import java.util.List;  
import java.util.Queue;  
import java.util.random.RandomGenerator;  
  
enum Color {  
    RED, BLACK;  
}  
  
class Node {  
    int val;  
    Node left, right, parent;  
    Color color;  
    public Node(Color color) {  
        this.val = 0;  
        this.color = color;  
    }  
        public Node(int val) {  
        this.val = val;  
        this.color = Color.RED;  
    }  
}  
  
class RBTree {  
    Node root, nil;  
        public RBTree() {  
        nil = new Node(Color.BLACK);  
        root = nil;  
    }  
        public RBTree(List<Integer> arr) {  
        this();  
        for (int num : arr) {  
            insertNode(num);  
        }  
    }  
        private Node rotateL(Node node) {  
        Node tmp = node.right;  
        node.right = tmp.left;  
        tmp.left = node;  
        if (node.parent == nil) {  
            root = tmp;  
        } else if (node.parent.left == node) {  
            node.parent.left = tmp;  
        } else if (node.parent.right == node) {  
            node.parent.right = tmp;  
        }  
        if (node.right != nil) {  
            node.right.parent = node;  
        }  
        tmp.parent = node.parent;  
        node.parent = tmp;  
        return tmp;  
    }  
        private Node rotateR(Node node) {  
        Node tmp = node.left;  
        node.left = tmp.right;  
        tmp.right = node;  
        if (node.parent == nil) {  
            root = tmp;  
        } else if (node.parent.left == node) {  
            node.parent.left = tmp;  
        } else if (node.parent.right == node) {  
            node.parent.right = tmp;  
        }  
        if (node.left != nil) {  
            node.left.parent = node;  
        }  
        tmp.parent = node.parent;  
        node.parent = tmp;  
        return tmp;  
    }  
        boolean hasOneRed(Node node) {  
        return (node.left.color.ordinal() ^ node.right.color.ordinal()) == 1;  
    }  
        private void insertFix(Node node) {  
        if (node.parent.color == Color.BLACK) {  
            return;  
        }  
        Node parent = node.parent;  
        Node grand_parent = parent.parent;  
        if (hasOneRed(grand_parent)) {  
            if (grand_parent.left == parent) {  
                if (parent.right.color == Color.RED) {  
                    parent = rotateL(parent);  
                }  
                grand_parent = rotateR(grand_parent);  
            } else {  
                if (parent.left.color == Color.RED) {  
                    parent = rotateR(parent);  
                }  
                grand_parent = rotateL(grand_parent);  
            }  
        }  
        grand_parent.color = Color.RED;  
        grand_parent.left.color = grand_parent.right.color = Color.BLACK;  
        insertFix(grand_parent);  
    }  
        private Node insert(Node node, Node new_node) {  
        if (node == nil) {  
            return new_node;  
        }  
        if (new_node.val < node.val) {  
            node.left = insert(node.left, new_node);  
            node.left.parent = node;  
        } else {  
            node.right = insert(node.right, new_node);  
            node.right.parent = node;  
        }  
        return node;  
    }  
        public void insertNode(int val) {  
        Node new_node = new Node(val);  
        new_node.left = new_node.right = new_node.parent = nil;  
        root = insert(root, new_node);  
        insertFix(new_node);  
        root.color = Color.BLACK;  
    }  
        private Node findMinNode(Node node) {  
        while (node.left != nil) {  
            node = node.left;  
        }  
        return node;  
    }  
        private boolean hasRed(Node node) {  
        return node.left.color == Color.RED || node.right.color == Color.RED;  
    }  
        private void eraseFix(Node node) {  
        if (node == root || node.color == Color.RED) {  
            node.color = Color.BLACK;  
            return;  
        }  
        Node parent = node.parent;  
        if (hasRed(parent)) {  
            if (parent.left.color == Color.RED) {  
                parent = rotateR(parent);  
            } else {  
                parent = rotateL(parent);  
            }  
            parent.color = Color.BLACK;  
            node.parent.color = Color.RED;  
            eraseFix(node);  
            return;  
        }  
        Node bro = parent.left == node ? parent.right : parent.left;  
        if (!hasRed(bro)) {  
            bro.color = Color.RED;  
            eraseFix(parent);  
            return;  
        }  
        Color parentColor = parent.color;  
        if (parent.left == node) {  
            if (bro.right.color == Color.BLACK) {  
                rotateR(bro);  
            }  
            parent = rotateL(parent);  
        } else {  
            if (bro.left.color == Color.BLACK) {  
                rotateL(bro);  
            }  
            parent = rotateR(parent);  
        }  
        parent.color = parentColor;  
        parent.left.color = parent.right.color = Color.BLACK;  
    }  
        private Node erase(Node node, int val) {  
        if (node == nil) {  
            return node;  
        }  
        Node deleteNode;  
        if (val < node.val) {  
            deleteNode = erase(node.left, val);  
        } else if (val > node.val) {  
            deleteNode = erase(node.right, val);  
        } else {  
            if (node.left == nil || node.right == nil) {  
                return node;  
            }  
            Node minNode = findMinNode(node.right);  
            node.val = minNode.val;  
            deleteNode = erase(node.right, minNode.val);  
        }  
        return deleteNode;  
    }  
        public void eraseNode(int val) {  
        Node deleteNode = erase(root, val);  
        if (deleteNode != nil) {  
            Node tmp = deleteNode.left != nil ? deleteNode.left : deleteNode.right;  
            if (tmp != nil) {  
                tmp.parent = deleteNode.parent;  
                tmp.color = Color.BLACK;  
            }  
            if (deleteNode.color == Color.BLACK && tmp == nil) {  
                eraseFix(deleteNode);  
            }  
            if (deleteNode.parent.left == deleteNode) {  
                deleteNode.parent.left = tmp;  
            } else if (deleteNode.parent.right == deleteNode) {  
                deleteNode.parent.right = tmp;  
            }  
        }  
    }  
        public boolean check() {  
        if (root.color == Color.RED) {  
            System.out.println("违反了根节点是红色的性质");  
            return false;  
        }  
        if (!checkRed(root)) {  
            System.out.println("违反了子父节点不能都是红色的性质");  
            return false;  
        }  
        int blackNum = checkBlack(root);  
        if (blackNum == -1) {  
            System.out.println("违反了根节点到叶子节点黑色节点数量相等的性质");  
            return false;  
        }  
        if (!checkOrder(root)) {  
            System.out.println("违反了节点有序的性质");  
            return false;  
        }  
        return true;  
    }  
        private boolean checkRed(Node node) {  
        if (node == nil) {  
            return true;  
        }  
        if (node.color == Color.RED) {  
            if (node.left.color == Color.RED || node.right.color == Color.RED) {  
                return false;  
            }  
        }  
        return checkRed(node.left) && checkRed(node.right);  
    }  
        private int checkBlack(Node node) {  
        if (node == nil) {  
            return 1;  
        }  
        int left = checkBlack(node.left);  
        int right = checkBlack(node.right);  
        if (left == -1 || right == -1 || left != right) {  
            return -1;  
        }  
        return left + (node.color == Color.BLACK ? 1 : 0);  
    }  
        private boolean checkOrder(Node node) {  
        if (node == nil) {  
            return true;  
        }  
        if (node.left != nil && node.left.val > node.val) {  
            return false;  
        }  
        if (node.right != nil && node.right.val < node.val) {  
            return false;  
        }  
        return checkOrder(node.left) && checkOrder(node.right);  
    }  
        @Override  
    public String toString() {  
        StringBuilder sb = new StringBuilder("===========================\n");  
        Queue<Node> queue = new ArrayDeque<>();  
        queue.add(root);  
        while (!queue.isEmpty()) {  
            int n = queue.size();  
            sb.append("---------------------------\n");  
            for (int i = 0; i < n; i++) {  
                Node node = queue.remove();  
                sb.append("(").append(node.val).append(",")  
                .append(node.color == Color.RED ? "R" : "B").append("|")  
                .append(node.left != nil ? node.left.val : "N").append(",")  
                .append(node.right != nil ? node.right.val : "N").append(",")  
                .append(node.parent != nil ? node.parent.val : "N").append(")\n");  
                                if (node.left != nil) {  
                    queue.add(node.left);  
                }  
                if (node.right != nil) {  
                    queue.add(node.right);  
                }  
            }  
        }  
        return sb.toString();  
    }  
}  
  
public class Main {  
    public static void main(String[] args) {  
        int size = 10000;  
        int testTime = 10000;  
        int speed = 100;  
        RandomGenerator rand = RandomGenerator.getDefault();  
        for (int i = 0; i < testTime; i++) {  
            if (i % speed == 0) {  
                System.out.print(i + " ");  
            }  
            if (i % (speed * 10) == 0) {  
                System.out.println();  
            }  
            List<Integer> arr = new ArrayList<>();  
            for (int j = 0; j < size; j++) {  
                arr.add(rand.nextInt(size));  
            }  
            RBTree tree = new RBTree(arr);  
            boolean flag = tree.check();  
            if (!flag) {  
                break;  
            }  
            for (int j = 0; j < size / 2; j++) {  
                tree.eraseNode(rand.nextInt(size));  
            }  
            flag = tree.check();  
            if (!flag) {  
                break;  
            }  
        }  
    }  
}
```