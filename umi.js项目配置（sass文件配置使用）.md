#### umi.js项目配置（sass文件配置使用）

1、umi.js项目 配置文件 ：有两钟。这两种只能二选一。

umirc.js 文件 和 config>config.ts。随后配置的属性都是一致的。

2、此处以 config.ts文件配置举例。

3、首先得在项目中安装 sass-loader、node-sass，以及umi 处理sass命令的plugin. 库为@umijs/plugin-sass

4、安装完之后，在defineConfig里面增加一个sass: {}，即可。

```
export default defineConfig({
 	sass: {},
})
```

