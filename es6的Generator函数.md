##es6的Generator函数
>Generator函数是es6用来解决异步问题的。
####异步
异步是不难理解的。比如，你早上起来煮咖啡，这时你不用一直在那里呆着什么事都不干，就等咖啡煮好，而是看看报纸，或者做其它事情，等一段时间咖啡煮好了，你再去倒咖啡喝。这就是异步，代码执行一段请求时不用一直卡在那里等待请求，而是继续执行下面的代码，等请求完成再回来执行请求完成后的代码。
####基本概念
Generator函数的写法和普通函数无异，只是多了个*号，如

	function* generatorTest(){
        console.log('###');
        yield 'yang';
        console.log('$$$');
        yield 'yao';
        console.log('%%%');
        return 'lin';
    }

不同的是调用函数时，函数不会执行，而是直接返回一个**遍历器对象**，遍历器对象具有**next**属性，可以通过next对对象的属性逐个访问，直到完成对对象所有属性的访问。如

	let g = generatorTest();
    g.next();

执行next后返回一个对象，改对象有value和done两个属性，vaule表示**yield**后面带的值，done为true表示后面没有yield了，false则表示后面还有yield可以执行。如

	function* generatorTest(){
        let a = 1;
        yield ++a;
        return ++a;
    }
    let g = generatorTest();
    console.log(g.next()); //{done:false,value:2}
    console.log(g.next()); //{done:true,value:2}

* yield只能在generator函数中使用，否则回发生错误。
* 返回的为遍历器对象，所以可以使用for...of直接遍历取值。由于当done为true时，for...of会停止取值，所以最后return后面的值不会被遍历出来。
* yield可以在任何位置，在表达式中时需要加括号

如：

	function* generatorTest(){
        let a = 3;
        b = a + 5;
        console.log(a + b + (yield 4));
        console.log('@@');
        yield 'yang';
    }

####与Iterator接口的关系
对象本身具有属性Symbol.interator，为对象对应的遍历器生成函数，即当将对象当成遍历器对待时，会调用改对象对应的Symbol.interator函数。而generator函数返回的就是一个遍历器对象，所以可以把generator赋值给对象的Symbol.interator属性。如

	let obj = {};
    obj[Symbol.iterator] = function* (){
        yield 'yang';
        yield 'yao';
        yield 'lin';
    };
    console.log([...obj]);  //["yang", "yao", "lin"]

####next方法参数
在执行next参数时可以传参数，参数会传到yield中，如

	let generatorTest  = function* (){
        console.log('y0');
        let y1 = yield 'yang'; //传入值后，此时yield值为value，并将其赋值给变量y1
        console.log(y1);
        yield 'yao';
        yield 'lin';
    };
    let g = generatorTest();
    g.next();
    g.next('value'); //传入值value给yield

    //输出：
    // y0
    // value


* next传的值为上一个yield的值，所以第一个next无法传值。

* 如果generator函数内部发生错误，则可以被函数外部的catch捕获。发生错误后，函数再执行next时会认为已经结束，即返回value为undefined，done为true。

####Generator.prototype.return()
当执行return时，generator函数返回的遍历器对象会终止遍历，如

	let generatorFun = function* (){
        yield 'yang';
        yield 'yao';
        yield 'lin';
    };
    let g = generatorFun();
    console.log(g.next());         //{value: "yang", done: false}
    console.log(g.return('over')); //{value: "over", done: true}
    console.log(g.next());         //{value: undefined, done: true}
上面遍历到第二个是执行了return，此时遍历器遍历结束，后面再执行next会返回done为true。

如果在执行到try后，再调用return，并且try附有finally，则后面的next会继续执行完finally里面的yield，如

	let generatorFun = function* (){
        yield 'yang';
        try{
            yield 'yao';
        }finally {
            yield 'a';
            yield 'b';
        }

        yield 'lin';
    };
    let g = generatorFun();
    console.log(g.next());         //{value: "yang", done: false}
    console.log(g.next());         //{value: "yao", done: false}
    console.log(g.return('over')); //{value: "a", done: false}
    console.log(g.next());         //{value: "b", done: false}
    console.log(g.next());         //{value: "over", done: true}

####yield*表达式
将两个generator函数结合起来，如

	let generatorFun1 = function*(){
        yield 'other'
    };
    let generatorFun = function* (){
        yield 'yang';
        yield* generatorFun1();
        yield 'lin';
    };
    let g = generatorFun();
    console.log(g.next().value); //yang
    console.log(g.next().value); //other
    console.log(g.next().value); //lin

将generator1和generator结合起来，相当于一个generator函数。

