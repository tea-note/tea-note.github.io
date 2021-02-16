# Scalable Form Editor

## 简介

Scalable Form中提供Scalable Form Editor表单设计器组件来与渲染组件配套使用。通过Scalable Form Editor可视化配置生成渲染组件能够识别的schema，配合使用渲染组件，就能够实现配置出来的表单在任何APP中快速落地。

## 引入

```bash
npm i scalable-form-editor -S
```

## Hello World

在第一个例子中，我们需要渲染一个表单可视化编辑器。

```javascript
import React from "react";
import "./styles.css";
import ScalableFormEditor from "scalable-form-editor";

class FormEditorExample extends Component {
  constructor(...args) {
    super(...args);
    this.state = {
        uiSchema: {},
        jsonSchema: {},
        bizData: {},
        sequence: []
        formData: {}
    };
  }

  handleSubmit = (formCode, {jsonSchema,uiSchema, formData, bizData, sequence}) => {
    console.log('submit by editor', jsonSchema, uiSchema, formData, bizData, sequence);
  };

  render() {
    return (
      <ScalableFormEditor
        jsonSchema={this.state.jsonSchema}
        uiSchema={this.state.uiSchema}
        formData={this.state.formData}
        bizData={this.state.bizData}
        sequence={this.state.sequence}
        onSubmit={this.handleSubmit}
      />
    );
  }
}
```

## 在codesandbox演示
这里查看第一个表单的例子在 codesandbox 的演示。

<iframe
     src="https://codesandbox.io/embed/late-bird-xform-antd-mobile-fz9m4?fontsize=14&hidenavigation=1&theme=dark&view=preview"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="late-bird-xform-editor"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
   ></iframe>
   
## 组件属性与API
| 属性名 | 属性说明 | 属性类型 | 默认值 |
| --- | --- | --- | --- |
| width | <div class="td-desc">设置组件容器的宽度，为保证正常展示，最小宽度为1000px</div> | number | 'auto' |
| height | 设置组件容器的高度 | number | 'auto' |
| platformConfigSupport | 设置是否支持适配端切换，如果设置为true，则顶部工具栏中才会有适配端切换入口。对于新建的表单，默认为PC端；对于编辑的表单，会依据上次保存编辑时的适配端来显示 | boolean | false |
| platform | 设置组件支持的适配端，如果设置了该属性，默认全部表单都会采用该适配端 | laptop(PC端)、mobile（手机端）、both（两者） | - |
| locale | 指定组件的多语言，其值为标准的[语言]和[国家]中间用中划线分隔（例如en-us），默认值为浏览器设置的语言，如果传入不能识别的值或自动启用英文 | navigator.language.toLocaleLowerCase() |  |
| emptyPlaceholder | 自定义组件空状态时的占位符，适用于有统一空状态占位设计的场景，使组件设计风格统一 | React Component | 有默认占位符 |
| popupContainer | 指定组件中modal、popup组件渲染的DOM节点，默认为document.body | function | () => document.body |
| onImagePreview | 当用户点击表单中图片时触发，该属性接受一个方法，该方法中会注入点击图片的url地址，可以用来做自定义的图片预览处理。默认为打开新的浏览器窗口来预览图片 | function | function(url) {window.open(url);} |
| defaultActionButtons | 配置是否使用内置的动作按钮，内置的动作按钮为“保存”操作，位置在表单设计器的右下角，允许设置该属性为false来使用自定义的动作按钮 | Boolean | true |
| renderActionButtons | 自定义渲染动作按钮，该属性为一个返回待渲染动作按钮的React Element | function(saveAction)，方法中会传入可以直接调用【保存】操作逻辑，与`handleFormSave`相同 | - |
| namespace | 改属性定义了store中的namespace，主要用来防止多个组件实例导致的store数据共享问题。强烈建议设置改属性并要保证值的唯一性 | String | xformBuilderDefault |
| systemFieldSupport | 是否开启对表单模板字段功能的支持 | Boolean | false |
| commonFieldSupport | 是否开启对通用字段功能的支持 | Boolean | false |
| bizDataSupport | 是否开启对字段业务属性的支持 | Boolean | false |
| supportLangList | 在组件支持的多语言中只开启对指定列表中的多语言类型的表单配置，如果不配置该属性，则默认内置多语言都支持（该属性只有在langConfigSupport属性设置为true时才生效，默认启用的语言类型的规则为：如果设置的locale属性值在supportLangList列表中，则默认启用语言与设置的组件语言相同，否则启用设置的supportLangList列表中的第一个语言） | Array | - |
| supportFieldList | 在组件内置的自定义字段中只开启对指定列表中的自定义字段的配置的支持，如果不配置该属性，则默认内置自定义字段都支持配置（该属性值为内置自定义字段的code值的数组，内置的自定义字段以及code值见下面的说明） | Array | [] |
| supportConfigList | 在组件内置的字段配置项中只开启对指定列表中的字段的配置的支持，如果不配置该属性，则默认内置字段配置项都支持配置（该属性值为内置字段配置项的code值的数组，内置的字段配置项code值见下面的说明） | Array | ['code', 'placeholder', 'description', 'value', 'dataSource', 'validate', 'server', 'require', 'hidden'] |
| getInstance | 通过该属性，能够获取到组件暴露出来的一些API，详见下面的具体API说明 | Function | - |
| beforeSubmit | 在XFormBuilder提交编辑前执行，在提交前可以做一些校验；return false;可以组织XFormBuilder提交 | Function | `function(Object schema{jsonSchema, uiSchema, formData, bizData, sequence}) {return true;}` |
| onError | 异常处理属性，会上报组件componentDidCatch中捕获到的错误 | function(error, info) | - |
| onSubmit | 允许在提交编辑之后执行一些自定义逻辑，该属性的方法中会注入此次提交生成的formCode值 | Function | `function(formCode, Object schema{jsonSchema, uiSchema, formData, bizData, sequence}) {}` |
| registerWidgets | 允许通过该属性注册可以配置的自定义表单字段 | array | [] |

