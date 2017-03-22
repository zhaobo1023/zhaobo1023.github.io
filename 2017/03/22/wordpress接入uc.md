# 技术博客接入UC
_赵铂 2017-03-22

## 步骤
1. 申请域名接入UC
2. 下载PhpCAS
3. 安装WpCas插件，修改插件配置
	执行以下操作
	
	```
	cp wpcas-conf-sample.php wpcas-conf.php
	vim wpcas-conf.php
	
	$wpcas_options = array(
        'cas_version' => '2.0',
        'include_path' => '/home/work/local/CAS/CAS.php', //phpCAS的目录
        'server_hostname' => 'passport.lianjia.com',
        'server_port' => '80',
        'server_path' => 'cas'
        );
    ```
	
4. 解决http访问问题，修改代码
	由于是wpCAS缺省是http访问。因此需要修改代码以支持http访问，修改以下方法 
	
	* _getServerBaseURL()
	
	```
	$this->_server['base_url'] = 'https://' . $this->_getServerHostname();
	改为
	$this->_server['base_url'] = 'http://' . $this->_getServerHostname();
	```
	* _getCallbackURL()
	
	```
    $final_uri = 'https://';
    改为
    $final_uri = 'http://';
    ```
	
6. 解决跳转带Port的问题，修改代码
	修改/home/work/local/CAS/CAS/Client.php文件，_getClientUrl()方法,下面代码注释掉
	
	```
	//if ( ($this->_isHttps() && $server_port!=443)
    //            || (!$this->_isHttps() && $server_port!=80)
    //        ) {
    //            $server_url .= ':';
    //           $server_url .= $server_port;
    //        }
   ```
7. 解决跳转域名问题（只带根域名，不带后面的path,测试不同权限跳转是否正确） 
8. 执行顺序，首先帮助同事用工号注册账号，然后才可以用UC登陆
9. 默认跳登录页
11. 如何不登录后台禁用插件
	在测试连接passport的时候，尴尬的一幕发生了，由于没有提前注册账户，导致通过passport.lianjia.com成功登陆后没有管理员权限，无法执行其他操作，只能禁掉wpCAS插件
	方法如下：
	记录 WordPress 插件信息的表位于：wp_options 表中的 active_plugins 字段，active_plugins 的值记录着插件使用情况。修改该字段，将wpCAS信息删去，然后用admin账户登陆后首先给相应工号的账户创建权限，然后重启wpCAS