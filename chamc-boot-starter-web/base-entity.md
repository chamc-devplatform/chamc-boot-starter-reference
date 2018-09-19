###1 介绍
####1.1 基本概念

- Entity（实体类）一般都有很多的属性（状态），并有相应的setter和getter方法。
- Entity的作用一般是和数据表做映射，即Entity对应数据库中的表。
- 在项目中写实体类一般遵循下面的规范：

	1. 根据业务命名实体类，定义一组所需的私有属性。
	
	2. 根据这些属性，创建它们的setter和getter方法。
	
	3. 按需提供带参构造器和无参构造器。
	
	4. 重写父类中的eauals()方法和hashcode()方法。（如果需要涉及到两个对象之间的比较，这两个功能很重要。）

	5. 按需实现序列化并赋予其一个版本号。

####1.2 常用注解

- 本小节仅介绍基本注解的含义，基本注解的使用示例见下一小节

- **@Entity** : 对实体注释，指明这是一个实体Bean。任何Hibernate映射对象都要有这个注释。

- **@Table** : 指定了Entity所要映射带数据库表，其中`@Table.name()`用来指定映射表的表名。如果缺省@Table注解，系统默认采用类名作为映射表的表名。实体Bean的每个实例代表数据表中的一行数据，行中的一列对应实例中的一个属性。

- **@NoArgsConstructor** 和 **@AllArgsConstructor** : `lombok`注解，提供分别提供无参和全参构造函数。

- **@Data** : lombok注解，注解在类上。使用@Data注解能够减少大量的模板代码，该注解提供属性的getter和setter方法，同时提供 `equals()`、`hashCode()`、`toString()` 方法。

- **@Id** ： 指定表的主键。

- **@GeneratedValue** ： 定义了标识字段生成方式。

- **@GenericGenerator** ： 是`Hibernate`所提供的自定义主键生成策略生成器，由`@GenericGenerator`实现多定义的策略。

- **@Column** ： 定义了将成员属性映射到关系表中的哪一列和该列的结构信息。


####1.3 关联关系

在hibernate中，通过映射将持久化类和数据库表来进行关联。关联是类（类的实例）之间的关系，表示有意义和值得关注的连接。

#####1.3.1 示例实体

> - 下面给出了用户和机构的实体，使用了上一小节介绍的全部基本注解，关联关系的介绍在此基础上展开。
> - **snowflake** ： 【生成规则，待补充】
> - 用户实体

	@Entity
	@Table(name = "t_user")
	public @Data class User {
	
		@Id @GeneratedValue(generator = "snowflake")
		@GenericGenerator(name = "snowflake", strategy = "com.chamc.boot.web.support.SnowflakeIdGenerator")
		@Column(name = "id_")
		private Long id;
		
		@Column(name = "name_")
		private String name;
		
		@Column(name = "age_")
		private Long age;
		
	}

> - 机构实体

	@Entity
	@Table(name = "t_org")
	public @Data class Org {
	
		@Id @GeneratedValue(generator = "snowflake")
		@GenericGenerator(name = "snowflake", strategy = "com.chamc.boot.web.support.SnowflakeIdGenerator")
		@Column(name = "id_")
		private Long id;
		
		@Column(name = "org_no_")
		private String orgNo;
		
		@Column(name = "org_name_")
		private String orgName;
		
	}

- **以下所说的`添加`均为在如上实体的前提下的修改，每个示例相互独立**

#####1.3.2 一对一关系

> - 假设用户与机构为单向一对一关系，关系拥有方为用户。示例：在`t_user`表添加字段`org_id_`，在User实体添加如下属性

	@OneToOne(fetch = FetchType.LAZY)
	//注释本表中指向另一个表的外键。
	@JoinColumn(name = "org_id_")
	private Org org;

