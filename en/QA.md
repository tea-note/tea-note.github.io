# 常见问题

1. 为什么我使用Scalable Form渲染组件渲染出来的表单样式是错乱的？<br />
答：组件中默认build出来的index.js中没有打包antd的样式，需要自行加载对应的antd的样式文件；对于使用babel-plugin-import按需加载antd的工程，组件中提供打包出来的index-with-antd-bundle.js，这个包中使用babel-plugin-import打包了组件中需要使用的antd样式。

2. 使用XForm体系是否一定需要配套使用服务端？<br />
答：Scalable Form组件不一定非要依赖服务端。Scalable Form Builder可视化配置出来的JSONSchema协议完全可以直接应用于XForm的渲染组件，前端自身就能够形成一个完整的应用闭环。当然，如果需要对JSONSchema协议进行存储、或者使用一些需要依赖服务端的特性，如数据源、校验器、场景等，必须要配套使用服务端。

3. 使用Scalable Form体系是否一定需要使用Scalable Form提供的服务端接口或者HSF服务？<br />
答：Scalable Form提供的服务端接口，是为了满足部分应用场景下快速接入。理论上，如果能够按照规范完全实现自己的服务，在自己的业务服务端封装相应接口。Scalable Form组件均提供了灵活的接口注册方法，能够做到对服务端的灵活接入。

4. 我们的业务有自己特殊的表单业务字段需要能够配置和渲染，Scalable Form能够支持吗？<br />
答：目前能够支持。可参见组件的registerWidget API以及如何开发自定义字段的文档

5. 使用Scalable Form渲染组件，图片/文件上传为什么无效，并且会在控制台有`[xform]: customUploadRequest props need to be configured before using image/file upload. For more detail, please see customUpload props in Upload component in ant design project`的提示？<br />
答：Scalable Form渲染组件不会托管上传的图片/文件服务，因而需要接入的业务自行实现上传业务逻辑。这一部分可以参考Scalable Form渲染组件的`customUploadRequest`属性

