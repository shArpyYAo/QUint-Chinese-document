# **QUnit官方文档译文**
[官网文档][1]

# 主要方法

## - module

QUnit.module（name [,hooks] [,nested])
使用单个标签对测试进行分组
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>name(string)</td>
            <td>用来标志一组测试</td>
        </tr>
        <tr>
            <td>hooks(object)</td>
            <td>在执行测试时的回调</td>
        </tr>
        <tr>
            <td>nested(function)</td>
            <td>嵌套模块的回调</td>
        </tr>
</table>

#### hooks的属性：{before, beforeEach, afterEach, after}

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>before (function)</td>
            <td>执行第一个测试之前运行</td>
        </tr>
        <tr>
            <td>beforeEach (function)</td>
            <td>每个测试之前运行</td>
        </tr>
        <tr>
            <td>afterEach (function)</td>
            <td>每个测试之后运行</td>
        </tr>
        <tr>
            <td>after (function)</td>
            <td>最后一个测试之后运行</td>
        </tr>
</table>

*提示：如果module的队列为空了之后定义了额外的测试方法，after这个钩子不会再次运行。*

#### 嵌套模块: nested( hooks )

当前模块下的一组测试和嵌套模块的回调
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>hooks (object)</td>
            <td>定义了before/beforeEach/afterEach/after钩子的对象</td>
        </tr>
</table>

### 描述

你可以用模块名称来组织、选择、筛选你要运行的测试。

所有的测试都是放在module的回调函数里来进行分组的。在测试结果里都是模块名称打头的，后面才是跟着具体的测试名称。其他模块可以嵌套在这个回调函数中，其中它们的测试名称将由它们的名称标记，并以其父模块为前缀。

如果QUnit.module定义的时候没有nested回调参数，所有后来定义的测试方法都被分组到这个模块里，直到另一个模块被定义。

有一组测试方法的module可以定义嵌套的module，在深入嵌套之前，QUnit会先在父module上跑测试，即使那些嵌套的module在父module最顶部定义了。此外，父模块上的任何钩子回调都会将挂钩包装在嵌套模块上。换句话说，before 和 beforeEach回调会形成一个队列，afterEach 和 after会形成一个栈。

你可以用钩子指定你在测试之前或测试之后需要做的事情，还可以创建用来在module内部的测试方法之间共享属性。钩子对象上的任何其他属性都将添加到该module中。如果你嵌套了子module，hooks参数是可选的。

The module’s callback is invoked with the test environment as its this context, with the environment’s properties copied to the module’s tests, hooks, and nested modules. Note that changes on tests’ this are not preserved between sibling tests, where this will be reset to the initial value for each test.

如果你的回调函数返回了了一个then，可用的Promise，QUnit.module的钩子能自动帮你进行一部结果的解析。

### 指定运行测试代码

当你在调试你的代码时，通常只需要运行一部分的测试代码。那你可以在QUnit.module后加上only告诉QUnit你想要运行的module。

如果有多个 QUnit.module.only()，则只运行最先定义的。

QUnit.only()也可以运行。
 
### 指定跳过测试代码

随着代码越来越庞大，你会发现有些与功能相关的测试由于某些原因暂时中断。在你找到bug之前，你可以使用skip跳过这些损坏的模块。

如果子组件和测试包含一些todo或仅仅是tests，QUnit也会跳过这些测试代码。

### 正在编写的测试

当这个module还在编写阶段，你可以用 QUnit.module.todo来标记。

Using this method will mark all underlying tests as todo as if they were defined using QUnit.todo except for tests defined using QUnit.skip() which will be left intact. Those tests will pass as long as one failing assertion is present.

If all assertions pass, then the tests will fail signaling that QUnit.module.todo should be replaced by QUnit.module.

### 例子

使用QUnit.module方法标记一组测试方法

    QUnit.module( "group a" );
    QUnit.test( "a basic test example", function( assert ) {
      assert.ok( true, "this test is fine" );
    });
    QUnit.test( "a basic test example 2", function( assert ) {
      assert.ok( true, "this test is fine" );
    });

     QUnit.module( "group b" );
        QUnit.test( "a basic test example 3", function( assert ) {
          assert.ok( true, "this test is fine" );
        });
        QUnit.test( "a basic test example 4", function( assert ) {
          assert.ok( true, "this test is fine" );
        });


----------


使用现代语法

    QUnit.module( "group a" );
    
    test( "a basic test example", t => {
      t.ok( true, "this test is fine" );
    });
    test( "a basic test example 2", t => {
      t.ok( true, "this test is fine" );
    });
    
    QUnit.module( "group b" );
    test( "a basic test example 3", t => {
      t.ok( true, "this test is fine" );
    });
    test( "a basic test example 4", t => {
      t.ok( true, "this test is fine" );
    });


----------


