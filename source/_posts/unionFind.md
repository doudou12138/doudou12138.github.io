---
title: 并查集
category: 数据结构
---
并查集:  
主要有两个操作: 并,查  
一个场景:  
江湖各大门派,小天想要知道他和小碗是否是同一门派,他只需要知道他两的首领是否是相同的即可确定他们是否是同一门派(集合)中  
并查集就是想要解决这样的问题,一个群体分为各个部分,各个部分之间的流通性

其中:
并操作,就两个门派合并;即使两个集合连通成为一个集合
差操作,查询某人(元素)所属门派(集合)


java没有提供并查集这种数据结构,以下是一个示例:  

```
class UnionFind{
        int[] parent;
        int[] size;

        public UnionFind(int n){
            parent = new int[n];
            size = new int[n];

            for(int i=0;i<n;++i){
                parent[i]=i;
                size[i]=1;
            }
        }

        public int find(int i){
            if(parent[i]==i){
                return i;
            }
            parent[i]=find(parent[i]);
            return parent[i];
        }

        public void add(int i, int j){
            int ip = find(i);
            int jp = find(j);
            if(ip==jp){
                return;
            }
            parent[jp] = ip;
            size[ip]+=size[jp];            
        }

        public int getSize(int i){
            return size[find(i)];
        }
    }
```
