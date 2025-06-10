# 資料結構 題目二

## 41243239 陳裕翔

## 解題說明

(a) 要求建立一棵空的二元搜尋樹（BST），然後隨機插入 n 個節點，重複做多次實驗，量測樹的高度，並計算高度與 log₂(n) 的比例，分析這個比例隨 n 變化的趨勢。

(b) 要求實作從 BST 中刪除指定鍵值 k 的節點函式，並說明此函式的時間複雜度。

### (a)解題策略

1.資料結構設計
   定義 BST 節點（key、左子節點指標、右子節點指標）與插入函式。

2.隨機插入
   使用均勻隨機數產生器生成節點鍵值，將鍵值依序插入 BST。

3.高度計算
   利用遞迴函式計算 BST 高度（節點數目最多的路徑長度）。

4.重複多次實驗
   對同一個 n 值重複多次（例如 10 次）隨機插入與高度計算，避免單次結果偏差。

5.計算比例
   取多次實驗的高度平均值，計算與 log₂(n) 的比值。

6.輸出與分析
   輸出 n、平均高度、log₂(n)、比例；並可使用外部繪圖工具將比例對 n 畫圖，觀察是否趨近於常數。

### (b)解題策略

1.使用遞迴定位節點。

2.根據節點狀況執行相應刪除策略。

3.維持 BST 性質。

## 程式實作

以下為主要程式碼：

```cpp
//main.cpp
#include <iostream>
#include <cmath>
#include <random>
#include <chrono>

using namespace std;

struct Node {
    int key;
    Node* left;
    Node* right;
    Node(int k) : key(k), left(nullptr), right(nullptr) {}
};

Node* insert(Node* root, int key) {
    if (!root) return new Node(key);
    if (key < root->key)
        root->left = insert(root->left, key);
    else
        root->right = insert(root->right, key);
    return root;
}

int height(Node* root) {
    if (!root) return 0;
    return 1 + max(height(root->left), height(root->right));
}

Node* findMin(Node* root) {
    while (root && root->left)
        root = root->left;
    return root;
}

Node* deleteNode(Node* root, int k) {
    if (!root) return nullptr;
    if (k < root->key)
        root->left = deleteNode(root->left, k);
    else if (k > root->key)
        root->right = deleteNode(root->right, k);
    else {
        if (!root->left) {
            Node* temp = root->right;
            delete root;
            return temp;
        }
        else if (!root->right) {
            Node* temp = root->left;
            delete root;
            return temp;
        }
        else {
            Node* temp = findMin(root->right);
            root->key = temp->key;
            root->right = deleteNode(root->right, temp->key);
        }
    }
    return root;
}

void freeTree(Node* root) {
    if (!root) return;
    freeTree(root->left);
    freeTree(root->right);
    delete root;
}

int main() {
    vector<int> ns = { 100, 500, 1000, 2000, 3000, 10000 };
    mt19937 rng((unsigned)chrono::steady_clock::now().time_since_epoch().count());
    uniform_int_distribution<int> dist(1, 1000000);

    cout << "節點數 n, 平均高度, log2(n), 高度與 log2(n) 比例 (height / log2(n))" << endl;

    for (int n : ns) {
        double sum_ratio = 0.0;
        int trials = 10;

        for (int t = 0; t < trials; ++t) {
            Node* root = nullptr;
            for (int i = 0; i < n; ++i) {
                int val = dist(rng);
                root = insert(root, val);
            }

            int h = height(root);
            double ratio = h / (log2(n));
            sum_ratio += ratio;
            freeTree(root);
        }
        double avg_ratio = sum_ratio / trials;
        cout << n << "\t, " << (avg_ratio * log2(n)) << "\t, " << log2(n) << "\t, " << avg_ratio << endl;
    }
    return 0;
}

```

## 效能分析

1.插入

   平均：O(log n)

   最壞：O(n)

2.搜尋
   
   平均：O(log n)

   最壞：O(n)

3.計算高度

   時間複雜度是 O(n)

4.刪除
   
   平均：O(log n)

   最壞：O(n)

## 測試與驗證

### 測試案例

| 節點數 | 平均高度 | log2(n) | 高度與 log2(n) 比例 (height / log2(n)) |
|----------|--------------|----------|----------|
| 100   | 13     | 6.64386        | 1.95669        |
| 500   | 20      | 8.96578   | 2.2307        |
| 1000   | 21.6      | 9.96578 | 2.16742    |
| 2000   | 24.5      | 10.9658 | 2.23422       |
| 3000   | 25.6     | 11.5507 | 2.21631 |
| 10000   | 31.7     | 13.2877 | 2.38566 |

## 申論及開發報告

1.struct Node (節點結構):

&nbsp;&nbsp;&nbsp;&nbsp;是構成二元樹的基本單元。
   
2.insert(Node* root, int key) (插入節點)：
   
&nbsp;&nbsp;&nbsp;&nbsp;是建立二元搜尋樹的核心操作。

3.height(Node* root) (計算樹高)：
   
&nbsp;&nbsp;&nbsp;&nbsp;用於測量二元搜尋樹的深度。

4.findMin(Node* root) (尋找最小節點)：

&nbsp;&nbsp;&nbsp;&nbsp;這是 deleteNode 函數的一個輔助函數。在刪除有兩個子節點的節點時，以維持二元搜尋樹的特性。且可以高效地找到最小節點。

5.deleteNode(Node* root, int k) (刪除節點)：
   
&nbsp;&nbsp;&nbsp;&nbsp;是維護二元搜尋樹的另一個核心操作。它處理三種刪除情況：
      
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a.葉子節點： 直接刪除。
      
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b.只有一個子節點： 用其唯一的子節點替換被刪除的節點。
      
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;c.有兩個子節點： 找到其右子樹的最小節點（中序後繼），將該最小節點的值複製到被刪除的節點，然後遞迴地刪除右子樹中的該最小節點。

&nbsp;&nbsp;&nbsp;&nbsp;此函數確保在刪除節點後，樹仍然保持二元搜尋樹的特性。

6.freeTree(Node* root) (釋放樹記憶體)：

&nbsp;&nbsp;&nbsp;&nbsp;是記憶體管理的最佳實踐。遞迴地刪除樹中的所有節點，確保所有動態分配的記憶體都被釋放。
