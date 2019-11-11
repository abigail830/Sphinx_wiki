SpringBoot Security(2)
==========================

Generally, we would not hardcode user/password but would store it and config in DB and may also further load in Redis. So here is another example with DB connection.

Step1. Prepare DB

Table User and Role, and also User_Role which is for the 1-N mapping between them. Also, we could prepare some dummy data in it. Please note the RoleName need to be start with "ROLE_" and also password is MD5 encrypted.

.. code-block:: sql
  
  create TABLE `user` (
    `id` bigint(11) NOT NULL AUTO_INCREMENT,
    `user_name` varchar(255) NOT NULL,
    `password` varchar(255) NOT NULL,
    PRIMARY KEY (`id`)
  );
  create TABLE `role` (
    `id` bigint(11) NOT NULL AUTO_INCREMENT,
    `name` varchar(255) NOT NULL,
    PRIMARY KEY (`id`)
  );
  create TABLE `user_role` (
    `user_id` bigint(11) NOT NULL,
    `role_id` bigint(11) NOT NULL
  );
  
  insert into user (user_name, password) VALUES ('user','e10adc3949ba59abbe56e057f20f883e');
  insert into user (user_name , password) VALUES ('admin','e10adc3949ba59abbe56e057f20f883e');
  insert into role (name) values ('ROLE_USER');
  insert into role (name) values ('ROLE_ADMIN');
  insert into user_role (user_id, role_id) values (1, 1);
  insert into user_role (user_id, role_id) values (2, 2);
  

Step2. Prepare Entity and Mappper (Assume using mybatis)

注意，在Entity里面实现了特殊的继承

**RoleEntity.java**

.. code-block:: java
  
  @Getter
  @Setter
  @ToString
  @AllArgsConstructor
  @NoArgsConstructor
  public class RoleEntity implements GrantedAuthority {

    private Long id;
    private String name;

    @Override
    public String getAuthority() {
        return name;
    }
  }


**UserEntity.java**

.. code-block:: java
  
  @ToString
  @Setter
  @Getter
  public class UserEntity implements UserDetails, Serializable {

    private Long id;
    private String userName;
    private String password;
    private List<RoleEntity> authorities;

    public User toUser(){
        return new User(id, userName, password);
    }
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public String getPassword() {
        return this.password;
    }

    @Override
    public String getUsername() {
        return this.userName;
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }

    public Long getId() {
        return id;
    }
  }


