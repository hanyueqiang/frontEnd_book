## 基于 React 实现一个调查问卷编辑器设计

### 背景

设计一款问卷编辑器，包含单选、多选、下拉、填空、矩阵等，配置完成后支持移动端`H5`展示、`PC`端展示

### 方案

#### 1.基于 gitHub 上开源调问插件

- [form-render](https://github.com/alibaba/form-render)

阿里开源，支持`Antd`
实现原理: 通过 `JSON Schema` 生成标准 `Form，`常用于自定义搭建配置界面生成.
优点：`UI`组件界面友好，任意组合各类型组件、生成表单
缺点：控件依赖`Antd`组件，加入矩阵评分题型、或备注题型支持不了，可扩展性差，不支持移动端展示

- [DWSurvey](https://github.com/wkeyuan/DWSurvey)
  `DWSurvey`是一款方便、高效、实用的调研问卷系统，一款基于 `JAVA WEB`的开源问卷表单系统。
  优点：基本满足调问系统基本使用，支持控件丰富
  缺点：前后端不分离、没有前端开发文档、可扩展性未知

#### 2.使用`JSON`维护一套问卷

实现原理：对一个`JSON`数据源分别编译成移动端和 `PC` 端页面
优点：扩展性强，个性化能力强、增加一个控件需添加一个组件，样式可控
缺点：目前配置好的问卷没有生成静态文件、移动端依赖`json`数据，针对`h5`、`PC`端分别编写编译组件

## 实现

![avatar](/assets/app_basic.png)

页面布局如下：
![avatar](/assets/basic.png)

更新`JSON`数据源主要代码：

```js
// config.js

export const getQuestionStem = (surveyId: string | undefined, editors: EditorItemProps[], type: string) => {
  const editorObj = {
    belongId: surveyId || '',
    copyFromId: '',
    hv: 0,
    quId: uuidv4(),
    isRequired: 1,
    keywords: '',
    orderById: getOrderById(editors),
    quName: getEditorTitle(type),
    quNote: '',
    quTitle: '',
    quType: type,
    questionLogics: [],
    tag: 0,
    visibility: 1,
    showState: 1,
    quTag: 1,
    isLogicShow: 1,
  };
  ...
  return editorObj;
}
```

组件设计如图：
![avatar](/assets/editor.png)

主要代码：

```js
// Content Component
<Row gutter={4} className={styles.surveyMain}>
  <Col span={17} style={{ padding: 16 }}>
    <ItemComponent />
  </Col>
  <Col span={7}>
    <CommonComponent />
  </Col>
</Row>


// 更新JSON数据源
const updateModelEditors = (data: EditorItemProps[]) => {
  dispatch({
    type: 'editor/save',
    payload: {
      editors: data,
    },
  });
  // 同步到server端
  fetchAsyncSurveyJson(data);
};

// Item Component
const SubComponent = {
  radio: (
    <RadioComponent
      itemModel={itemModel}
      addOthersHandle={addOthersHandle}
    />
  ),
  checkbox: (
    <CheckboxComponent
      itemModel={itemModel}
      addOthersHandle={addOthersHandle}
    />
  ),
  ...
};

if (SubComponent.hasOwnProperty(itemModel.quType)) {
  return SubComponent[itemModel.quType];
} else {
  return null;
}
```

## 移动端显示

![avatar](/assets/h5.png)

实现流程图如下：

![avatar](/assets/app_h5.png)

入口代码如下：

```js
<div className={styles.phone_head}>
  <div className={styles.phone_head_title}>{survey_name}</div>
  <div
    className={styles.phone_head_desc}
    dangerouslySetInnerHTML={{ __html: lineFeed(welcomeSpeech) }}
  />
</div>
<div className={styles.phone_content}>
  <QuestionnaireContent survey_stem={survey_stem} updateStemHandle={updateStemHandle} />
</div>
<div className={styles.phone_footer}>
  <div className={styles.phone_submit}>
    {survey_stem.length > 0 && (
      <Button type="primary" style={{ width: '100%' }} size="large" onClick={questionSubmitHandle}>
        提交
      </Button>
    )}
  </div>
  <div className={styles.phone_footer_content}>XXX提供支持</div>
</div>
```

## PC 端显示

![avatar](/assets/pc1.png)

实现流程图如下：

![avatar](/assets/app_pc.png)

## 存在问题

- 问卷与系统集成在一起，后续问卷迭代升级，需将整个系统打包，存在鉴权问题，需开启白名单。
- 移动端控件需支持移动端 UI 组件，包括下拉、时间等，额外引入移动端 UI 插件会使系统打包文件内存变大(可使用按需加载)
- 依赖`json`数据源，在患者端如果 json 实时变动，页面内容也响应改变，要引入版本概念。

## 改进

- 支持拖拽排序
- 可将问卷系统抽离，单独打包部署，后期升级问卷不影响主系统使用、不涉及服务端鉴权、主系统调用问卷需传入问卷 id 和登录人信息，新页面打开方式。
- 问卷抽离后，若保持单页面体验，可使用微前端方式处理，需主应用进行微前端改造，子应用也同样支持微前端
- 在服务端把`json`使用模板打包成静态文件
