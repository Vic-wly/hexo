title: yii2 框架的 orm 基础操作
author: Laiyong Wang
date: 2022-09-27 15:13:06
tags:
---
##### PS : CLASS STUDENT SCORE 默认为三张表的类

##### 单条数据查询
```
CLASS::findOne($id);
CLASS::find()->where('id='.$id)->one();
CLASS::find()->where(['id'=>$id])->one();
```
##### 多条数据查询
```
CLASS::findAll();
CLASS::find()->where(1)->offset(0)->limit(20)->all();
```
##### 单 where 条件

```
CLASS::find()->where('id='.$id)->one();
CLASS::find()->where(['id'=>$id])->one();
```
##### 多 where 条件

```
//andWhere
CLASS::find()->where('id='.$id)->andWhere('name='.$name)->one();
CLASS::find()->where(['id'=>$id])->andWhere(['name'=>$name])->one();
//与之类似
CLASS::find()->where(['id'=>$id,'name'=>$name])->one();
CLASS::find()->where(['and',['id'=>$id],['name'=>$name]])->one();

//orWhere
CLASS::find()->where('id='.$id)->orWhere('name='.$name)->one();
CLASS::find()->where(['id'=>$id])->orWhere(['name'=>$name])->one();
//与之类似
CLASS::find()->where(['or',['id'=>$id],['name'=>$name]])->one();

```
##### 子查询
```
SCORE::find('student_id')->where(['>','value',60])->andWhere(
	'student_id'=>STUDENT::find()->select('id')->where(
    [
      'or',
      ['sex'=>'男'],
      ['sex'=>'女']
      ]
    )
)
```
##### 跨表查询
```
CLASS::find()->alias(c)->where(['>',c.id,10])
	->leftJoin(['s'=>CLASS::tableName()],'c.student_id = s.id')
   ->andWhere(['s.sex','男'])
   ->andWhere(['in','s.id',[1,2,3,4,5,6]])
   ->all();
    

```
##### 复杂写法
```
CLASS::find()->Where(
    ['or',
        ['id' => $id],
        ['and',
            ['name' => $name],
            ['desc' => $desc]
        ]
    ]
)->one();

//与之类似

CLASS::find()->where(['id' => $id])->orWhere(
	[
    'and',
    ['name' => $name],
    ['desc' => $desc]
   ]
);
//与之类似
CLASS::find()->where(['id' => $id])->orWhere(
	[
    'name' => $name,
    'desc' => $desc
   ]
);

```