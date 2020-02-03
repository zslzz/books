#tarjan
**求有向图中强连通分量的算法。**

主要辅助元素
dfs[]：遍历的点
low[]：该点以及子孙点最小的low值
stack[] :当前所求强连通分量
low[]：：最小连通的位置

```
临接表法由该点出发到所有的相连的点。

//伪代码
tarjan(u){
　　DFN[u]=Low[u]=++Index      // 为节点u设定次序编号和Low初值
　　Stack.push(u)              // 将节点u压入栈中
　　for each (u, v) in E       // 枚举每一条边
　　　　if (v is not visted)   // 如果节点v未被访问过
          tarjan(v)       // 继续向下找
　　　　　　Low[u] = min(Low[u], Low[v])   //更新这个点能指出去的最浅的时间戳
　　　　else if (v in S)       // 如果节点u还在栈内
　　　　　　Low[u] = min(Low[u], DFN[v])
 
　　if (DFN[u] == Low[u])      // 如果节点u是强连通分量的根
       do{
　　　　    v = S.pop            // 将v退栈，为该强连通分量中一个顶点
　　　　}while(u == v);

public List< ArrayList<Integer> > run(){
		for(int i=0;i<numOfNode;i++){
			if(dfn[i] == -1){
				tarjan(i);
			}
		}
		return result;
```

##求强连通分量
