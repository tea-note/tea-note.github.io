# Scalable Form Antd

## 简介
Scalable Form Antd渲染组件可以用来方便的渲染在Scalable Form Editor中搭建的表单到PC Web页面中。Scalable Form Antd组件是基于Scalable Form Core组件的扩展，表单的渲染基于[Ant Design](https://github.com/ant-design/ant-design)组件。

## 引入
Scalable Form Antd提供npm包的引入方式
```bash
npm i scalable-form-antd -S
```

## Hello World
在第一个例子中，我们需要渲染一个表单，支持用户填写name字段，并自行选择所在城市。
注意，scalable-form是一套基于表单数据协议（JSONSchema）的动态表单解决方案。所以我们需要按照[react-jsonschema-form](https://github.com/rjsf-team/react-jsonschema-form)的规则书写一下表单规则描述（schema），并且将schema作为scalable-form的props转入组件。
> 写这个schema会很繁琐，不过放心，scalable-form的一大创新点就在于支持使用可视化的编排组件editor编排产生这个schema，在下文中，你会了解如何使用这个editor

```javascript
import React from "react";
import "./styles.css";
import ScalableForm from "scalable-form-antd";

export default class FormAntdExample extends React.PureComponent {
 constructor(props) {
   super(props);
   this.state = {
     formData: {
       name: "",
       city: "hangzhou"
     }
   };
   this.jsonSchema = {
     title: "Scalable Form render sdk with Ant Design components",
     description: "",
     type: "object",
     required: ["name"],
     properties: {
       name: {
         type: "string",
         title: "名称",
         default: "",
         maxLength: 15
       },
       city: {
         type: "string",
         title: "请选择城市",
         default: "",
         enum: ["beijing", "shanghai", "hangzhou"],
         enumNames: ["北京", "上海", "杭州"]
       }
     }
   };
   this.uiSchema = {
     name: {
       "ui:help": '关于"名称"字段的帮助说明',
       "ui:options": {
         placeholder: "请输入名称"
       }
     },
     city: {
       "ui:widget": "radio",
       "ui:options": {
         vertical: false,
         validate: [
           {
             type: "empty",
             message: "请选择"
           }
         ]
       }
     }
   };
 }

 handleChanged = (formData, bizData) => {
   console.log("ScalableForm Changed!", formData);
   console.log("ScalableForm Changed!", bizData);
   this.setState({
     formData: { ...formData }
   });
 };

 handleSubmit = (formData, bizData) => {
   console.log("ScalableForm Submitted!", formData);
   console.log("ScalableForm Submitted!", bizData);
 };

 render() {
   return (
     <div className="scalable-form-demo-element">
       <ScalableForm
         jsonSchema={this.jsonSchema} //jsonSchema用于表述表单字段信息
         uiSchema={this.uiSchema} //uiSchema用于控制表单字段的UI组件
         formData={this.state.formData} //formData是表单中填写的字段数据
         onChange={this.handleChanged}
         onSubmit={this.handleSubmit}
       />
     </div>
   );
 }
}
```

## 在codesandbox演示
这里查看第一个表单的例子在 codesandbox 的演示。

<iframe
    src="https://codesandbox.io/embed/late-bird-x6qoo?fontsize=14&hidenavigation=1&theme=dark"
    style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
    title="late-bird-x6qoo"
    allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
    sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
  ></iframe>

## 组件属性与API

属性名 | 属性说明 | 属性类型 | 默认值
---- | ---- | ---- | ---- 
locale | <div class="td-desc">指定组件的多语言，其值为标准的[语言]和[国家]中间用中划线分隔（例如en-us），默认值为浏览器设置的语言，如果传入不能识别的值或自动启用英文</div> | navigator.language.toLocaleLowerCase()
formContext | 指定表单的Context，其中的属性值在所有注册的字段（包括自定义字段）中都能够通过this.props.formContext获取 | Object | {}
customUploadRequest | 自定义XForm的图片/文件上传服务，从3.0.0版本起不再内置提供图片/文件上传服务，必须自己实现该属性才能使用图片/文件上传以及富文本编辑器的添加图片功能。该属性与ant design中Upload组件的[customRequest属性](https://ant.design/components/upload/#customRequest)使用方法相同 | function(file, onSuccess, onError),其中file参数为上传文件的File对象，onSuccess回调方法中必须传入上传图片url作为参数，onError为上传失败回调方法 | -
popupContainer | Scalable Form中popup组件渲染的根节点，默认为XForm组件的容器 | func | `() => {return xform_root_element;}`
labelType | 表单label与字段你的排列方式，值为inline或vertial | enum枚举型 | inline
alignType | 表单field的对齐方式，值为inline或vertical（当labelType为inline的情形下该配置才有效） | enum枚举型 | vertical
labelAlign | 表单label的对齐方式，值为left（label左对齐）或right（label右对齐）（当labelType为inline的情形下该配置才有效） | enum枚举型 | right
itemNumberInRow | 当表单field的对齐方式为inline时，可以通过设置该属性决定一行显示几个field（alignType不是'inline'情形下无效） | number | 2
formItemLayout | 调整表单的label与wrapper之间的布局，采用ant的24栅格grid布局方式设置 | object | {labelCol: {span: 6}, wrapperCol: {span: 18}}
registerWidgets | 向Scalable Form组件注册自定义widget（值为一个object，object key为注册的widget名称，object value为注册的自定义widget。注册后可以通过uiSchema中指定`'ui:widget': [key]`来使用。） | object | {}
defaultSubmitButton | 是否使用xform默认的“提交”按钮（默认不使用，`如需使用默认按钮必须设置该属性开启`） | boolean | false
noLabel | 表单各个字段是否有label。有些场景的表单不需要有label，可以设置该项为true | boolean | false
className | 为组件增加自定义class | string | ''
disabled | 把Scalable Form表单的每一项设置为禁用 | bool | false
readonly | 把Scalable Form表单的每一项设置为只读（该属性只对input与textarea类型有效，其他类型无效） | bool | false
jsonSchema | 表单的jsonSchema描述 | object | undefined
uiSchema | 表单的uiSchema描述 | object | undefined
formData | 表单的formData描述 | object | undefined
bizData | 表单的业务属性bizData描述（业务属性不是必须的） | object | undefined
sequence | 表单中field的展示顺序配置，其值为一个由表单字段code值组成的数组。默认按照schema中object的顺序排序 | array | undefined
validation | 自定义的表单校验规则，该属性接受一个方法，该方法中会注入formData以及bizData，要求返回是否校验通过的boolean值 | function | function() { return true; }
onImagePreview | 当用户点击表单中图片时触发，该属性接受一个方法，该方法中会注入点击图片的url地址，可以用来做自定义的图片预览处理。默认为打开新的浏览器窗口来预览图片 | function | function(url) {window.open(url);}
onError | 异常处理属性，会上报组件catch到的错误 | function | -
onChange | 当用户填写表单是触发，该属性接受一个方法，该方法中会注入表单onChange之后的formData和bizData | function | function() {}
onSubmit | 当用户触发提交表单是触发，该属性接受一个方法，该方法中会注入表单提交时的formData和bizData | function | function() {}

如果你获取到了组件的ref，你可以调用下面这些API：
* XFormValidateSync()：该方法会触发Scalable Form的表单校验，该方法为同步校验方法，该方法会返回校验通过或不通过的布尔值
* XFormSubmit()：该方法会触发Scalable Form的表单提交，允许用户在自定义的地方触发表单的提交操作。在表单提交前会自动执行表单校验，之后触发定义的onSubmit属性中定义的提交方法
* XFormInitFormData()：获取Scalable Form初始化时的formData，一些特殊的重置操作使用
* XFormCurrentFormData()：该方法会返回当前Scalable Form表单中的formData
* XFormSetFormData(formData)：该方法允许设置Scalable Form当前的formData
* XFormReset()：重置Scalable Form表单
* XFormBizData()：该方法会返回当前Scalable Form表单中的bizData
* XFormFetchAllFromDataSource()：该方法会触发当前Scalable Form表单所有配置了数据源的字段更新
* XFormFetchFromDataSource(name)：该方法会触发当前Scalable Form表单中字段名称为name的字段数据源更新
