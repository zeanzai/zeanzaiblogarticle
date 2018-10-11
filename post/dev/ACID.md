---
title: "ACID"
date: 2018-10-11T11:09:31+08:00
description: "ACID"
keywords: "ACID"
categories:
  - "Dev"
tags:
  - "ACID"
---

## 事务概述

​     数据库事务是指逻辑工作单元执行的一系列操作，具有要么全部执行成功，要么全部执行不成功的特点。通过将一系列相关操作组合为一个要么完全执行成功，要么完全执行不成功的单元，可以有效的简化错误恢复从而提高系统的可靠性。一个逻辑工作单元要想成为事务，就必须具备所谓的ACID属性。

## ACID属性概述

​     1.A（原子性：Atomicity）

​          整个事务要么全部执行成功，要么失败，不会存在滞留在其中某一个环节上的情况。例如：在一个事务中，存在着三个操作：查询A表数据、更新B表数据、删除C表数据，如果查询A成功，更新B成功，删除C失败，那么整个事务就不会执行成功，只会回滚到执行事务之前的状态，不会存在查询A成功，更新B成功，但是删除C失败的过程。

​     2.C（一致性：Consistency）

#### 原子性

编辑

整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被<a href="http://baike.baidu.com/item/%E5%9B%9E%E6%BB%9A" target="_blank">回滚</a>（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

#### 一致性

编辑

一个事务可以封装状态改变（除非它是一个只读的）。事务必须始终保持系统处于一致的状态，不管在任何给定的时间<a target="_blank" href="http://baike.baidu.com/item/%E5%B9%B6%E5%8F%91">并发</a>事务有多少。

也就是说：如果事务是<a target="_blank" href="http://baike.baidu.com/item/%E5%B9%B6%E5%8F%91">并发</a>多个，系统也必须如同串行事务一样操作。其主要特征是保护性和不变性(Preserving an Invariant)，以转账<a target="_blank" href="http://baike.baidu.com/item/%E6%A1%88%E4%BE%8B">案例</a>为例，假设有五个账户，每个账户余额是100元，那么五个账户总额是500元，如果在这个5个账户之间同时发生多个转账，无论<a target="_blank" href="http://baike.baidu.com/item/%E5%B9%B6%E5%8F%91">并发</a>多少个，比如在A与B账户之间转账5元，在C与D账户之间转账10元，在B与E之间转账15元，五个账户总额也应该还是500元，这就是保护性和不变性

#### 隔离性

编辑

隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。这种属性有时称为<a target="_blank" href="http://baike.baidu.com/item/%E4%B8%B2%E8%A1%8C">串行</a>化，为了防止事务操作间的混淆，必须串行化或序列化请求，使得在同一时间仅有一个请求用于同一数据。

#### 持久性

编辑

在事务完成以后，该事务对数据库所作的更改便持久的保存在数据库之中，并不会被回滚。

由于一项操作通常会包含许多子操作，而这些子操作可能会因为硬件的损坏或其他因素产生问题，要正确实现ACID并不容易。ACID建议数据库将所有需要更新以及修改的资料一次操作完毕，但实际上并不可行。

目前主要有两种方式实现ACID：第一种是Write ahead logging，也就是日志式的方式(现代数据库均基于这种方式)。第二种是Shadow paging。

相对于WAL（write ahead logging）技术，shadow paging技术实现起来比较简单，消除了写日志记录的开销恢复的速度也快(不需要redo和undo)。shadow paging的缺点就是事务提交时要输出多个块，这使得提交的开销很大，而且以块为单位，很难应用到允许多个事务并发执行的情况——这是它致命的缺点。

WAL 的中心思想是对数据文件 的修改（它们是表和索引的载体）必须是只能发生在这些修改已经 记录了日志之后 -- 也就是说，在日志记录冲刷到永久存储器之后． 如果我们遵循这个过程，那么我们就不需要在每次事务提交的时候 都把数据页冲刷到磁盘，因为我们知道在出现崩溃的情况下， 我们可以用日志来恢复数据库：任何尚未附加到数据页的记录 都将先从日志记录中重做（这叫向前滚动恢复，也叫做 REDO） 然后那些未提交的事务做的修改将被从数据页中删除 （这叫向后滚动恢复 - UNDO）。

事务有三种模型：

1．隐式事务是指每一条数据操作语句都自动地成为一个事务，事务的开始是隐式的，事务的结束有明确的

标记。

2．显式事务是指有显式的开始和结束标记的事务，每个事务都有显式的开始和结束标记。

3．自动事务是系统自动默认的，开始和结束不用标记。

并发控制

1． 数据库系统一个明显的特点是多个用户共享数据库资源，尤其是多个用户可以同时存取相同数据。

串行控制：如果事务是顺序执行的，即一个事务完成之后，再开始另一个事务

并行控制：如果DBMS可以同时接受多个事务，并且这些事务在时间上可以重叠执行。

2．并发控制概述

事务是并发控制的基本单位，保证事务ACID的特性是事务处理的重要任务，而并发操作有可能会破坏其ACID特性。

DBMS并发控制机制的责任：

对并发操作进行正确调度，保证事务的隔离性更一般，确保数据库的一致性。

如果没有锁定且多个用户同时访问一个数据库，则当他们的事务同时使用相同的数据时可能会发生问题。由于并发操作带来的数据不一致性包括：丢失数据修改、读”脏”数据（脏读）、不可重复读、产生幽灵数据。

（1）丢失数据修改

当两个或多个事务选择同一行，然后基于最初选定的值更新该行时，会发生丢失更新问题。每个事务都不知道其它事务的存在。最后的更新将重写由其它事务所做的更新，这将导致数据丢失。如上例。

再例如，两个编辑人员制作了同一文档的电子复本。每个编辑人员独立地更改其复本，然后保存更改后的复本，这样就覆盖了原始文档。最后保存其更改复本的编辑人员覆盖了第一个编辑人员所做的更改。如果在第一个编辑人员完成之后第二个编辑人员才能进行更改，则可以避免该问题。

（2）读“脏”数据（脏读）

读“脏”数据是指事务T1修改某一数据，并将其写回磁盘，事务T2读取同一数据后，T1由于某种原因被除撤消，而此时T1把已修改过的数据又恢复原值，T2读到的数据与数据库的数据不一致，则T2读到的数据就为“脏”数据，即不正确的数据。

例如：一个编辑人员正在更改电子文档。在更改过程中，另一个编辑人员复制了该文档（该复本包含到目前为止所做的全部更改）并将其分发给预期的用户。此后，第一个编辑人员认为所做的更改是错误的，于是删除了所做的编辑并保存了文档。分发给用户的文档包含不再存在的编辑内容，并且这些编辑内容应认为从未存在过。如果在第一个编辑人员确定最终更改前任何人都不能读取更改的文档，则可以避免该问题。

（ 3）不可重复读

指事务T1读取数据后，事务T2执行更新操作，使T1无法读取前一次结果。不可重复读包括三种情况：

事务T1读取某一数据后，T2对其做了修改，当T1再次读该数据后，得到与前一不同的值。

（4）产生幽灵数据

按一定条件从数据库中读取了某些记录后，T2删除了其中部分记录，当T1再次按相同条件读取数据时，发现某些记录消失

T1按一定条件从数据库中读取某些数据记录后，T2插入了一些记录，当T1再次按相同条件读取数据时，发现多了一些记录。