嵌套用法

    QUnit.module( "module a", function() {
      QUnit.test( "a basic test example", function( assert ) {
        assert.ok( true, "this test is fine" );
      });
    });
    
    QUnit.module( "module b", function() {
      QUnit.test( "a basic test example 2", function( assert ) {
        assert.ok( true, "this test is fine" );
      });
    
      QUnit.module( "nested module b.1", function() {
    
        // This test will be prefixed with the following module label:
        // "module b > nested module b.1"
        QUnit.test( "a basic test example 3", function( assert ) {
          assert.ok( true, "this test is fine" );
        });
      });
    });


----------


使用现代语法

    const { test } = QUnit;
    QUnit.module( "module a", () => {
      test( "a basic test example", t => {
        t.ok( true, "this test is fine" );
      });
    });
    
    QUnit.module( "module b", () => {
      test( "a basic test example 2", t => {
        t.ok( true, "this test is fine" );
      });
    
      QUnit.module( "nested module b.1", () => {
    
        // This test will be prefixed with the following module label:
        // "module b > nested module b.1"
        test( "a basic test example 3", t => {
          t.ok( true, "this test is fine" );
        });
      });
    });


----------


before, beforeEach, afterEach, and after 钩子的用法

    QUnit.module( "module A", {
      before: function() {
        // prepare something once for all tests
      },
      beforeEach: function() {
        // prepare something before each test
      },
      afterEach: function() {
        // clean up after each test
      },
      after: function() {
        // clean up once after all tests are done
      }
    });

----------

共同上下文环境的钩子和测试共享变量

    QUnit.module( "Machine Maker", {
      beforeEach: function() {
        this.maker = new Maker();
        this.parts = [ "wheels", "motor", "chassis" ];
      }
    });
    
    QUnit.test( "makes a robot", function( assert ) {
      this.parts.push( "arduino" );
      assert.equal( this.maker.build( this.parts ), "robot" );
      assert.deepEqual( this.maker.made, [ "robot" ] );
    });
    
    QUnit.test( "makes a car", function( assert ) {
      assert.equal( this.maker.build( this.parts ), "car" );
      this.maker.duplicate();
      assert.deepEqual( this.maker.made, [ "car", "car" ] );
    });


----------

before/beforeEach钩子在嵌套模块中以队列方式调用，after/afterEach钩子在嵌套模块中以栈方式调用

    QUnit.module( "grouped tests argument hooks", function( hooks ) {
    
      // You can invoke the hooks methods more than once.
      hooks.beforeEach( function( assert ) {
        assert.ok( true, "beforeEach called" );
      } );
    
      hooks.afterEach( function( assert ) {
        assert.ok( true, "afterEach called" );
      } );
    
      QUnit.test( "call hooks", function( assert ) {
        assert.expect( 2 );
      } );
    
      QUnit.module( "stacked hooks", function( hooks ) {
    
        // This will run after the parent module's beforeEach hook
        hooks.beforeEach( function( assert ) {
          assert.ok( true, "nested beforeEach called" );
        } );
    
        // This will run before the parent module's afterEach
        hooks.afterEach( function( assert ) {
          assert.ok( true, "nested afterEach called" );
        } );
    
        QUnit.test( "call hooks", function( assert ) {
          assert.expect( 4 );
        } );
      } );
    } );


----------

在钩子里处理异步返回的例子，例子使用es6的Promise接口，操作是连接、断开数据库

    QUnit.module( "Database connection", {
      before: function() {
        return new Promise( function( resolve, reject ) {
          DB.connect( function( err ) {
            if ( err ) {
              reject( err );
            } else {
              resolve();
            }
          } );
        } );
      },
      after: function() {
        return new Promise( function( resolve, reject ) {
          DB.disconnect( function( err ) {
            if ( err ) {
              reject( err );
            } else {
              resolve();
            }
          } );
        } );
      }
    } );
    


----------

只跑一部分测试代码

    QUnit.module( "Robot", function() {
      // ...
    } );
    
    // Currenly working on implementing features related to androids
    QUnit.module.only( "Android", function( hooks ) {
      hooks.beforeEach( function() {
        this.android = new Android();
      } );
    
      QUnit.test( "Say hello", function( assert ) {
        assert.strictEqual( this.android.hello(), "Hello, my name is AN-2178!" );
      } );
    
      QUnit.test( "Basic conversation", function( assert ) {
        this.android.loadConversationData( {
          "Hi": "Hello",
          "What's your name?": "My name is AN-2178.",
          "Nice to meet you!": "Nice to meet you too!",
          "...": "..."
        } );
    
        assert.strictEqual(
          this.android.answer( "What's your name?" ), "My name is AN-2178."
        );
      } );
    
      // ...
    } );
    


----------

