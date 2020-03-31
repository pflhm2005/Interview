- H5性能优化
1. 大图片压缩 1x/2x/3x => 压缩3x、安卓替换成webp、ios替换成png
2. 异步加载非首屏数据，懒加载非首屏图片 => stateCallback
3. 移除无用包(线上编译通过eslint检测unused)，使用公共离线包。
4. 离线包 => react、react-dom等公共JS库直接在html中使用CDN链接，数据包接入离线包。
5. 数据预加载，schema加上预请求接口，通过jsb快速获取数据

- 移动端开发
1. 低版本安卓1px不显示，需要1.05px
2. 低版本chrome内核无法用line-height垂直居中，需要用绝对定位+scale
3. 横向滚动条最右边的dom右边距不生效，需要插一个空dom
4. ipx需要做底部兼容
5. 刘海屏需要对status做兼容处理
6. IOS默认带有弹性滚动，需要做兼容处理
7. 指定行文案css
overflow: hidden;
text-overflow: ellipsis;
-webkit-box-orient: vertical;
display: -webkit-box;
-webkit-line-clamp: 1;
8. 图层不显示的问题 => 加上transform: translate3d(0, 0, 0)
9. 页面渲染与代码不同步 => 改变opacity会强制重绘
10. 一般h5自带flexible，自动会将px转为rem，但是需要注意的是行内样式不会转换，另外PX也会被忽略

- 调试
1. SSR的开发，使用本地json文件
2. 跨域用webpack反向代理
devServer: {
    proxy: {
      '/xxx/\*\*': {
        target: 'xxx',
        changeOrigin: true,
      },
    }
}
3. 联调利用抓包工具Charles，主要是看接口请求与相应

- webpack优化
1. babel-plugin-import 按需引入组件
具体原理 将整个库的引用转换为单个模块的引入
import {Buttom} from 'antd' => import \_Buttom from 'antd/lib/button';
2. ScriptExtHtmlWebpackPlugin可以强化HtmlWebpackPlugin插件 => 比如给script标签添加属性或设置为async
3. 多entry的webpack，通过cacheGroups的配置将特殊库(比如echarts)单独拆分出来
