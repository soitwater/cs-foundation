# assets文件夹与static文件夹的区别

## assets文件夹与static文件夹的区别
> 区别一：assets文件是src下的，所以最后运行时需要进行打包，而static文件不需要打包就直接放在最终的文件中了  
> 区别二：assets中的文件在vue中的template/style下用../这种相对路径的形式进行引用，在script下必须用@import的方式引入    
  而static下的文件在.vue中的任何地方都只要使用../这种相对路径的方式引入，

## assets文件夹与static文件夹存放东西的差异
> （1）assets用来放置样式、静态图片，只要src下面的组件中用到的资源就放在assets中。  
> （2）static用来放没有npm包的第三方插件，字体文件。  
> （3）assets与components同级 components下的.vue引用静态文件时，相对路径为 ../assets/wapFront  

## vue如何引入其它静态文件：
> （1）src目录下的资源只能import或require。  
> （2）想静态引入的话，建立一个与src同级的目录例如static，然后把静态资源放入该文件夹下，html的引入路径如下：./static/...（注：试过一定要放在static文件夹下，否则报错）

## 参考
- [Vue项目报错：Uncaught SyntaxError: Unexpected token <](https://www.jb51.net/article/150517.htm)