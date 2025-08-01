```cpp
#include <iostream>
#include <vector>
using namespace std;

class SGT {
    int n;
    vector<int> tree;
    vector<int> lazy;
    void build(vector<int> arr, int left, int right, int cur) {
        if (left == right) {
            tree[cur] = arr[left];
            return;
        }
        int mid = (left + right) / 2;
        build(arr, left, mid, 2 * cur + 1);
        build(arr, mid + 1, right, 2 * cur + 2);
        tree[cur] = tree[2 * cur + 1] + tree[2 * cur + 2];
    }
    void _update(int left, int right, int cur, int idx, int val) {
        if (left == right) {
            tree[cur] += val;
            return;
        }
        int mid = (left + right) / 2;
        if (idx <= mid) {
            _update(left, mid, 2 * cur + 1, idx, val);
        } else {
            _update(mid + 1, right, 2 * cur + 2, idx, val);
        }
        tree[cur] = tree[2 * cur + 1] + tree[2 * cur + 2];
    }
    int _query(int left, int right, int cur, int start, int end) {
        if (start <= left && right <= end) {
            return tree[cur];
        }
        push_down(left, right, cur);
        int mid = (left + right) / 2;
        int res = 0;
        if (start <= mid) {
            res += _query(left, mid, 2 * cur + 1, start, end);
        }
        if (end > mid) {
            res += _query(mid + 1, right, 2 * cur + 2, start, end);
        }
        return res;
    }
    void push_down(int left, int right, int cur) {
        if (!lazy[cur]) {
            return;
        }
        int mid = (left + right) / 2;
        int left_node = 2 * cur + 1, right_node = 2 * cur + 2;
        tree[left_node] += lazy[cur] * (mid - left + 1);
        tree[right_node] += lazy[cur] * (right - mid);
        if (mid != left) {
            lazy[left_node] += lazy[cur];
        }
        if (right != mid + 1) {
            lazy[right_node] += lazy[cur];
        }
        lazy[cur] = 0;
    }
    void _update_range(int left, int right, int cur, int start, int end, int val) {
        if (start <= left && right <= end) {
            tree[cur] += (right - left + 1) * val;
            if (left != right) {
                lazy[cur] += val;
            }
            return;
        }
        push_down(left, right, cur);
        int mid = (left + right) / 2;
        if (start <= mid) {
            _update_range(left, mid, 2 * cur + 1, start, end, val);
        }
        if (end > mid) {
            _update_range(mid + 1, right, 2 * cur + 2, start, end, val);
        }
        tree[cur] = tree[2 * cur + 1] + tree[2 * cur + 2];
    }

  public:
    SGT(vector<int> arr) : n(arr.size()) {
        tree.resize(4 * arr.size(), 0);
        lazy.resize(2 * arr.size(), 0);
        build(arr, 0, arr.size() - 1, 0);
    }
    void update(int idx, int val) {
        _update(0, n - 1, 0, idx, val);
    }
    int query(int start, int end) {
        return _query(0, n - 1, 0, start, end);
    }
    void update_range(int start, int end, int val) {
        _update_range(0, n - 1, 0, start, end, val);
    }
};
```

