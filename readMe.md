# 测试脚本：
  测试脚本里面应该包括一个或多个 describe 块，每个 describe 块应该包括一个或多个 it 块 <br>
  describe 块 称为“测试套件”(test suite)，表示一组相关的测试。它是一个函数，第一个参数是测试套件的名称（"假发函数的测试"），第二个参数是一个实际执行的函数<br>

  it块称为“测试用例”（test case），表示一个单独的测试，是测试的最小单位。它也是一个函数，第一个参数是测试用例的名称（"1 加 1 应该等于 2"），第二个参数是一个实际执行的函数 <br>

# 断言库的用法
  断言：
    expect(add(1, 1)).to.be.equal(2);
  所谓“断言”，就是判断源码的实际执行结果与预期结果是否一直，如果不一致就抛出一个错误。上面这句断言的意思是，调用add(1, 1)，结果应该等于2 <br>

  所有的测试用例（it块）都应该含有一句或多句的断言。它是编写测试用例的关键。断言功能由断言库来实现，Mocha本身不带断言库，所以必须先引入断言库。 <br>

    var expect = require('chai').expect;

  断言库有很多种，Mocha并不限制使用哪一种。上面代码引入的断言库是chai，并且指定使用它的 expect 断言风格。<br>

  expect 断言的优点是很接近自然语言，如下：

    //相等或不相等
    expect(4 + 5).to.be.equal(9)
    expect(4 + 5).to.be.not.equal(10)
    expect(foo).to.be.deep.equal({ bar: 'baz' })

    //布尔值为true
    expect('everthing').to.be.ok
    expect(false).to.not.be.ok

    //typeof
    expect('test').to.be.a('string')
    expect({ foo: 'bar' }).to.be.an('object')
    expect(foo).to.be.an.instanceof(Foo)

    //include
    expect([1, 2, 3]).to.include(2);
    expect('foobar').to.contain('foo')
    expect({ foo:'bar', hello: 'universe' }).to.include.keys('foo')

    //empty
    expect([]).to.be.empty
    expect('').to.be.empty
    expect({}).to.be.empty

    //match
    expect('foobar').to.match(/^foo/)

  基本上，expect断言的写法都是一样的。头部是 expect 方法，尾部是断言方法，比如 equal、a/an、ok、match等。两者之间使用 to 或者to.be 链接 <br>

  如果expect断言不成立，就会抛出一个错误。事实上，只要不抛出错误，测试用例就算通过

    it('1 加 1 应该等于 2', function() {});

  上面的这个测试用例， 内部没有任何代码，由于没有抛出了错误， 所以还是会通过。

# Mocha 的基本用法
  有了测试脚本以后，就可以用 Mocha 运行它，mocha命令后面紧跟测试脚本的路径和文件名，可以指定多个测试脚本。

    $ mocha file1 file2 file3

  Mocha默认运行 test 子目录里面的测试脚本。所以，一般都会把测试脚本放在 test 目录里面，然后执行 mocha 就不需要参数了。<br>

  Mocha 默认只执行 test 子目录下面第一层的测试用例，不会执行更下层的用例。为了改变这种行为，就必须加上 --recursive参数，这时 test 子目录下面所有的测试用例--不管在哪一层 ----都会执行

# 通配符
  命令行指定测试脚本时，可以使用通配符，同时指定多个文件
    $ mocha spec/{my, awesome}.js
    $ mocha test/unit/\*.js

  上面的第一行命令，指定执行 spec 目录下面的my.js 和 awesome.js，第二行命令，指定执行 text/unit 目录下面的所有js 文件。<br>

  除了使用 Shell 通配符，还可以使用 Node 通配符
    $ mocha 'test/\*\*/\*.@(js|jsx)'

  上面代码指定运行 test 目录下面任何子目录中、文件后缀名为js 或 jsx 的测试脚本。注意，Node的通配符要放在单引号之中，否则星号(\*)会先被Shell解释<br>

  上面这行Node通配符，如果改用Shell通配符，要携程下面这样
    $ mocha test/{,\*\*/}\*.{js,jsx}

