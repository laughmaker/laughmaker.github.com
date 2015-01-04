---
layout: post
title: "php开发规范"
date: 2015-01-04 16:04:00 +0800
comments: true
categories:web 
---


#### PHP命名规范

1. 类文件都是以“**.class.php**“为后缀，且类文件名只允许字母，使用驼峰法命名，并且首字母大写，例如：DbMysql.class.php 。
2. 配置和函数等其他类库文件之外的文件一般是分别以“.inc.php“和”.php“为后缀，且文件名命名使用小写字母和下划线的方式，多个单词之间以下 划线分隔，例如config.inc.php ， common.php，install_function.php 。
3. 确保文件的命名和调用大小写一致，是由于在类Unix系统上面，对大小写是敏感的。
4. 类名和文件名一致（包括上面说的大小写一致），且类名只允许字母，例如 UserAction类的文件命名是UserAction.class.php， InfoModel类的文件名是InfoModel.class.php 。
5. 控制器类以Action为后缀，例如 UserAction、InfoAction ，模型类以Model为后缀，例如UserModel、InfoModel ，其他类也分别以相应分类为后缀，例如Service 、Widget。
6. 方法名只允许由字母组成，下划线是不允许的，首字母要小写，其后每个单词首字母要大写，即所谓的 “驼峰法命名” 规则，且越详细越好，应该能够描述清楚该方法的功能，例如switchModel、findPage。
7. 属性的命名只允许由字母组成，下划线是不允许的，首字母要小写，其后每个单词首字母要大写，即所谓的 “驼峰法命名” 规则，例如tablePrefix、tableName 。
8. 对于对象成员的访问，我们必须始终使用 “get” 和 “set” 方法。例如：  

		class Foo
		{
		    protected $_testObj;
		    public function getTestObj()
		
		    {
		        return $this->_testObj;
		    }
		    public function setTestObj($testObj)
		    {
		        $this->testObj = $_testObj;
		    }
		}
9. 当类成员方法被声明为 private 时，必须分别以双下划线 "__"为开头；被声明为 protected 时，必须分别以单下划线 "_" 为开头；一般情况下的方法不含下划线。例如 ：

		class Foo
		{
		    private function __example()
		    {
		        // ...
		    }
		    protected function _example()
		    {
		        // ...
		    }
		    public function example()
		    {
		        // ...
		    }
		}
10. 如果我们需要把一些经常使用的方法定义为全局函数，那么应该把它们以静态 (static) 的形式定义在类中。例如：

		class Think
		{
		    // ...
		    static public function autoload($classname)
		    {
		        // ...
		    }
		}
11. 被声明为 private的类成员属性必须由双下划线 "__" 作为开头；被声明为 protected 的类成员属性必须由下划线 "_" 作为开头；而声明为 public 的成员属性则在任何时候都不允许含有下划线。
12. 函数的命名使用小写字母和下划线的方式，且越详细越好，应该能够描述清楚该函数的功能，例如 get_client_ip 。
13. 当方法或函数参数不一定需要被赋值的时候，用 "null" 来代替 "false" 作为函数参数的默认值，除非该参数是 boolean 值。
14. 变量只允许由小写字母和下划线组成，且建议用描述性的变量的命名，越详细越好，以至于像 $i 或 $n 等等都是不鼓励使用的。
15. 类中的常量 constant 和全局范围内常量define，只能由大写字母和下划线组成，各个单词之间以下划线分割。
16. boolean 值和 null 值都采用小写。