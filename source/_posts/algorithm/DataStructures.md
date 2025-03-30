---
title: Data Structures Overview
date: 2023-11-22
category:
  - DataStructures
tag:
  - java
sticky: false
---

# Data Structures Overview



```mermaid
graph LR;
数据结构-->线性结构
数据结构-->逻辑结构

线性结构-->线性表
线性表-->数组Array
线性表-->链表LinkedList
线性结构-->Hash表
线性结构-->栈Stack
线性结构-->队列Queue

逻辑结构-->树Tree
逻辑结构-->堆Heap
逻辑结构-->图Graph

```

## 线性结构

### 线性表

数组

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/272164f0-a1d6-4c86-a01c-8fd0b0a89d5c/Untitled.png)

链表

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5b7a2597-d459-4fe5-a7b2-0a1dd5678e9d/Untitled.png)

### Hash表

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c152953-dd52-49ec-8401-bd755ae9e5e4/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/41478b2e-eeca-47df-bd38-bd9053bcf771/Untitled.png)

### 栈

![f1uyb7t5l4.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9b573b4f-eb04-4385-9d6f-d9030659878c/f1uyb7t5l4.gif)

### 队列

![uj2ybsqwq7.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e198ebad-7492-4a86-8242-9f93171f97d8/uj2ybsqwq7.gif)

## 逻辑结构

### 树

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/656ee0b7-2f9b-4612-9152-0171e2b1835b/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6677dddf-75de-4a33-b05c-10ac1b2b7a9a/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/17b07941-b5bb-475d-827a-050ad7db31b0/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/272f992b-55f9-4c99-bffe-b078a326a152/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/46de73c4-9bb2-4f8e-a4ae-d3797000e620/Untitled.png)

![bwwf75vdyg.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/76540342-e3e4-4ce5-8b7b-24cc65ab47d7/bwwf75vdyg.gif)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/af074197-4d5d-415d-b152-ad4d5b0e4ab3/Untitled.png)

![25stotndte.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/df60cdcd-9a1d-4c31-a371-ebb19b1e9177/25stotndte.gif)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b4623e8b-10b1-4cb9-b040-9a1c97681d83/Untitled.png)

### 堆

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1d29facd-d5fb-415c-b112-5ae89ecf0bd0/Untitled.png)

### 图

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f4a42831-d48c-4159-af87-ddc3df1c6f48/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/409a916c-d4f0-43ce-bf46-9434b28c2081/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/67c9c6ed-c391-4cc0-a9cb-6b6ee001bd61/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4918e506-61f8-4cbf-9991-cefe3752322f/Untitled.png)

```java
package org.data.structurs.graph;

import java.util.Arrays;
import java.util.Vector;

public class Graph {

    private int n;  // 节点数
    private int m;  // 边数
    private boolean directed;    // 是否为有向图
    private Vector<Integer>[] adjacency; // 图的具体数据

    // 构造函数
    public Graph(int n, boolean directed) {
        assert n >= 0;
        this.n = n;
        this.m = 0;    // 初始化没有任何边
        this.directed = directed;
        // g初始化为n个空的vector, 表示每一个g[i]都为空, 即没有任和边
        this.adjacency = (Vector<Integer>[]) new Vector[n];
        for (int i = 0; i < n; i++)
            this.adjacency[i] = new Vector<Integer>();
    }

    public int V() {
        return n;
    } // 返回节点个数

    public int E() {
        return m;
    } // 返回边的个数

    // 向图中添加一个边
    public void addEdge(int v, int w) {
        assert v >= 0 && v < n;
        assert w >= 0 && w < n;
        this.adjacency[v].add(w);
        if (v != w && !directed)
            this.adjacency[w].add(v);
        m++;
    }

    // 验证图中是否有从v到w的边
    boolean hasEdge(int v, int w) {
        assert v >= 0 && v < n;
        assert w >= 0 && w < n;
        for (int i = 0; i < this.adjacency[v].size(); i++)
            if (this.adjacency[v].elementAt(i) == w)
                return true;
        return false;
    }

    // 返回图中一个顶点的所有邻边
    // 由于java使用引用机制，返回一个Vector不会带来额外开销,
    public Iterable<Integer> adj(int v) {
        assert v >= 0 && v < n;
        return this.adjacency[v];
    }

    @Override
    public String toString() {
        return "Graph{" +
                "n=" + n +
                ", m=" + m +
                ", directed=" + directed +
                ", adjacency=" + Arrays.toString(adjacency) +
                '}';
    }

    public static void main(String[] args) {
        //初始化一个有5个节点的图
        Graph graph = new Graph(5, true);
        System.out.println(graph);
        //添加节点之间的关系
        graph.addEdge(0, 1);
        graph.addEdge(0, 2);
        graph.addEdge(0, 3);
        graph.addEdge(1, 2);
        graph.addEdge(2, 4);

        System.out.println(graph);
    }
}
```