跳过损坏模块

    QUnit.module( "Robot", function() {
      // ...
    } );
    
    // Tests related to androids are failling due to unkown cause.
    // Skipping them for now.
    QUnit.module.skip( "Android", function( hooks ) {
      hooks.beforeEach( function() {
        this.android = new Android();
      } );
    
      QUnit.test( "Say hello", function( assert ) {
        assert.strictEqual( this.android.hello(), "Hello, my name is AN-2178!" );
      } );
    
      QUnit.test( "Basic conversation", function( assert ) {
        // ...
        assert.strictEqual(
          this.android.answer( "Nice to meet you!" ), "Nice to meet you too!"
        );
      } );
    
      QUnit.test( "Move left arm", function ( assert ) {
        // Move the left arm of the android to point (10, 50)
        this.android.moveLeftArmTo( 10, 50 );
    
        assert.deepEqual( this.android.getLeftArmPosition(), { x: 10, y: 50 } );
      } );
    
      QUnit.test( "Move right arm", function ( assert ) {
        // Move the right arm of the android to point (15, 45)
        this.android.moveRightArmTo( 15, 45 );
    
        assert.deepEqual( this.android.getRightArmPosition(), { x: 15, y: 45 } );
      } );
    
      QUnit.test( "Grab things", function ( assert ) {
        // Move the arm of the android to point (10, 50)
        this.android.grabThingAt( 25, 5 );
    
        assert.deepEqual( this.android.getPosition(), { x: 25, y: 5 } );
        assert.ok( this.android.isGrabbing() );
      } );
    
      QUnit.test( "Can walk", function ( assert ) {
        assert.ok( this.android.canWalk() );
      } );
    
      QUnit.test( "Can speak", function ( assert ) {
        assert.ok( this.android.canSpeak() );
      } );
    
      // ...
    } );
    


----------

使用 QUnit.module.todo()来标记代码还在编写中

    QUnit.module.todo( "Robot", function( hooks ) {
      hooks.beforeEach( function() {
        this.robot = new Robot();
      } );
    
      QUnit.test( "Say", function( assert ) {
        // Currently, it returns undefined
        assert.strictEqual( this.robot.say(), "I'm Robot FN-2187" );
      } );
    
      QUnit.test( "Move arm", function ( assert ) {
        // Move the arm to point (75, 80). Currently, it throws a NotImplementedError
        assert.throws( function() {
          this.robot.moveArmTo(75, 80);
        }, /Not yet implemented/ );
    
        assert.throws( function() {
          assert.deepEqual( this.robot.getPosition(), { x: 75, y: 80 } );
        }, /Not yet implemented/ );
      } );
    
      // ...
    } );


----------


## - only

QUnit.only( name, callback )
添加单独运行的测试，阻止其他测试代码运行
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>name (string)</td>
            <td>名称</td>
        </tr>
        <tr>
            <td>callback (function)</td>
            <td>Function to close over assertions</td>
        </tr>
</table>
回调的参数: callback( assert ):
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>assert (object)	</td>
            <td>断言方法的新的实例对象</td>
        </tr>
</table>

### 描述

这个方法能帮助你专注于你的特殊测试单元，QUnit.only会导致运行时其他的测试被忽略。

如果QUnit.only有多个，则只运行最早定义的那个。

当有测试代码中大量的测试或者想要在控制台看测试输出时，这个方法非常有用。可以在页面的结果输出页面过滤其他测试的输出。

### 例子

如何使用 QUnit.only 

    QUnit.module( "robot", {
      beforeEach: function() {
        this.robot = new Robot();
      }
    });
    
    QUnit.test( "say", function( assert ) {
      assert.ok( false, "I'm not quite ready yet" );
    });
    
    QUnit.test( "stomp", function( assert ) {
      assert.ok( false, "I'm not quite ready yet" );
    });
    
    // You're currently working on the laser feature, so we run only this test
    QUnit.only( "laser", function( assert ) {
      assert.ok( this.robot.laser() );
    });
    


----------

使用现代语法

    const { test, only } = QUnit;
    
    QUnit.module( "robot", {
      beforeEach: function() {
        this.robot = new Robot();
      }
    });
    
    test( "say", t => {
      t.ok( false, "I'm not quite ready yet" );
    });
    
    test( "stomp", t => {
      t.ok( false, "I'm not quite ready yet" );
    });
    
    // You're currently working on the laser feature, so we run only this test
    only( "laser", function( t ) {
      t.ok( this.robot.laser() );
    });
    


----------

## - skip

QUnit.skip( name )
跳过指定测试方法
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>name (string)</td>
            <td>名称</td>
        </tr>
</table>

### 描述

使用这个方法替换掉QUnit.test()来跳过整个测试方法

This test’s prototype will be listed on the suite as a skipped test, ignoring the callback argument and the respective global and module’s hooks.

### Example

