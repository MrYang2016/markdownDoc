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

执行next后返回一个对象，改对象有value和done两个属性，vaule表示
