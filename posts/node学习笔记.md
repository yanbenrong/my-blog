# Node学习笔记

## require函数都做了什么？

 伪代码参考

```js
function require(modulePath){
    // 1.将modulePath转换为绝对路径: D:\\xxxx
    // 2.判断是都有该模块的缓存
    // if(require.cache["D:\\xxxx"]){
    // return require.cache["D:\\xxxx"
    // }
    // 3. 读取文件内容
    // 4. 包裹到函数环境中
    function __require(module,exports,require,__dirname,__filename){
        // 文件内容
    }
    // 创建module对象
    module.exports = {}
    const exports = module.exports
    
    // 绑定this 
    __require.call(exports,module,exports,require,module.path,module.filename)
    // 最终会返回module.exports
    return module.exports
}

```