如何使用skip方法

    QUnit.module( "robot", {
      beforeEach: function() {
        this.robot = new Robot();
      }
    });
    
    QUnit.test( "say", function( assert ) {
      assert.strictEqual( this.robot.say(), "Exterminate!" );
    });
    
    // Robot doesn't have a laser method, yet, skip this test
    // Will show up as skipped in the results
    QUnit.skip( "laser", function( assert ) {
      assert.ok( this.robot.laser() );
    });
    


----------

使用现代语法

    const { test, skip } = QUnit;
    
    QUnit.module( "robot", {
      beforeEach() {
        this.robot = new Robot();
      }
    });
    
    test( "say", function( t ) {
      t.strictEqual( this.robot.say(), "Exterminate!" );
    });
    
    // Robot doesn't have a laser method, yet, skip this test
    // Will show up as skipped in the results
    skip( "laser", function( t ) {
      t.ok( this.robot.laser() );
    });


----------

## - start

QUnit.start()

略（#TODO）

## - test

QUnit.test( name, callback )
添加测试方法
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>name (string)</td>
            <td>名称</td>
        </tr>
        <tr>
            <td>callback (function)</td>
            <td>Function to close over assertions</td>
        </tr>
</table>
回调参数
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>assert (object)</td>
            <td>断言方法的新的实例对象</td>
        </tr>
</table>

### 描述

使用QUnit.test()添加测试方法

这个assert参数包含了QUnit所有的断言方法，使用这个参数进行断言

如果你的回调函数返回了了一个then，可用的Promise，QUnit.test能自动帮你进行一部结果的解析。

### 例子
一个实际的例子，使用局部的assert参数

    function square( x ) {
      return x * x;
    }
    
    QUnit.test( "square()", function( assert ) {
      var result = square( 2 );
    
      assert.equal( result, 4, "square(2) equals 4" );
    });
    


----------

使用现代语法

    function square( x ) {
      return x * x;
    }
    
    const { test } = QUnit;
    
    test( "square()", t => {
      t.equal( square( 2 ), 4, "square(2) equals 4" );
      t.equal( square( 3 ), 9, "square(3) equals 9" );
    });


----------

关于处理Promise异步返回的一个例子，例子使用的是es6的Promise并且延时了500ms

    QUnit.test( "a Promise-returning test", function( assert ) {
      assert.expect( 1 );
    
      var thenable = new Promise(function( resolve, reject ) {
        setTimeout(function() {
          assert.ok( true );
          resolve( "result" );
        }, 500 );
      });
      return thenable;
    });


----------

接着上面的例子，QUnit.test也支持js的async方法。

    function squareAfter1Second(x) {
      return new Promise(resolve => {
        setTimeout(() => {
          resolve(x * x);
        }, 1000);
      });
    }
    
    const { test } = QUnit;
    
    test( "an async test", async t => {
      var a = await squareAfter1Second(2);
      var b = await squareAfter1Second(3);
    
      t.equal( a, 4 );
      t.equal( b, 9 );
      t.equal( await squareAfter1Second(5), 25 );
    });
    

----------

## - todo

QUnit.todo( name, callback )
添加一个在运行期间至少有个失败断言的测试
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>name (string)</td>
            <td>名称</td>
        </tr>
        <tr>
            <td>callback (function)</td>
            <td>Function to close over assertions</td>
        </tr>
</table>
回调参数: callback( assert ):
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>assert (object)</td>
            <td>断言方法的新的实例对象</td>
        </tr>
</table>

### 描述

可以使用这个方法去测试还在开发阶段的代码。只要存在一个失败的断言，测试就会通过。

如果全部断言都通过，测试后会有个失败结果，然后应该替换成QUnit.test

### 例子

如何使用QUnit.todo

    QUnit.module( "robot", {
      beforeEach: function() {
        this.robot = new Robot();
      }
    });
    
    // fireLazer hasn't been properly implemented yet, so this is a todo test
    QUnit.todo( "fireLazer returns the correct value", function( assert ) {
      var result = this.robot.fireLazer(); // Currently returns undefined
      assert.equal( result, "I'm firing my lazer!" );
    });

# 断言

## - async
async( [ acceptCallCount = 1 ] )

等待异步操作
<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>acceptCallCount (Number)</td>
            <td>在测试完成之前期望回调次数，默认是1</td>
        </tr>
</table>

### 介绍

assert.async()返回一个回调函数，并且暂停测试进程直到回调方法被调用指定的次数。The callback will throw an Error if it is invoked more often than the accepted call count.

这取代了先前由QUnit.stop()和QUnit.start()提供的功能

### 例子

让QUnit等待定时器里的done方法调用（#TODO，以后不确定的就用#TODO）

	QUnit.test( "assert.async() test", function( assert ) {
	  var done = assert.async();
	  var input = $( "#test-input" ).focus();
	  setTimeout(function() {
	    assert.equal( document.activeElement, input[0], "Input was focused" );
	    done();
	  });
	});
	
