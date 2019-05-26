# Junit #
GitHub项目链接为：
- 单元测试：[https://github.com/Mity1299/SpringBoot/tree/master/junit](https://github.com/Mity1299/SpringBoot/tree/master/junit)
- 套件测试：[https://github.com/Mity1299/SpringBoot/tree/master/junit-suit](https://github.com/Mity1299/SpringBoot/tree/master/junit-suit)
1. Junit的作用
Junit使用于单元测试的组件，即对单一实体、类、方法的测试，测试他们的功能是否能按需要正常运行。
1. 添加依赖
```
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
	```
2. 创建测试类
在想测试的.java文件，右键->New->Other->JUnit Test Case，自动生成一个测试类。
3. 在测试类前添加注解
	在测试类添加以下三个注释
	1. @RunWith(SpringJUnit4ClassRunner.class)
		>参考链接：[https://www.jianshu.com/p/70aee958e980](https://www.jianshu.com/p/70aee958e980 "Spring中使用@RunWith整合的测试注解")

		括号里写的其实是“测试运行器”，JUnit所有的测试方法都是由测试运行器负责执行。
		``@RunWith(SpringJUnit4ClassRunner.class``，让测试运行于Spring测试环境。
	2. @SpringApplicationConfiguration(classes = Application.class)
		@SpringBootApplication是一个组合注解，用于快捷配置启动类。```classes```参数表示把某些类纳入测试环境的容器中。
		>需要注意的是，较新版的Spring Boot取消了@SpringApplicationConfiguration这个注解，用@SpringBootTest就可以了。
		>参考链接：[https://blog.csdn.net/long_yi_1994/article/details/80274220](https://blog.csdn.net/long_yi_1994/article/details/80274220 "Spring Boot @SpringApplicationConfiguration")
	3. @WebAppConfiguration
		指定加载 ApplicationContext是一个WebApplicationContext。

		>参考链接：[https://www.cnblogs.com/jianliang-Wu/p/5652629.html](https://www.cnblogs.com/jianliang-Wu/p/5652629.html "Spring 注解学习笔记 ")
4. 业务层的测试
	
	1. 自动装载需要的Biz
	
	```    
	@Autowired
    UserBiz userBiz; 
	```
	2. 在需要测试的方法上面使用```@Test```注解
		测试方法返回值必须为void，且要求参数为空
	3. 在测试的方法体里写上自己需要测试的内容即可
	```
    public void test() {
        User user=userBiz.selectById((long) 1);
        System.out.println(user.getName());
    }
``` 

5.  使用MockMvc对控制层进行测试
>参考链接：[https://www.jianshu.com/p/4ffa505bbd32](https://www.jianshu.com/p/4ffa505bbd32 "Java测试套件 - Junit与Mockito")

	MockMvc可以模拟客户端的请求和服务端的响应，也可以模拟请求路径。

	1. 初始化
	初始化请求、响应和mvc 
```
   @Before
    public void setUp() throws Exception{
        request = new MockHttpServletRequest();//初始化请求
        request.setCharacterEncoding("UTF-8");
        response = new MockHttpServletResponse();//初始化响应
//初始化MockMvc，用来模拟路径请求
        mvc = MockMvcBuilders.webAppContextSetup(wac).build();
    }
```

	1. 模拟请求路径
```
    @Test
    @Rollback
    public void testPath() {
        try {
            
            mvc.perform(post("/user/main").param("id", "1")).//请求该路径，参数为id=1
                    andExpect(status().isOk()).//希望返回的响应的状态是"ok"
                    andDo(print());//打印请求体和响应体
}
```
```@Rollback```是指该测试执行完了之后，就回滚。
>但是实际上，就算用了@Rollback，对数据库也是有影响的。
>
>举个例子，假设数据库表里的user的主键(id)是自动增长的：
>我想测试一个插入user的方法，在使用了```@Rollback```注解的情况下，该测试执行完后，虽然此时查看数据库里，没有刚刚测试插入的user，但是自动增长序列是增长了的。
>也就是说，如果一开始自动增长序列为1，插入user后的增长序列为2，虽然```@Rollback```回滚了，但是再次插入数据的时候，自动增长编号会是3而不是2

	1. 模拟请求(request)
	```    
public void test() {
        request.setParameter("id", "1");//设置请求参数为id=1
		//控制器从请求中获得参数id，并且执行响应的方法
        User user =userController.getUser(Long.parseLong(request.getParameter("id")));
        System.out.println(user.getName());
		//使用断言判断结果
        assertEquals("user1", user.getName());
    }```
由于我的数据库里id=1的user的name是user1，因此使用断言判断测试结果是否是user1
2. 断言机制
所谓的断言，就是一种判断结果是否正确的机制，总是以“assertXXXX”格式出现，只要有一条验证结果与预想不匹配，则测试不通过。

 断言的优点是什么呢？
首先我们假设没有断言，如果我们有很多个要测试的方法，我们只能一个一个地打印方法返回的结果，判断这个结果是否是我们想要地结果。当然我们也可以使用```if```来进行判断，就像下面这样。但是这样没有那么直观。
```
if(是我们想要的结果){
	return;
}
```
所以，断言的作用实际上就是免去了“打印结果，判断测试是否通过”这个步骤，把这个步骤交给断言做。
断言会根据方法返回的值，以及我们想要的返回的值进行对比，判断两者是否一致：

 >如果一致，则通过测试；
>如果不一致，则不通过测试，并且告诉程序员，哪个方法发生了错误。

 这样就使程序员可以进行批量测试，因为不需要一个一个地看方法的返回值是什么，再自己进行判断。
>常用断言见:[https://www.jianshu.com/p/c14f0b974b90](https://www.jianshu.com/p/c14f0b974b90 "JUnit常用断言及注解")
1. 套件测试
所谓的套件测试其实是升级版的批量测试。
在实际项目中，随着项目进度的开展，单元测试类会越来越多，可是直到现在我们还只会一个一个的单独运行测试类，这在实际项目实践中肯定是不可行的。为了解决这个问题，JUnit 提供了一种批量运行测试类的方法，叫做测试套件。在前面的介绍中，我们最多只能对一个类的方法进行批量测试，但是在套件测试中，我们可以将多个类的测试放在一起进行测试。
	1. 套件的使用
	在测试类文件中，右键->New->Other->Junit Test Suite，选中自己想要一起测试的类，点击“Finish”
生成的代码如下：

	```
	@RunWith(Suite.class)
//花括号里面的两个测试类，就是该套件会执行的测试
	@SuiteClasses({ HouseControllerTest.class, UserControllerTest.class })
	public class AllTests {
	//这里面不用写代码
	}
	```
该套件会执行```@SuiteClasses```里的所有测试类

3. 常用注解
>见[https://www.cnblogs.com/wxishang1991/p/4485686.html](https://www.cnblogs.com/wxishang1991/p/4485686.html "junit常用注解详细说明 ")


