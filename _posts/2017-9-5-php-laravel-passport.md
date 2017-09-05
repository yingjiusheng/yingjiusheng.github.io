---
layout: post
title:  "安装并使用 Laravel Passport 来请求授权令牌（OAuth2.0认证方式）"
categories: PHP Laravel Oauth2.0
tags:  PHP Laravel Oauth2.0
---

* content
{:toc}
laravel框架API认证（Passport）的使用

<!--excerpt-->
# 安装Passport
## 使用 Composer 依赖包管理器安装 Passport

```
composer require laravel/passport
```

![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-composer-require-laravel-passport.png)
接下来将

```
 	
Laravel\Passport\PassportServiceProvider::class,
```
这句代码添加到 config/app.php 的 providers 数组，
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-provider.png)

然后运行

```
php artisan migrate --path=vender/laravel/passport/database/migrate
/* 此处为oauth生成的数据表结构，路径略有不同 */
```
来运行 Passport 自带的数据库迁移文件（用于自动创建 Passport 必需的客户端数据表和令牌数据表）,
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-artisan-migrate.png)

然后运行

```
php artisan passport:install
```
来创建生成安全访问令牌时用到的加密密钥及私人访问和密码访问客户端。
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-artisan-passport-install.png)

接下来再将

```
 	
Laravel\Passport\HasApiTokens
```
Trait 添加到 App\User 模型中，这个 Trait 会给这个模型提供一些辅助函数，用于检查已认证用户的令牌和使用作用于。
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-has-api-tokens.png)

然后在 AuthServiceProvider 的 boot 方法中添加

```
    Passport::routes();
    Passport::tokensExpireIn(Carbon::now()->addMinutes(30));
    Passport::refreshTokensExpireIn(Carbon::now()->addDays(1));
```
来注册 Passport 在访问令牌、客户端、私人访问令牌的发放和吊销过程中一些必要路由。
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-routes.png)
最后修改下配置文件 config/auth.php 中的 api driver 改为 passport，此项操作会将 API 请求时使用 Passport 的  TokenGuard 来处理授权保护。

![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-auth-api-driver.png)

全部配置好后，我们来请求令牌，但是发现我们并没有创建任何用户，于是我们需要用到seed来预设填充数据。创建用户。

```
DB::table('users')->insert([
'name'=>'test1',
'email'=>'test1@qq.com',
'password'=>bcrypt('123456')
]);
```

至此安装成功。

# 两种方式运行测试
- 首先此时表中的client有两个

![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-sql1.png)

## 方式一：access_token和token

这是前端 UI，
    
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-passport-ajax-oauth-token.png)

然后些下JavaScript请求代码如下：


```
$('#loginButton').click(function(event){
event.preventDefault();
$.ajax({
type:'post',
url:'http://wodedaxue-backend/oauth/token',
dataType:'json',
data:{
'grant_type':'password',
'client_id':'2',
'client_secret':'o6N7JzK2y3pbJiQmDcHaDhrpvPHBktnAlmmfEnS7',
'username':$('#username').val(),
'password':$('#password').val(),
'scope':''
},
success:function(data){
console.log(data);
alert(JSON.stringify(data));
},
error:function(err){
console.log(err);
alert('statusCode:'+err.status+'\n'+'statusText:'+err.statusText+'\n'+'description:\n'+JSON.stringify(err.responseJSON));
}
});
});
```
接下来再将以下代码写入 User 模型：

```
/**
* 自定义用Passport授权登录：用户名+密码
* @param $username
* @return mixed
*/
public function findForPassport($username)
{
return self::where('name', $username)->first();
}
```
然后输入用户名 admin 和密码 admin ，点击登录按钮尝试登录：

```
{
token_type:"Bearer",
expires_in:31536000,
access_token:"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp0aSI6ImNiOG…MFF7PYRIhXqCkZhZNfoLV1IYTzRmhO2oV41XR9wD7Zd3Oxl-g",
refresh_token:"sabtS2jYhqAJtVxe868OpTTgBeYgpWgfttn5h97drUgBVLN7vL…ukx7yGfGhn5h+zCouLmuh0sdjYU+dgtRweXU3jiA0Fa6/c6k="
}
```
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-postman.png)
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-sql2.png)

*默认情况下，Passport颁发的访问令牌（access token）是长期有效的，如果你想要配置更短的令牌生命周期，可以使用 tokensExpireIn 和 refreshTokensExpireIn 方法，这些方法需要在 AuthServiceProvider 的 boot 方法中调：*

## 方式二：私人访问令牌
*注：私人访问令牌总是一直有效的，它们的生命周期在使用 tokensExpireIn 或 refreshTokensExpireIn 方法时不会修改。*
- 新建测试控制器和路由
routes/api.php
```
    Route::post('login', 'API\UserController@login');
    Route::any('register', 'API\UserController@register');
    Route::group(['middleware' => 'auth:api'], function(){
    Route::post('details', 'API\UserController@details');
});
```
controller/api/usercontroller

```
<?php namespace App\Http\Controllers\API;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\User;
use Illuminate\Support\Facades\Auth;
use Validator;

class UserController extends Controller
{
    public $successStatus = 200;

    /** * login api * * @return \Illuminate\Http\Response */
    public function login()
    {
        if (Auth::attempt(['email' => request('email'), 'password' => request('password')])) {
            $user = Auth::user();
            $success['token'] = $user->createToken('MyApp')->accessToken;
            return response()->json(['success' => $success], $this->successStatus);
        } else {
            return response()->json(['error' => 'Unauthorised'], 401);
        }
    }

    /** * Register api * * @return \Illuminate\Http\Response */
    public function register(Request $request)
    {
        $validator = Validator::make($request->all(), ['name' => 'required', 'email' => 'required|email', 'password' => 'required', 'c_password' => 'required|same:password',]);
        if ($validator->fails()) {
            return response()->json(['error' => $validator->errors()], 401);
        }
        $input = $request->all();
        $input['password'] = bcrypt($input['password']);
        $user = User::create($input);
        $success['token'] = $user->createToken('MyApp')->accessToken;
        $success['name'] = $user->name;
        return response()->json(['success' => $success], $this->successStatus);
    }

    /** * details api * * @return \Illuminate\Http\Response */
    public function details()
    {
        $user = Auth::user();
        return response()->json(['success' => $user], $this->successStatus);
    }
}
```
- 测试
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-postman2.png)
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-postman3.png)
![image](https://raw.githubusercontent.com/yingjiusheng/yingjiusheng.github.io/master/images/php-laravel-sql3.png)