> - `@OneToOne(fetch = FetchType.LAZY)`标识关联关系为一对一关联，加载策略为延迟加载(默认为EAGER)。                 
> - 假设用户与机构非主键关联，示例：通过`org_no_`关联，在用户表新建字段`org_code_`，用于存储机构表`org_no_`的值形成关联关系。

	@OneToOne(fetch = FetchType.LAZY)
	// name本表字段名, referencedColumnName应用表字段名
	@JoinColumn(name = "org_code_", referencedColumnName = "org_no_")
	private Org org;

#####1.3.3 多对一关系

> - 假设用户与机构为单向多对一关系，关系拥有方为用户。示例：在`t_user`表添加字段`org_id_`，在User实体添加如下属性
> - 关系的维护端都是在多的那一面，多的一面为主控方，拥有指向对方的外键。 

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "org_id_")
	private Org Org;

#####1.3.4 一对多关系

> - 假设机构与用户为单向一对多关系，关系拥有方为用户。示例：在`t_user`表添加字段`org_id_`，在Org实体添加如下属性
> - 在一对多里面,映射关系的维护端都是在多的那一方,也就是User。因为在数据库中表现的话,是在User表中建立指向Org的外键。

	@OneToMany(fetch = FetchType.LAZY)
	@JoinColumn(name = "org_id_")
	private List<User> users;

> - 注意，在这里@JoinColumn这个注释指的是在User里面的外键的列的名字

#####1.3.5 MappedBy与双向关系

- MappedBy表示声明自己不是一对多的关系维护端，由对方来维护。一定是定义在被拥有方的，他指向拥有方。
- 只有OneToOne，OneToMany，ManyToMany上才有MappedBy属性，ManyToOne不存在该属性。
- MappedBy的含义为，拥有方能够自动维护跟被拥有方的关系，当然，如果从被拥有方，通过手工强行来维护拥有方的关系也是可以做到的。
- 双向关联关系包括双向一对一，双向多一和双向多对多关系，其中双向多一关系所有方为`ManyToOne`注解的一方，在被拥有方配置`OneToMany`和`MappedBy`

【双向多一示例】
> - 假设机构与用户为双向多一关系。示例：在`t_user`表添加字段`org_id_`。
> - User中添加如下属性和注解

	@JsonIgnoreProperties("users")
	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "org_id_")
	private Org org;

> - Org中添加如下属性和注解

	@JsonIgnoreProperties("org")
	@OneToMany(mappedBy = "org")
	private List<User> users;

- MappedBy是在一的一方进行声明的，MappedBy的值应该为关系拥有方(此例为多方)中含被拥有方(一方)的属性名
- 双向关联关系在**JSON转化**时会发生**循环关联**，推荐使用`@JsonIgnoreProperties`避免。注解的值注明该变量中的哪个属性不被序列化，从而允许在双向访问上都不存在环或是缺失。


#####1.3.6 多对多关系

- 假设用户与机构为多对多关联关系，增加关联表如下所示，使用用户和机构两个表的主键作为联合主键。

<pre>
Table : t_user_org
+----------+----------+------+-------+---------+
|  Field   |   Type   | Null |  Key  | Default |
+----------+----------+------+-------+---------+
| user_id_ |  bigint  |  No  |  PRI  |   Null  |
| org_id_  |  bigint  |  No  |  PRI  |   Null  |
+----------+----------+------+-------+---------+
</pre>
 
> - 在Org中添加属性和注解如下：

	@ManyToMany(fetch = FetchType.LAZY)
	@JoinTable(name = "t_user_org", joinColumns = { @JoinColumn(name = "org_id_") }, 
	inverseJoinColumns = { @JoinColumn(name = "user_id_") })
	private List<User> users;	

> - 其中`@JoinTable`标识了中间表的表名为`t_user_org`，以上为简略写法，等同于下面示例。
> joinColumns写的是本表在中间表的外键名称，inverseJoinColumns写的是另一个表在中间表的外键名称。

	@JoinTable(name = "t_user_org", joinColumns = { @JoinColumn(name = "org_id_", referencedColumnName = "id_") }, 
	inverseJoinColumns = { @JoinColumn(name = "user_id_", referencedColumnName = "id_") })