> registerWidgets为高级用法，该属性的值为一类特定格式的Object组成的数组，表示可以注册多种不同的组件配置。

可以通过组件的getInstance属性来获取到组件暴露的一些API：

- handleFormPreview(): 调用该方法会直接弹出表单设计器的【预览】模态框。适用于自定义预览按钮的使用场景（2.0.0版本以上，预览会集中在顶部工具栏，不再支持自定义预览）
- handleFormSave(): 调用该方法会直接执行表单设计器的【保存】逻辑，表单数据会自动保存或更新。适用于自定义预览按钮的使用场景

## 组件内置支持的自定义字段

下面是XForm表单设计器内置支持的自定义字段类型（括号中为字段对应的code值，右侧中括号中为支持的端，分为PC和无线端）

- 段落（label）[PC, mobile]
- 单行文本框（input）[PC, mobile]
- 多行文本框（textarea）[PC, mobile]
- 数字选择器（number）[PC, mobile]
- 单选（radio）[PC, mobile]
- 复选（checkbox）[PC, mobile]
- 单项复选（booleanCheckbox）[PC, mobile]
- 图片上传（upload）[PC, mobile]
- 附件上传（file）[PC]
- 多选下拉框（multiSelect）[PC, mobile]
- 单选下拉框（select）[PC, mobile]
- 日期选择器（date）[PC, mobile]
- 日期范围选择器（dateRange）[PC, mobile]
- 日期时间选择器（datetime）[PC, mobile]
- 树形选择器（treeSelect）[PC, mobile]
- 多选树形选择器（multiTreeSelect）[PC]
- 富文本（richtext）[PC]
- suggest下拉框（suggestSelect）[PC]
- 评分（rate）[PC]
- 开关（booleanSwitch）[PC, mobile]
- 滑动输入条（slider）[PC, mobile]
- 滑动范围输入条（sliderRange） [PC, mobile]
- 级联下拉框（cascaderSelect）[PC, mobile]

## 内置配置字段

### 通用配置字段

下面是XFormBuilder内置支持的配置字段（括号中为配置字段对应code值）

- 在【基础配置】区域的配置项：

  - 字段名称（name）：用来配置字段的label，默认必填
  - 字段code值（code）：用来配置字段的唯一code值，默认会生成一个10为字符串编码
  - 字段占位符（placeholder）：用来配置字段的placeholder值
  - 字段说明（description）：用来配置一句对该字段的描述
  - 默认值（value）：用来配置字段的初始渲染默认值
  - 必填字段（require）：用来配置该字段是否必填的校验
  - 是否隐藏字段（hidden）：用来配置该字段是否隐藏
  - 字段是否禁用（disabled）： 用来配置该字段是否禁用
- 在【高级配置】区域的配置项：

  - 数据源（dataSource）：用来配置该字段的数据源
  - 静态校验器（validate）：用来配置字段的静态校验器（包括纯数字校验、邮箱校验等）
  - 动态校验器（server）：用来配置字段的动态校验器
  - 层叠字段（cascade）：用来配置字段是否加入层叠设置

### 特殊配置字段

- 在【基础配置】区域的配置项：

  - 文件大小（图片/文件）：用来配置上传的图片/文件的大小限制
  - 文件数量（图片/文件）：用来配置上传的图片/文件的数量限制
  - 示例（图片/文件）：用来配置上传的图片/文件的示例
- 在【高级配置】区域的配置项

  - 层叠字段（单行文本框、多行文本框、数字选择器、日期选择器、日期范围选择器、时间选择器）：用来配置字段是否加入层叠设置
