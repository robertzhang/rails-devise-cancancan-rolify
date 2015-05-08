# rails-devise-cancancan-rolify
devise、cancancan、rolify的使用说明

### devise的添加

#### 1.创建项目
rails new project_name --skip-bundle #跳过bundler

#### 2.在gemfile中加入gem包
~~~
gem 'devise'
gem 'cancan'
gem 'rolify'
~~~

#### 3.运行bundler加载gem包

#### 4.初始化devise
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
