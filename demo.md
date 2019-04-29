* 创建一个雍正
```
create (:Person {name: "雍正"})
```
* 创建一个乾隆， 跟雍正创建父子关系
```
match (p:Person {name: "雍正"})
create (c:Person {name: "乾隆",alias: "弘历"})
create (p)<-[r:父亲 {role: "世袭"}]-(c)
return p,r,c
```
* 乾隆是雍正不受待见的儿子
```
match(p1:Person {name: "雍正"})
match(p2:Person {name: "乾隆"})
Create (p1)-[r:儿子 {role: "不待见"}]->(p2)
return p1,p2, r
```
* 乾隆 1736 - 1795
merge (p:Person {name:"乾隆"})
on match set p.born=1736, p.die=1795
return p
