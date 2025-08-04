查找第一个大于等于tmp位置的索引
```cpp
int LBS(vector<int> &arr, int tmp) {
    int l = 0, r = arr.size() - 1;
    while (l <= r) {
        int mid = (l + r) / 2;
        if (arr[mid] < tmp) {
            l = mid + 1;
        } else {
            r = mid - 1;
        }
    }
    return l;
}
```
查找第一个大于tmp位置的索引
```cpp
int LBS(vector<int> &arr, int tmp) {
    int l = 0, r = arr.size() - 1;
    while (l <= r) {
        int mid = (l + r) / 2;
        if (arr[mid] <= tmp) {
            l = mid + 1;
        } else {
            r = mid - 1;
        }
    }
    return l;
}
```