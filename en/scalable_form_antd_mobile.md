# Scalable Form Antd Mobile

# 简介

- Scalable Form提供Scalable Form Antd Mobile组件来方便渲染在表单设计器中创建的移动端的表单。Scalable Form Antd Mobile组件是基于Scalable Form Core组件，基于[Antd Mobile](https://github.com/ant-design/ant-design-mobile)的渲染组件。

## 引入
Scalable Form Antd Mobile提供npm包的引入方式
```bash
npm i scalable-form-antd-mobile -S
```

## Hello World
在这个例子中，我们需要渲染一个表单，支持用户填写name字段，并自行选择所在城市。
```javascript
import React from "react";
import "./styles.css";
import ScalableForm from "scalable-form-antd-mobile";

export default class FormExample extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {
      formData: {
        name: "",
        city: "hangzhou"
      }
    };
    this.jsonSchema = {
      title: "Scalable Form render sdk with Ant Design Mobile components",
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
     src="https://codesandbox.io/embed/late-bird-xform-antd-qspcg?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="late-bird-xform-antd-mobile"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
   ></iframe>
   
## 组件属性与API

| 属性名 | 属性说明 | 属性类型 | 默认值 |
| --- | --- | --- | --- |
| locale | <div class="td-desc">指定组件的多语言，其值为标准的[语言]和[国家]中间用中划线分隔（例如en-us），默认值为浏览器设置的语言，如果传入不能识别的值或自动启用英文</div> | navigator.language.toLocaleLowerCase() |  |
| formContext | 指定表单的Context，其中的属性值在所有注册的字段（包括自定义字段）中都能够通过this.props.formContext获取 | Object | {} |
| customUploadRequest | 自定义图片上传服务，当业务需要使用图片选择器组件时customUploadRequest和customUpload两个属性二选一必传 | function(files, options, onSuccess),其中files中包含上传文件相关的数据，具体见antd-mobile中imagePicker组件中的file数据结构；options中包含当前选择器的一些状态信息，主要包括maxFileNum（最大允许上传数）、maxFileSize（最大允许上传文件大小）、currentFileNum（当前上传的文件数目）;onSuccess回调中必须传入上传图片url（只有一个元素的数组）或urls集合作为第一个参数 | - |
| customUpload | 自定义图片选择+图片上传，当业务需要使用图片选择器组件时customUploadRequest和customUpload两个属性二选一必传 | function(options, onSuccess),options中包含当前选择器的一些状态信息，主要包括maxFileNum（最大允许上传数）、maxFileSize（最大允许上传文件大小）、currentFileNum（当前上传的文件数目）;onSuccess回调中必须传入上传图片url（只有一个元素的数组）或urls集合作为第一个参数 | - |
| registerWidgets | 向Scalable Form组件注册自定义widget（值为一个object，object key为注册的widget名称，object value为注册的自定义widget。注册后可以通过uiSchema中指定`'ui:widget': [key]`来使用。） | object | {} |
| className | 为组件增加自定义class | string | '' |
| disabled | 把Scalable Form表单的每一项设置为禁用 | bool | false |
| readonly | 把Scalable Form表单的每一项设置为只读（该属性只对input与textarea类型有效，其他类型无效） | bool | false |
| jsonSchema | 表单的jsonSchema描述 | object | undefined |
| uiSchema | 表单的uiSchema描述 | object | undefined |
| formData | 表单的formData描述 | object | undefined |
| bizData | 表单的业务属性bizData描述（业务属性不是必须的） | object | undefined |
| sequence | 表单中field的展示顺序配置，其值为一个由表单字段code值组成的数组。默认按照schema中object的顺序排序 | array | undefined |
| validation | 自定义的表单校验规则，该属性接受一个方法，该方法中会注入formData以及bizData，要求返回是否校验通过的boolean值 | function | function() { return true; } |
| onError | 异常处理属性，会上报组件componentDidCatch中捕获到的错误 | function(error, info) | - |
| onChange | 当用户填写表单是触发，该属性接受一个方法，该方法中会注入表单onChange之后的formData和bizData | function | function() {} |
| onSubmit | 当用户触发提交表单是触发，该属性接受一个方法，该方法中会注入表单提交时的formData和bizData | function | function() {} |


Scalable Form组件中暴露出一些API，可以通过refs的方式来调用

- XFormValidateSync()：该方法会触发Scalable Form的表单校验，该方法为同步校验方法，会默认忽略掉`type=server`类型的异步校验方法，该方法会返回校验通过或不通过的布尔值
- XFormSubmit()：该方法会触发Scalable Form的表单提交，允许用户在自定义的地方触发表单的提交操作。在表单提交前会自动执行表单校验，之后触发定义的onSubmit属性中定义的提交方法
- XFormInitFormData()：获取Scalable Form初始化时的formData，一些特殊的重置操作使用
- XFormCurrentFormData()：该方法会返回当前Scalable Form表单中的formData
- XFormSetFormData(formData)：该方法允许设置Scalable Form当前的formData
- XFormReset()：重置Scalable Form表单
- XFormBizData()：该方法会返回当前Scalable Form表单中的bizData

## 与PC端的Scalable Form Antd渲染组件的不同

由于移动端的特殊性，Scalable Form Antd Mobile组件与PC端的Scalable Form Antd渲染组件有些许不同：

- 不支持defaultSubmitButton，必须通过XFormSubmit方法来进行表单提交
- 不支持多种布局方式的配置，因为移动端受屏幕小的限制，UI布局基本一致，不需要做灵活配置
- 不支持noLabel的配置方式，移动端不会有这种场景