为了每一个操作assert.async()，每一个done回调被调用至多一次

	QUnit.test( "two async calls", function( assert ) {
	  assert.expect( 2 );
	
	  var done1 = assert.async();
	  var done2 = assert.async();
	  setTimeout(function() {
	    assert.ok( true, "test resumed from async operation 1" );
	    done1();
	  }, 500 );
	  setTimeout(function() {
	    assert.ok( true, "test resumed from async operation 2" );
	    done2();
	  }, 150);
	});
	
设置异步测试三个退出点。 每个done（）调用都会累加到acceptCallCount。 三次调用后，测试完成

	QUnit.test( "multiple call done()", function( assert ) {
	  assert.expect( 3 );
	  var done = assert.async( 3 );
	
	  setTimeout(function() {
	    assert.ok( true, "first call done." );
	    done();
	  }, 500 );
	
	  setTimeout(function() {
	    assert.ok( true, "second call done." );
	    done();
	  }, 500 );
	
	  setTimeout(function() {
	    assert.ok( true, "third call done." );
	    done();
	  }, 500 );
	});
	
## - deepEqual

深度递归比较，处理原始类型，数组，对象，正则表达式，日期和函数

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 介绍

如果是判断两个对象相不相等，比如说像这样子的，{ key: value } 等于 { key: value }。deepEqual断言可以像equal（）那样子去使用。对于non-scalar values、identity会被deepEqual无视

notDeepEqual（）可用于显式测试深度，严格的不等式

### 例子

比较两个对象

	QUnit.test( "deepEqual test", function( assert ) {
	  var obj = { foo: "bar" };
	
	  assert.deepEqual( obj, { foo: "bar" }, "Two objects can be the same in value" );
	});

## - equal

equal( actual, expected [, message ] )

不严格比较，大致相当于JUnit的assertEquals。

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

equal断言用来简单比较actual和expected这两个参数，跟操作符（==）相似。如果相等，则断言通过；否则就失败。如果失败了，actual和expected这两个值会展示在测试结果上。除了给定的消息。

notEqual用来判断不相等。

strictEqual用来判断严格相等。

### 例子

简单的断言例子

	QUnit.test( "a test", function( assert ) {
	  assert.equal( 1, "1", "String '1' and number 1 have the same value" );
	});
	
一组稍微彻底点的断言

	QUnit.test( "equal test", function( assert ) {
	  assert.equal( 0, 0, "Zero, Zero; equal succeeds" );
	  assert.equal( "", 0, "Empty, Zero; equal succeeds" );
	  assert.equal( "", "", "Empty, Empty; equal succeeds" );
	
	  assert.equal( "three", 3, "Three, 3; equal fails" );
	  assert.equal( null, false, "null, false; equal fails" );
	});
	
## - expect

expect( amount )

指定在测试中会有多少个断言

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>断言的数量</td>
        </tr>
</table>

### 描述

确保在任何运行中的测试的准确断言的数量，用assert.expert（number）注册断言的数量。如果运行测试后断言数量与注册的断言数量不匹配，则不通过测试

### 例子

声明断言数量

	QUnit.test( "a test", function( assert ) {
	  assert.expect( 2 );
	
	  function calc( x, operation ) {
	    return operation( x );
	  }
	
	  var result = calc( 2, function( x ) {
	    assert.ok( true, "calc() calls operation function" );
	    return x * x;
	  });
	
	  assert.equal( result, 4, "2 squared equals 4" );
	});
	

## - notDeepEqual

notDeepEqual( actual, expected [, message ] )

深度递归比较差异（与deepEqual相反，一个判相同，一个判不同），处理原始类型，数组，对象，正则表达式，日期和函数

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

如果是判断两个对象相不相等，比如说像这样子的，{ key: value } 等于 { key: value }。notDeepEqual断言可以像equal（）那样子去使用。对于non-scalar values、identity会被notDeepEqual无视

deepEqual（）可用于显式测试深度，严格的相等式

### 例子

比较两个对象

	QUnit.test( "notDeepEqual test", function( assert ) {
	  var obj = { foo: "bar" };
	
	  assert.notDeepEqual( obj, { foo: "bla" }, "Different object, same key, different value, not equal" );
	});
	
## - notEqual

notEqual( actual, expected [, message ] )

不严格比较，用来判断不等

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

notEqual断言用来简单比较actual和expected这两个参数，跟操作符（!=）相似。如果不相等，则断言通过；否则就失败。如果失败了，actual和expected这两个值会展示在测试结果上，除了给定的消息。

equal用来判断相等

notStrictEqual()用来判断严格不相等

### 例子

简单的例子

	QUnit.test( "a test", function( assert ) {
	  assert.notEqual( 1, "2", "String '2' and number 1 don't have the same value" );
	});

## - notOk

notOk( state [, message ] )

和ok()和CommonJS的assert.ok()相反，与JUnit的assertFalse等价。第一个参数为false就通过测试

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>state</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

