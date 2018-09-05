---
title: "Mybatis中Foreach的用法"
date: 2018-08-31T11:05:33+08:00
description: ""
thumbnail: "img/tn.png"
categories:
  - "Mybatis"
tags:
  - "foreach"
---

# foreach属性

|属性|描述|
|:---|:---|
|item |	循环体中的具体对象。支持属性的点路径访问，如item.age,item.info.details。具体说明：若collection属性为list或array，则item代表list或array里面的一个元素。若collection属性对应一个map，则item代表的是map中的value集合中的单个value, 该参数为必选。|
|collection| foreach遍历的对象，作为入参时，List对象默认用list代替作为键，数组对象有array代替作为键，Map对象没有默认的键。也就是传入的集合（list，array，map）的名字，这个名字可以在foreach里面随便引用），当然在作为入参时可以使用@Param("params")来设置键，设置keyName后，list,array将会失效。 除了入参这种情况外，还有一种作为参数对象的某个字段的时候。举个例子：如果User有属性List ids。入参是User对象，那么这个collection = "ids" 如果User有属性Ids ids;其中Ids是个对象，Ids有个属性List id;入参是User对象，那么collection = "ids.id" 如果传入参数类型为map，这个入参有注解@Param("params")，则map的所有的key集合可以写成params.keys，所有值集合可以写成params.values。这样foreach就可以对key集合或值集合进行迭代了。上面只是举例，具体collection等于什么，就看你想对那个元素做循环。该参数为必选。|
|separator   | 元素之间的分隔符，例如在in()的时候，separator=","会自动在元素中间用“,“隔开，避免手动输入逗号导致sql错误，如in(1,2,)这样。该参数可选。  |
|open   | foreach代码的开始符号，一般是(和close=")"合用。常用在in(),values()时。该参数可选。  |
|close   | foreach代码的关闭符号，一般是)和open="("合用。常用在in(),values()时。该参数可选。  |
|index   |  在list和数组中,index是元素的序号，在map中，index是元素的key，该参数可选。
 |

```
<!--List:forech中的collection属性类型是List,collection的值必须是:list,item的值可以随意,Dao接口中参数名字随意 -->
<select id="getEmployeesListParams" resultType="Employees">
    select *
    from EMPLOYEES e
    where e.EMPLOYEE_ID in
    <foreach collection="list" item="employeeId" index="index"
        open="(" close=")" separator=",">
        #{employeeId}
    </foreach>
</select>

<!--Array:forech中的collection属性类型是array,collection的值必须是:list,item的值可以随意,Dao接口中参数名字随意 -->
<select id="getEmployeesArrayParams" resultType="Employees">
    select *
    from EMPLOYEES e
    where e.EMPLOYEE_ID in
    <foreach collection="array" item="employeeId" index="index"
        open="(" close=")" separator=",">
        #{employeeId}
    </foreach>
</select>

<!--Map:不单单forech中的collection属性是map.key,其它所有属性都是map.key,比如下面的departmentId -->
<select id="getEmployeesMapParams" resultType="Employees">
    select *
    from EMPLOYEES e
    <where>
        <if test="departmentId!=null and departmentId!=''">
            e.DEPARTMENT_ID=#{departmentId}
        </if>
        <if test="employeeIdsArray!=null and employeeIdsArray.length!=0">
            AND e.EMPLOYEE_ID in
            <foreach collection="employeeIdsArray" item="employeeId"
                index="index" open="(" close=")" separator=",">
                #{employeeId}
            </foreach>
        </if>
    </where>
</select>
```
