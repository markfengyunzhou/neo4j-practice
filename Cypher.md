## CREATE
* 创建单个点
```
CREATE (:Movie { title:"The Matrix",released:1997 })
```
```
CREATE (p:Person { name:"Keanu Reeves", born:1964 })
```
* 创建点以及关系
```
CREATE (a:Person { name:"Tom Hanks",
  born:1956 })-[r:ACTED_IN { roles: ["Forrest"]}]->(m:Movie { title:"Forrest Gump",released:1994 })
CREATE (d:Person { name:"Robert Zemeckis", born:1951 })-[:DIRECTED]->(m)
```

##  MARCH (查找)
* 查找所有movie
```
MATCH (m:Movie)
RETURN m
```
* 查找满足条件的movie
```
MATCH (p:Person { name:"Keanu Reeves" })
RETURN p
```
* 带关系的复杂查询
```
MATCH (p:Person { name:"Tom Hanks" })-[r:ACTED_IN]->(m:Movie)
RETURN m.title, r.roles
```
* 查找并创建
```
MATCH (p:Person { name:"Tom Hanks" })
CREATE (m:Movie { title:"Cloud Atlas",released:2012 })
CREATE (p)-[r:ACTED_IN { roles: ['Zachry']}]->(m)
RETURN p,r,m
```
* 查找并修改属性 
```
merge (p:Person {name: "Tom Hanks"})
on match set p.born=1700
return p
```
* 添加属性
```
MERGE (m:Movie { title:"Cloud Atlas" })
ON CREATE SET m.released = 2012
RETURN m
```
* 查找并添加属性
```
MATCH (m:Movie { title:"Cloud Atlas" })
MATCH (p:Person { name:"Tom Hanks" })
MERGE (p)-[r:ACTED_IN]->(m)
ON CREATE SET r.roles =['Zachry']
RETURN p,r,m
```
* 查找并创建关系
```
CREATE (y:Year { year:2014 })
MERGE (y)<-[:IN_YEAR]-(m10:Month { month:10 })
MERGE (y)<-[:IN_YEAR]-(m11:Month { month:11 })
RETURN y,m10,m11
```

## 索引
创建索引
```
CREATE INDEX ON :Actor(name)
```
查看索引
```
CALL db.indexes
YIELD description, label, properties;
```
## 约束
创建约束
```
CREATE CONSTRAINT ON (movie:Movie) ASSERT movie.title IS UNIQUE
```
查看约束
```
CALL db.constraints
```

## 删除
* 删除节点及其关系
```
match (p:Person)
detach delete p
```
* 删除属性
```
match (p:Person )
remove p.community
```
* 清空
```
MATCH (n)
OPTIONAL MATCH (n)-[r]-()
DELETE n,r
```

## 查询入度以及出度
```
MATCH (p:Person {telNumber:"1866165xxxx"})
WITH p, size ((p)-[:call]-> ()) AS outDegree, size ((p)<-[:call]- ()) AS inDegree
return p, inDegree, outDegree
```

## 路径
* 查询经过1-5次关系能够到达s的路径
```
match (s:Person {name:"c"})
match p=(n)-[:call \*1..5]->(s)
return p
```

## apoc
*  添加插件
```
下载jar包 https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases
放到plugins 目录下
neo4j.conf 加上 dbms.security.procedures.unrestricted=apoc.\*
重启neo4j
return apoc.version()
```
* dijkstra  
```
call apoc.help("dijkstra")

```

```
match (p0:Person {name:"0"})
match (pc:Person {name: "c"})
CALL apoc.algo.dijkstra(p0, pc, "call", "d") yield path as path, weight as weight
return path, weight

```
* 社区发现 
发现并生成社区
```
CALL apoc.algo.community(25,["Person"],'community','call','BOTH',null, 1000)

```
社区查看
```
MATCH (n:Person) 
RETURN distinct(n.community) as community,count(n) as Members,
avg(n.count) as avg_count,stdev(n.count) as count_stdev
```