notOk()只需要一个参数。如果参数为false，则测试通过；否则就失败。如果提供了第二个参数，会在结果那里展示出来。

### 例子

	QUnit.test( "notOk test", function( assert ) {
	  assert.notOk( false, "false succeeds" );
	  assert.notOk( "", "empty string succeeds" );
	  assert.notOk( NaN, "NaN succeeds" );
	  assert.notOk( null, "null succeeds" );
	  assert.notOk( undefined, "undefined succeeds" );
	
	  assert.notOk( true, "true fails" );
	  assert.notOk( 1, "1 fails" );
	  assert.notOk( "not-empty", "not-empty string fails" );
	});
	
## - notPropEqual

notPropEqual( actual, expected [, message ] )

严格比较对象的属性，判断不相等则通过测试

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

notPropEqual断言用来严格判断对象的属性相不相等，并不是constructors。不相等测试就通过。

### 例子

比较两个对象的属性相不相等

	QUnit.test( "notPropEqual test", function( assert ) {
	  function Foo( x, y, z ) {
	    this.x = x;
	    this.y = y;
	    this.z = z;
	  }
	  Foo.prototype.doA = function () {};
	  Foo.prototype.doB = function () {};
	  Foo.prototype.bar = 'prototype';
	
	  var foo = new Foo( 1, "2", [] );
	  var bar = new Foo( "1", 2, {} );
	  assert.notPropEqual( foo, bar, "Properties values are strictly compared." );
	});
	
## - notStrictEqual

notStrictEqual( actual, expected [, message ] )

严格比较相不相等

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

notStrictEqual断言用来简单比较actual和expected这两个参数，跟操作符（!==）相似。如果不相等，则断言通过；否则就失败。如果失败了，actual和expected这两个值会展示在测试结果上。除了给定的消息。

strictEqual()用来严格判断相等

### 例子

简单的例子

	QUnit.test( "a test", function( assert ) {
	  assert.notStrictEqual( 1, "1", "String '1' and number 1 have the same value but not the same type" );
	});
	
## - ok

ok( state [, message ] )

等价于CommonJS的assert.ok()和JUnit的assertTrue().state参数为true，则则是通过。

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>state</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

在QUnit中最基础的断言，只需要一个参数。如果个为true，则测试通过，如果为false，则测试失败。如果提供了第二个参数msg，msg则会显示在测试结果上。

### 例子

	QUnit.test( "ok test", function( assert ) {
	  assert.ok( true, "true succeeds" );
	  assert.ok( "non-empty", "non-empty string succeeds" );
	
	  assert.ok( false, "false fails" );
	  assert.ok( 0, "0 fails" );
	  assert.ok( NaN, "NaN fails" );
	  assert.ok( "", "empty string fails" );
	  assert.ok( null, "null fails" );
	  assert.ok( undefined, "undefined fails" );
	});
	
## - propEqual

propEqual( actual, expected [, message ] )

比较对象的属性

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

propEqual断言判断对象的属性，相等则通过，不相等则不通过。跟操作符（===）类似。不同于deppEqual，propEqual使用来判断对象的属性和constructors相不相等的。

### 例子

比较两个对象的属性

	QUnit.test( "propEqual test", function( assert ) {
	  function Foo( x, y, z ) {
	    this.x = x;
	    this.y = y;
	    this.z = z;
	  }
	  Foo.prototype.doA = function () {};
	  Foo.prototype.doB = function () {};
	  Foo.prototype.bar = 'prototype';
	
	  var foo = new Foo( 1, "2", [] );
	  var bar = {
	    x : 1,
	    y : "2",
	    z : []
	  };
	  assert.propEqual( foo, bar, "Strictly the same properties without comparing objects constructors." );
	});

## - pushResult

pushResult( data: { result, actual, expected, message } )

报告自定义断言的结果

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>data.result (boolean)</td>
            <td>断言的结果</td>
        </tr>
        <tr>
            <td>data.actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td> data.expected</td>
            <td>期望的值</td>
        </tr>
        <tr>
            <td> data.message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

某些测试套件可能需要表达QUnit的任何内置断言未定义的期望。可以通过将期望封装在JavaScript函数中来满足此需求（#TODO），该JavaScript函数返回布尔值。然后将此值传递给ok()断言

A more readable solution would involve defining a custom assertion. If the expectation function invokes pushResult, QUnit will be notified of the result and report it accordingly.

### 例子

定义自定义断言mod2，在modulo2，如果提供的参数是等价的则通过。

	QUnit.assert.mod2 = function( value, expected, message ) {
	    var actual = value % 2;
	    this.pushResult( {
	        result: actual === expected,
	        actual: actual,
	        expected: expected,
	        message: message
	    } );
	};
	
	QUnit.test( "mod2", function( assert ) {
	    assert.expect( 2 );
	
	    assert.mod2( 2, 0, "2 % 2 == 0" );
	    assert.mod2( 3, 1, "3 % 2 == 1" );
	});
	
