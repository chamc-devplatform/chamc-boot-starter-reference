4） 域登陆demo

* 新建以下7张表：t_sys\_org，t\_sys\_permission，t\_sys\_role，t\_sys\_role\_permission，t\_sys\_user，t\_sys\_user\_org，t\_sys\_user\_role。在jar包中获取建表脚本:\`init\_sys_\[mysql\|oracle\].sql\`。
* 在库中插入数据，包括用户、角色、权限等。以下是可用的几个域用户，角色、机构、权限请自行插入：

  ```text
    INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (1,'user1@kmdev.com.cn','用户1',NULL,'user1@dev.com',NULL,'dev\\user1',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
    INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (2,'leader1@kmdev.com.cn','领导1',NULL,'leader1@dev.com',NULL,'dev\\leader1',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
    INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (3,'user2@kmdev.com.cn','用户2',NULL,'user2@dev.com',NULL,'dev\\user2',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
  ```

* 写一个接口获取ad登录地址

  ```text
    @GetMapping("loginUrl")
    public String loginUrl(){
        String loginUrl = securityProperties.getAd().getUrl() + "?appName=" + securityProperties.getAd().getAppName() 
        + "&retUrl=" + securityProperties.getAd().getRetUrl();
        return loginUrl;    
    }
  ```

* 请求该接口，获取登录地址进行登录。