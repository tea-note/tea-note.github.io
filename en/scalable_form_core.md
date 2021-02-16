# Scalable Form Core API 介绍
## 简介
Scalable Form Core是整个Scalable Form对JSONSchema协议的实现，它主要是在社区组件[react-jsonschema-form](https://github.com/rjsf-team/react-jsonschema-form)进行功能的扩展。Scalable Form Core只是对表单逻辑的抽象层，没有对相关UI的封装。通过Scalable Form Core核心组件，你可以自己实现一个自定义UI的Scalable Form渲染组件。
为了方便使用， Scalable Form体系提供了对[Ant Ddesign](https://github.com/ant-design/ant-design)封装的渲染组件[Scalable Form Antd](https://www.npmjs.com/package/scalable-form-antd)以及对[Ant Design Mobile](https://github.com/ant-design/ant-design-mobile)封装的渲染组件[Scalable Form Antd Mobile](https://www.npmjs.com/package/scalable-form-antd-mobile)。这两个组件的实现就是对Scalable Form Core核心组件的封装。要实现自定义的渲染组件，可以参照这两个组件的源码来进行开发。

## 引入
Scalable Form Core提供npm包的引入方式
```bash
npm i scalable-form-core -S
```

## 使用
下面，我们实现一个表单，支持用户填写name，note等字段，并自行选择所在城市。
注意，scalable-form是一套基于表单数据协议（JSONSchema）的动态表单解决方案。所以我们需要按照[react-jsonschema-form](https://github.com/rjsf-team/react-jsonschema-form)的规则书写一下表单规则描述（schema），并且将schema作为scalable-form的props转入组件。
> 写这个schema会很繁琐，不过放心，scalable-form的一大创新点就在于支持使用可视化的编排组件editor编排产生这个schema，在下文中，你会了解如何使用这个editor

```javascript
import React from "react";
import "./styles.css";
import ScalableForm from "scalable-form-core";

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
          jsonSchema={this.jsonSchema}
          uiSchema={this.uiSchema}
          formData={this.state.formData}
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
     src="https://codesandbox.io/embed/late-bird-xform-antd-77ho8?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="late-bird-xform-core"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
   ></iframe>
   
## 组件属性与API
| 属性名 | 属性说明 | 属性类型 | 默认值 |
| --- | --- | --- | --- |
| locale | <div class="td-desc">表单多语言配置</div> | string | - |
| defaultSubmitButtonComponent | 用于自定义默认提交按钮 | React Component | - |
| widgets | 自定义基础字段集（对于每一个XForm自定义字段类型，都需要有相应的组件实现。必须实现的基础字段列表参见下面备注部分） | array | - |
| fieldTemplate | 除Array、Object类型表单属性类型外的自定义字段模板（通过这个模板，决定XForm里面每一个表单字段的渲染模板），这个是react-jsonschema-form的特性，详见组件对应属性文档 | React Component | - |
| arrayFieldTemplate | Array类型表单属性类型的自定义字段模板（通过这个模板，决定XForm里面每一个表单字段的渲染模板），这个是react-jsonschema-form的特性，详见组件对应属性文档 | React Component | - |
| objectFieldTemplate | Object类型表单属性类型的自定义字段模板（通过这个模板，决定XForm里面每一个表单字段的渲染模板），这个是react-jsonschema-form的特性，详见组件对应属性文档 | React Component | - |
| formContext | 在表单字段中能够读取到的上下文信息 | Object | {} |
| formCode | 表单code | String | '' |
| jsonSchema | react-jsonschema-form中的表单协议的一部分，用于表单渲染 | Object | {} |
| uiSchema | react-jsonschema-form中的表单协议的一部分，用于表单渲染 | Object | {} |
| formData | react-jsonschema-form中的表单协议的一部分，用于表单渲染 | Object | {} |
| bizData | Scalable Form中扩展表单协议的一部分，用于表单渲染 | Object | {} |
| className | 组件父容器指定class | string | '' |

## 自定义组件渲染

在Scalable Form Core中是没有携带任何UI的，所有与表单UI相关的内容完全需要渲染组件自己去实现。其中主要包括两个部分：

1. 表单的布局。Scalable Form Core默认的布局是字段100%纵向布局，如果需要支持复杂布局配置需要在渲染组件中自行实现
1. 通用表单字段UI。下面列出来了目前在Scalable Form渲染组件中实现的表单字段，其中大写字母开头的为react-jsonschema-form中内置实现的基础字段，小写字母开头的为扩展基础字段。对于小写字母开头的字段，如果未实现而表单中配置了这些字段就会出现渲染失败。

### Widgets属性中需要实现的组件列表

#### PC端组件：

```
{
   treeSelect: [Your Component],    // 树选择器
   multiTreeSelect: [Your Component],    // 树选择器（多选）
   SelectWidget: [Your Component],    // 下拉列表
   multiSelect: [Your Component],    // 下拉列表（多选）
   suggestSelect: [Your Component],    // suggest下拉列表
   TextWidget: [Your Component],    // 普通单行文本输入框
   TextareaWidget: [Your Component],    // 普通多行文本输入框
   UpDownWidget: [Your Component],    // 数字选择器
   DateWidget: [Your Component],    // 日期选择器
   DateTimeWidget: [Your Component],    // 日期时间选择器
   dateRange: [Your Component],    // 日期范围选择器
   RadioWidget: [Your Component],    // 普通单选
   CheckboxesWidget: [Your Component],    // 普通复选
   CheckboxWidget: [Your Component],    // 单项复选
   FileWidget: [Your Component],    // 文件/图片上传
   tag: [Your Component],    // 标签选择
   label: [Your Component],    // 普通文本
   richtext: [Your Component],    // 富文本
   rate: [Your Component],    // 评分
   cascader: [Your Component],    // 级联下拉框
   slider: [Your Component],    // 滑动选择器
   sliderRange: [Your Component],    // 滑动范围选择器
   switch: [Your Component]    // 开关选择器
}
```

#### 移动端组件：

```
{
   treeSelect: [Your Component],    // 树选择器
   label: [Your Component],    // 普通文本
   dateRange: [Your Component],    // 日期范围选择器
   multiSelect: [Your Component],    // 下拉选择（多选）
   UpDownWidget: [Your Component],    // 数字选择
   SelectWidget: [Your Component],    // 下拉选择
   TextWidget: [Your Component],    // 普通单行文本输入框
   TextareaWidget: [Your Component],    // 普通多行文本输入框
   DateWidget: [Your Component],    // 日期选择器
   DateTimeWidget: [Your Component],    // 日期时间选择器
   RadioWidget: [Your Component],    // 单选
   CheckboxesWidget: [Your Component],    // Switch开关选择
   CheckboxWidget: [Your Component],    // 复选
   FileWidget: [Your Component],    // 图片上传
   cascader: [Your Component],    // 级联下拉框
   slider: [Your Component],    // 滑动选择器
   sliderRange: [Your Component],    // 滑动范围选择器
   switch: [Your Component]    // Switch开关选择器（移动端这两个组件相同）
}
```