## - rejects

rejects( promise[, expectedMatcher][, message ] )

Test if the provided promise rejects, and optionally compare the rejection value.

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>promise (thenable)</td>
            <td>返回的rejects</td>
        </tr>
        <tr>
            <td>expectedMatcher</td>
            <td>比较Reject值的方法</td>
        </tr>
        <tr>
            <td>message (string)</td>
            <td>描述</td>
        </tr>
</table>

### 描述

当测试代码基于一组特定的环境想要返回rejects时，使用assert.rejects()测试并比较

expectedMatcher方法的参数可以是

- 断言通过时返回true的方法

- 错误对象

- A base constructor to use ala rejectionValue instanceof expectedMatcher

- 匹配 rejectionValue.toString()
的正则

###### 提示：为了避免把 message 和 expectedMatcher搞混了，expectedMatcher不能是string

### 例子

纠正错误信息的断言收到自定义错误对象（#TODO）

	QUnit.test( "rejects", function( assert ) {
	
	  assert.rejects(Promise.reject("some error description"));
	
	  assert.rejects(
	    Promise.reject(new Error("some error description")),
	    "rejects with just a message, not using the 'expectedMatcher' argument"
	  );
	
	  assert.rejects(
	    Promise.reject(new Error("some error description")),
	    /description/,
	    "`rejectionValue.toString()` contains `description`"
	  );
	
	  // Using a custom error like object
	  function CustomError( message ) {
	    this.message = message;
	  }
	
	  CustomError.prototype.toString = function() {
	    return this.message;
	  };
	
	  assert.rejects(
	    Promise.reject(new CustomError()),
	    CustomError,
	    "raised error is an instance of CustomError"
	  );
	
	  assert.rejects(
	    Promise.reject(new CustomError("some error description")),
	    new CustomError("some error description"),
	    "raised error instance matches the CustomError instance"
	  );
	
	  assert.rejects(
	    Promise.reject(new CustomError("some error description")),
	    function( err ) {
	      return err.toString() === "some error description";
	    },
	    "raised error instance satisfies the callback function"
	  );
	});
	
## - step

step( [ message ] )

给定测试的进度标记

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>message (string)</td>
            <td>描述</td>
        </tr>
</table>

### 描述

step通常都会通过不报错的，除非你传的参数不是string类型或者没传参数。step使用提供的消息注册测试中的断言。这使得开发者容易检查执行中代码的关键点，特别是在异步测试的时候和使用verifySteps的时候。

与verifySteps一起使用的时候，step断言帮你验证代码执行的顺序和执行次数

### 例子

	QUnit.test( "step test", function( assert ) {
	  assert.expect( 1 );
	  obj.hook = function() {
	    assert.step('Hook is called!');
	  };
	  obj.invokeHookIndirectly();
	});

###### 提示：查看verifySteps，知道更多内容和例子

## - strictEqual

strictEqual( actual, expected [, message ] )

严格类型、值的比较

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>actual</td>
            <td>测试的值</td>
        </tr>
        <tr>
            <td>expected</td>
            <td>已知被比较的值</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

提供严格的类型、值的比较。与操作符(===)等价

### 例子

	QUnit.test( "strictEqual test", function( assert ) {
	  assert.strictEqual( 1, 1, "1 and 1 have the same value and type" );
	}); 
	
## - throws

throws( blockFn[, expectedMatcher][, message ] )

Test if a callback throws an exception, and optionally compare the thrown error.

<table>
    <thead>
        <tr>
            <td>参数</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>blockFn (function)</td>
            <td>执行的方法</td>
        </tr>
        <tr>
            <td>expectedMatcher</td>
            <td>错误处理方法</td>
        </tr>
        <tr>
            <td>message（string）</td>
            <td>描述</td>
        </tr>
</table>

### 描述

当测试代码基于一组特定的环境期望抛出错误时，使用assert.throws()捕捉错误对像并比较是否相同。

expectedMatcher的参数

- 错误对象

- An Error constructor to use ala errorValue instanceof expectedMatcher

- 匹配错误提示的正则

- 必须返回true的回调以通过测试

- 在极少数的环境下，像Closure Compiler，异常信息是不返回的并且会因为发生错误而中断。对于这种情况，别名被捆绑称为引发。 它具有相同的签名和行为，只是一个不同的名称。（#TODO）

### 例子

