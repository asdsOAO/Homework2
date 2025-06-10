# 資料結構 題目一

## 41243239 陳裕翔

## 解題說明

這題要求我們寫一個最小優先佇列（MinPQ）的抽象類別，並用最小堆（MinHeap）來實作它。

### 抽象類別 MinPQ 定義了最小優先佇列的基本功能：

1.判斷是否空的 IsEmpty()

2.取出最小元素 Top()

3.插入元素 Push()

4.刪除最小元素 Pop()

### MinHeap 實作 使用陣列（vector）儲存元素，並保持二元最小堆的特性：

1.新增元素後往上調整（swim），保持堆序

2.刪除最小元素後往下調整（sink），保持堆序

這樣插入和刪除元素的時間複雜度都是 O(log n)，取出最小元素是 O(1)，符合題目要求。

## 程式實作

以下為主要程式碼：
```cpp
//MinHeap.h
#pragma once
#ifndef MINHEAP_H
#define MINHEAP_H

#include "MinPQ.h"
#include <vector>
#include <stdexcept>

template <class T>
class MinHeap : public MinPQ<T> {
private:
    std::vector<T> heap;

    // 向上調整
    void Swim(int k) {
        while (k > 0 && heap[k] < heap[(k - 1) / 2]) {
            std::swap(heap[k], heap[(k - 1) / 2]);
            k = (k - 1) / 2;
        }
    }

    // 向下調整
    void Sink(int k) {
        int n = heap.size();
        while (2 * k + 1 < n) {
            int j = 2 * k + 1;
            if (j + 1 < n && heap[j + 1] < heap[j]) j++;
            if (heap[k] <= heap[j]) break;
            std::swap(heap[k], heap[j]);
            k = j;
        }
    }

public:
    MinHeap() {}
    ~MinHeap() override {}

    bool IsEmpty() const override {
        return heap.empty();
    }

    const T& Top() const override {
        if (heap.empty())
            throw std::underflow_error("MinHeap is empty");
        return heap[0];
    }

    void Push(const T& item) override {
        heap.push_back(item);
        Swim(heap.size() - 1);            // O(log n)
    }

    void Pop() override {
        if (heap.empty())
            throw std::underflow_error("MinHeap is empty");
        std::swap(heap[0], heap.back());
        heap.pop_back();
        if (!heap.empty())
            Sink(0);                       // O(log n)
    }
};

#endif // MINHEAP_H

```
```cpp
//MinPQ.h
#pragma once
#ifndef MINPQ_H
#define MINPQ_H

template <class T>
class MinPQ {
public:
    MinPQ() {}
    virtual ~MinPQ() {}

    // 回傳 true 當且僅當佇列為空
    virtual bool IsEmpty() const = 0;

    // 取得最小元素
    virtual const T& Top() const = 0;

    // 插入一個元素
    virtual void Push(const T& item) = 0;

    // 刪除最小元素
    virtual void Pop() = 0;
};

#endif // MINPQ_H

```
```cpp
//main.cpp
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <chrono>
#include <stdexcept>

template <class T>
class MinPQ {
public:
    MinPQ() {}
    virtual ~MinPQ() {}

    virtual bool IsEmpty() const = 0;
    virtual const T& Top() const = 0;
    virtual void Push(const T& item) = 0;
    virtual void Pop() = 0;
};

template <class T>
class MinHeap : public MinPQ<T> {
private:
    std::vector<T> heap;

    void Swim(int k) {
        while (k > 0 && heap[k] < heap[(k - 1) / 2]) {
            std::swap(heap[k], heap[(k - 1) / 2]);
            k = (k - 1) / 2;
        }
    }

    void Sink(int k) {
        int n = heap.size();
        while (2 * k + 1 < n) {
            int j = 2 * k + 1;
            if (j + 1 < n && heap[j + 1] < heap[j]) j++;
            if (heap[k] <= heap[j]) break;
            std::swap(heap[k], heap[j]);
            k = j;
        }
    }

public:
    MinHeap() {}
    ~MinHeap() override {}

    bool IsEmpty() const override {
        return heap.empty();
    }

    const T& Top() const override {
        if (heap.empty())
            throw std::underflow_error("MinHeap is empty");
        return heap[0];
    }

    void Push(const T& item) override {
        heap.push_back(item);
        Swim(heap.size() - 1);
    }

    void Pop() override {
        if (heap.empty())
            throw std::underflow_error("MinHeap is empty");
        std::swap(heap[0], heap.back());
        heap.pop_back();
        if (!heap.empty())
            Sink(0);
    }
};

int main() {
    const int NUM_GROUPS = 5;
    const int NUM_ELEMENTS = 100000; // 增大到十萬筆

    srand(static_cast<unsigned int>(time(nullptr)));

    for (int group = 1; group <= NUM_GROUPS; ++group) {
        MinHeap<int> pq;

        auto start = std::chrono::high_resolution_clock::now();

        for (int i = 0; i < NUM_ELEMENTS; ++i) {
            int val = rand() % 1000000;
            pq.Push(val);
        }

        while (!pq.IsEmpty()) {
            pq.Pop();
        }

        auto end = std::chrono::high_resolution_clock::now();

        // 用微秒計算時間
        auto duration_us = std::chrono::duration_cast<std::chrono::microseconds>(end - start).count();

        std::cout << "第 " << group << " 組，處理 " << NUM_ELEMENTS << " 項隨機數，耗時："
            << duration_us << " 微秒" << std::endl;
    }

    return 0;
}


```

## 效能分析

1. Push(const T& item) — 插入元素

    時間複雜度： O(log n)

   空間複雜度： O(1)

2. Pop() — 刪除最小元素

   時間複雜度： O(log n)

   空間複雜度： O(1)

3. Top() — 取得最小元素

   時間複雜度： O(1)

   空間複雜度： O(1)

4. IsEmpty() — 判斷是否空佇列

   時間複雜度： O(1)

   空間複雜度： O(1)

5. 整體空間複雜度

   空間複雜度： O(n)

## 測試與驗證

### 測試案例

| 測試案例 | 處理n項隨機數 | 耗時(微秒) | 
|----------|--------------|----------|
| 測試一   | $n = 100000$      | 67026 | 
| 測試二   | $n = 100000$      | 72566 |
| 測試三   | $n = 100000$      | 70430 | 
| 測試四   | $n = 100000$      | 67670 |
| 測試五   | $n = 100000$     | 68306 |

## 申論及開發報告

1. 資料結構選擇：
使用二元最小堆（MinHeap），以std::vector當作底層儲存結構。這樣能快速存取父子節點位置，節省空間且效率高。

2. 演算法設計：

   Swim（上浮）：插入新元素時，往上調整保持堆序性。

   Sink（下沉）：刪除最小元素後，從根節點向下調整保持堆序性。
兩者時間複雜度都是 O(log n)，效率優於線性搜尋。