# 命令行参数
  1. --help 或 -h 参数，用来查看 Mocha 的所有命令行参数。
    $ mocha --help
  2. --reporter, -R 参数，用来指定测试报告的格式，默认是 spec 格式
    $ mocha --reporter spec

  除了 spec 格式，官方网站还提供了其他许多[报告格式](http://mochajs.org/#reporters)
    $ mocha --reporter tap

  使用mochawesome 模块，可以生成漂亮的 HTML 格式的报告。

    $ npm install --save-dev mochawesome
    $ ../node_modules/.bin/mocha --recursive --reporter mochawesome

  上面的代码中，mocha 命令使用了项目内安装的版本， 而不是全局安装的版本，因为mochawesome模块是安装在项目内的。<br>
  然后，测试结果报告就在 mochawesome-reports 子目录生成(上面的语句，如果执行一次样式文件没有生成，则需要多执行几次)
  [img](https://github.com/Little-Phoenix/mocha-demo/demo2/test-reporter.png)

  3. --growl, -G 参数，将测试结果在桌面显示(该功能未能显示出来)
    $ mocha --growl

  4. --watch, -W 参数，用来监视指定的测试脚本。只要测试脚本有变化，就会自动运行Mocha
    $ mocha --watch
  上面命令执行以后，并不会退出。

  5. -bail， -b 参数，指定只要有一个测试用例没有通过，就停止执行后面的测试用例。这对持续集成很有用
    $ mocha --bail

  6. --grep, -g 参数，用于搜索测试用例的名称（即 it 块的第一个参数），然后只执行匹配的测试用例。
    $ mocha --grep "1 加 1"

  7. --invert, -i 参数， 表示只运行不符合条件的测试脚本，必须与 --grep 参数配合使用。
    $ mocha --grep "1 加 1" --invert

# 配置文件 mocha.opts
  Mocha 允许在 test 目录下面，放置配置文件 mocha.opts， 把命令行参数写在里面。请先进入 demo3 目录，运行下面的命令。
    $ mocha --recursive --reporter spec --growl
  上面面这个命令有三个参数 --recursive 、--reporter spec 、 --growl <br>

  然后，把这三个参数写入 test 目录下的 mocha.opts 文件

    --reporter spec
    --recursive
    --growl
  然后，执行 mocha 就能取得与第一行命令一样的效果。
    $ mocha

  如果测试用例不是存放在 test 子目录， 可以在mocha.opts 写入一下内容。
    server-test
    --recursive

  上面代码指定运行 server-tests 目录及其子目录之中的测试脚本。


# ES6 测试(安装全局解码器、编辑.babelrs配置文件)
  如果测试脚本是ES6写的，那么运行测试之前，需要先用Babel转码。进入 demo4 目录，打开 test/add.test.js 文件，可以看到这个测试用例是用ES6写的。
    import add from '../src/add.js'
    import chai from 'chai'

    let expect = chai.expect

    describe('加法函数的测试', function(){
      it('1 加 1 应该等于 2', function() {
        expect(add(1, 1)).to.be.equal(2)
      })
    })

  ES6转码， 需要安装Babel
    $ npm install babel-core babel-preset-es2015 --save-dev
  然后，在项目目录下面，新建一个.babelrc 配置文件
    {
      "presets": ["es2015"]
    }

  最后，使用 --compilers 参数指定测试脚本的转码器

    $ ../node_modules/mocha/bin/mocha --compilers js:babel-core/register

  上面代码中， --compilers 参数后面紧跟一个用冒号分隔的字符串，冒号左边是文件的后缀名，右边是用来处理这一类文件的模块名。上面代码表示，运行测试之前，先用 babel-core/register模块，处理一下.js文件。 `由于这里的转码器安装在项目内，所以要使用项目内安装的Mocha；如果转码器安装在全局，就可以使用全局的 Mocha`(全局安装转码器： npm install babel-cli babel-core babel-preset-es2015 -g)

# 异步测试
  Mocha 默认每个测试用例最多执行2000毫秒，如果到时没有得到结果，就报错。对于涉及异步操作的测试用例，这个时间往往是不够的，需要用 -t 或 -timeout 参数指定超时门槛。
    it('测试应该 5000 毫秒后结束', function(done){
      var x = true;
      var f = function(){
        x = false;
        expect(x).to.be.not.ok
        done()
      }
      setTimeout(f, 4000)
    })

  上面的测试用例，需要4000毫秒之后，才有运行结果。所以，需要用 -t 或 --timeout 参数，改变默认的超时设置。
    $ mocha -t 5000 timeout.test.js

  上面命令将测试的超时时限指定为 5000 毫秒 <br>

  另外，上面的测试用例里面，有一个 done 函数。it 块执行的时候，传入一个 done 参数，当测试结束的时候，必须显示调用这个函数，告诉 Mocha 测试结束了。否则，Mocha就无法知道，测试是否结束，会一直等到超时报错。 <br>

  Mocha 默认会高亮显示超过 75 毫秒的测试用例，可以用 -s 或 --slow 调整这个参数。

    $ mocha -t 5000 -s 1000 timeout.test.js

  上面命令指定高亮显示耗时超过1000毫秒的测试用例。

  下面是另外一个异步测试的例子：async.test.js

    it('异步请求应该返回一个对象', function(done){
      request.get('https://api.github.com')
             .end(function(err, res){
               expect(res).to.be.an('object');
               done()
            })
      })

  运行下面的命令，可以看到这个测试会通过。

    $mocha -t 10000 async.test.js

  另外，Mocha内置对Promise的支持，允许直接返回Promise，等到它的状态改变，再执行断言，而不用显示调用 done 方法。请看 promise.test.js

    it('异步请求应该返回一个对象', function(){
      return fetch('https://api.github.com')
        .then(function(res){
          return res.json()
          }).then(function(json){
            expect(json).to.be.an('object')
            })
      })

# 测试用例的钩子
  Mocha 在 describe 块之中，提供测试用例的四个钩子： before()、after()、beforeEach()和 afterEach()。它们会在指定时间执行。

    describe('hooks', function(){
      before(function(){
        // 在本区块的所有测试用例之前执行
      });

      after(function(){
        // 在本区块的所有测试用例之后执行
      });

      beforeEach(function(){
        // 在本区块的每个测试用例之前执行
      });

      afterEach(function(){
        // 在本区块的每个测试用例之后执行
      })
    })

  进入 demo6 子目录，可以看到下面两个例子。 首先是 beforeEach 的例子 beforeEach.test.js

    describe('beforeEach 示例', function(){
        var foo = false;

        beforeEach(function(){
          foot = true
        })

        it('修改全局变量应该成功', function() {
          expect(foo).to.be.equal(true)
        })
    })

  上面代码中， beforeEach 会在 it 之前执行，所以会修改全局变量。<br>
  另一个例子 beforeEach-async.test.js 则是演示，如何在 beforeEach 之中使用异步操作。

    describe('异步 beforeEach 示例', function(){
      var foo = false;

      beforeEach(function(done){
        setTimeout(function(){
          foo = true;
          done()
        }, 50)
      })

      it('全局变量异步修改应该成功', function(){
        expect(foo).to.be.equal(true);
      })
    })
