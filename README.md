# rails-devise-cancancan-rolify
devise、cancancan、rolify的使用说明
关于用户管理和权限管理详细介绍可以阅读：   https://ruby-china.org/topics/25512

## devise的集成

### 1.创建项目
rails new project_name --skip-bundle #跳过bundler

### 2.在gemfile中加入gem包
~~~
gem 'devise'
gem 'cancan'
gem 'rolify'
~~~

### 3.运行bundler加载gem包

### 4.初始化devise
~~~
rails g devise:install
~~~

这句命令会产生一个用户指南，告诉你该做的几件事请，以下是内容翻译（已经去除heroku部署的那一条，增加了登录退出选项的说明）：

1) 确定你的环境中有一个缺省的URL，config/environments/development.rb:
~~~
config.action_mailer.default_url_options = { :host => 'localhost:3000' }
~~~
如果在production环境, :host 必须设置成应用的真实主机名。

2) 确定已经在config/routes.rb中定义了root_url（注意删除public下面的index.html）, 例如：
~~~
root :to => "home#index" #项目入口
~~~
可以使用下面命令生成一个home#index的页面：
~~~
rails g controller home index
~~~
3) 在app/views/layouts/application.html.erb中增加消息提醒，例如：
~~~
<p class="notice"><%= notice %></p>  <p class="alert"><%= alert %></p> 
~~~
4) 很多时候还需要增加登录、退出的选项：

    <% if current_user %>
      <%= link_to('退出', destroy_user_session_path, :method => :delete) %> |
      <%= link_to('修改密码', edit_registration_path(:user)) %>
    <% else %>
      <%= link_to('注册', new_registration_path(:user)) %> |
      <%= link_to('登录', new_session_path(:user)) %>
    <% end %><span></span>
5) 如果要定制Devise的view模型，可以再执行以下语句：
~~~
rails g devise:views
~~~
### 5.生成用户模型
~~~
rails g devise User #可以是任意name
rake db:migrate
~~~
### 6.在controller中加入认证过滤
~~~
before_filter :authenticate_user!
~~~
## 集成cancan和rolify

cancan提供对资源的授权控制。例如，在视图中使用can?方法来决定是否显示某个页面元素。如果系统角色非常简单，那么cancan还在代码中直接指定常量就可以支持，具体操作可以参考官方文档。但要提供复杂的角色管理，最好的方案，还是在devise基础上再集成cancan+rolify。

1.修改Gemfile，并再次运行bundle install
~~~
gem 'cancan'
gem 'rolify'
~~~
2.创建cancan的Ability和rolify的Role
~~~
$ rails generate cancan:ability
$ rails generate rolify Role User
$ rake db:migrate
~~~
3.定制devise用户注册事件，可以在注册时赋予用户rolify角色，例如，下面的代码为首个用户赋予admin角色：
~~~
    class ApplicationController < ActionController::Base
     def after_sign_in_path_for(resource)
       if resource.is_a?(User)
         if User.count == 1
           resource.add_role 'admin'
         end
         resource
       end
       root_path
     end
   end
~~~
4.使用cancan可以为rolify中建立的角色分配授权资源，例如我们为允许admin角色的用户分配针对所有控制类的”manage”资源，而其他用户分配”read”资源：
~~~
    class Ability
     include CanCan::Ability
     def initialize(user)
       if user.has_role? :admin
         can :manage, :all
       else
         can :read, :all
       end
     end
   end
~~~
5.以上已经实现了“用户－角色－权限”的三层权限模型，在view中就可以使用了。例如，在Home#index页面中增加如下代码：
~~~
    <% if user_signed_in? %>
        <p>The user is loged in.</p>
        <% if can? :manage, :Home %>
          <%= link_to "About", home_about_path   %>
        <% end %>
    <% end %>
~~~

## License
欢迎修改、完善和指正不合理的步骤。

感谢：http://my.oschina.net/silentboy/blog/204772  博主的资源