纠正错误信息的断言收到自定义错误对象（#TODO）

	QUnit.test( "throws", function( assert ) {
	
	  function CustomError( message ) {
	    this.message = message;
	  }
	
	  CustomError.prototype.toString = function() {
	    return this.message;
	  };
	
	  assert.throws(
	    function() {
	      throw "error"
	    },
	    "throws with just a message, not using the 'expected' argument"
	  );
	
	  assert.throws(
	    function() {
	      throw new CustomError("some error description");
	    },
	    /description/,
	    "raised error message contains 'description'"
	  );
	
	  assert.throws(
	    function() {
	      throw new CustomError();
	    },
	    CustomError,
	    "raised error is an instance of CustomError"
	  );
	
	  assert.throws(
	    function() {
	      throw new CustomError("some error description");
	    },
	    new CustomError("some error description"),
	    "raised error instance matches the CustomError instance"
	  );
	
	  assert.throws(
	    function() {
	      throw new CustomError("some error description");
	    },
	    function( err ) {
	      return err.toString() === "some error description";
	    },
	    "raised error instance satisfies the callback function"
	  );
	});
	
## - timeout

timeout( duration )

（#TODO）

### 描述

（#TODO）

### 例子

	QUnit.test( "Waiting for focus event", function( assert ) {
	  assert.timeout( 1000 ); // Timeout of 1 second
	  var done = assert.async();
	  var input = $( "#test-input" ).focus();
	  setTimeout(function() {
	    assert.equal( document.activeElement, input[0], "Input was focused" );
	    done();
	  });
	});
	
--

	QUnit.test( "Waiting for async function", function( assert ) {
	  assert.timeout( 500 ); // Timeout of .5 seconds
	  var promise = asyncFunction();
	  return promise.then( function( result ) {
	    assert.ok( result );
	  } );
	});

--

	QUnit.test( "Waiting in an async test", async assert => {
	  assert.timeout( 500 ); // Timeout of .5 seconds
	
	  let result = await asyncFunction();
	  assert.ok( result );
	});

## - verifySteps

verifySteps( steps [, message ] )

（#TODO）

### 描述

（#TODO）

### 例子

（#TODO）

	QUnit.test( "user-defined hooks execute in correct order", function( assert ) {
	  let lastStep = 'none';
	  let startCount = 0;
	  let middleCount = 0;
	  let endCount = 0;
	
	  obj.start = function() {
	    assert.equal(lastStep, 'none');
	    lastStep = 'start';
	    startCount++;
	  };
	  obj.middle = function() {
	    assert.equal(lastStep, 'start');
	    lastStep = 'middle';
	    middleCount++;
	  };
	  obj.end = function() {
	    assert.equal(lastStep, 'middle');
	    endCount++;
	  };
	
	  return obj.process().then(function() {
	    assert.equal(startCount, 1);
	    assert.equal(middleCount, 1);
	    assert.equal(endCount, 1);
	  });
	});
	
（#TODO）

	QUnit.test( "user-defined hooks execute in correct order", function( assert ) {
	  obj.start = function() {
	    assert.step('start');
	  };
	  obj.middle = function() {
	    assert.step('middle');
	  };
	  obj.end = function() {
	    assert.step('end');
	  };
	
	  return obj.process().then(function() {
	    assert.verifySteps(['start', 'middle', 'end']);
	  });
	});
	
（#TODO）

	QUnit.test( "subscribe/unsubscribe", function( assert ) {
	
	  const publisher = new Publisher();
	  const messages = [];
	
	  const subscriber1 = message => messages.push(`Subscriber #1: ${message}`);
	  const subscriber2 = message => messages.push(`Subscriber #2: ${message}`);
	
	  publisher.subscribe(subscriber1);
	  publisher.subscribe(subscriber2);
	
	  publisher.publish('Hello!');
	
	  publisher.unsubscribe(subscriber1);
	
	  publisher.publish('World!');
	
	  assert.deepEqual(messages, [
	    'Subscriber #1: Hello!',
	    'Subscriber #2: Hello!',
	    'Subscriber #2: World!'
	  ]);
	});
	
（#TODO）

	QUnit.test( "subscribe/unsubscribe", function( assert ) {
	
	  const publisher = new Publisher();
	
	  const subscriber1 = message => assert.step(`Subscriber #1: ${message}`);
	  const subscriber2 = message => assert.step(`Subscriber #2: ${message}`);
	
	  publisher.subscribe(subscriber1);
	  publisher.subscribe(subscriber2);
	
	  publisher.publish('Hello!');
	
	  publisher.unsubscribe(subscriber1);
	
	  publisher.publish('World!');
	
	  assert.verifySteps([
	    'Subscriber #1: Hello!',
	    'Subscriber #2: Hello!',
	    'Subscriber #2: World!'
	  ]);
	});
	
（#TODO）

	QUnit.test( "verify steps", function test(assert){
	    assert.expect( 5 );
	
	    assert.step( "do stuff 1" );
	    assert.step( "do stuff 2" );
	    assert.verifySteps( [ "do stuff 1", "do stuff 2" ] );
	
	    assert.step( "do stuff 3" );
	    assert.verifySteps( [ "do stuff 3" ] );
	} );


  [1]: https://api.qunitjs.com/QUnit/module
