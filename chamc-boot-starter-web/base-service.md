### Service层

1. 简介：service负责实现业务逻辑，service调用repository中的方法，在service中进行事务控制。
 
2. 事务控制  
   - 在service中进行事务控制，只需在service的方法上添加 `@Transactional` （javax.transaction.Transactional），推荐在做数据库修改操作时都加上事务控制。
   
   			@Service
			public class UserService {
				private @Autowired UserRepository userRepository;
				private @Autowired UserDetailRepository userDetailRepository;

				@Transactional
				public User save(User user){
					UserDetail detail = userDetailRepository.save(user.getUserDetail());
					user.setUserDetail(detail);
					user = userRepository.save(user);
       				return user;
				}

   - 注意，不推荐在service中调用service，这样做可能会让事务失效。

3. param 、 result 和 dto 尽量不要出现在service中

   - service中只做数据库服务相关的操作，判断操作结果为空应在controller中进行
   - param、result 和 dto尽量不要传入service中，service中只接收controller中处理好的参数或实体类。 
