> 通过`useImperativeHandle`将子组件中值暴露给父组件

#### 一、获取子组件 formFields

父组件

```js
import React, { useRef } from "react";
import { Modal } from "antd";
import SubForm from "./subForm";

const Parent = () => {
  const getFormValue = useRef();

  const handleOk = () => {
    const fields = getFormValue.current.formFields;
    console.log(fields);
  };

  return (
    <Modal onOk={handleOk}>
      <SubForm ref={getFormValue} />
    </Modal>
  );
};

export default Parent;
```

子组件

```js
import React, { useImperativeHandle, useRef, forwardRef } from "react";
import { Form } from "antd";

const SubForm = (props, ref) => {
  const [form] = Form.useForm();
  const formRef = useRef();

  useImperativeHandle(ref, () => ({
    formFields: form.getFieldsValue(),
  }));

  return (
    <Form form={form} ref={formRef}>
      ...
    </Form>
  );
};

const WrappedForm = forwardRef(SubForm);
export default WrappedForm;
```

#### 二、获取子组件实例值（`useState` 内容）

父组件

```js
import React, { useRef } from "react";
import { Button } from "antd";
import { ExportOutlined } from "@ant-design/icons";
import ChildComponent from "./component/ChildComponent";

const ParentCom = () => {
  const getTypeValues = useRef();

  // 导出
  const exportHandle = () => {
    // 获取子组件实例值
    const { typeFields }: any = getTypeValues.current;
    const type = typeFields.statType;

    const ifr = document.createElement("iframe");
    ifr.style.display = "none";
    ifr.src = `/aaa/bbb/export?type=${type}`;
    document.body.appendChild(ifr);
  };

  return (
    <>
      <ChildComponent ref={getTypeValues} />
      <Button type="primary" icon={<ExportOutlined />} onClick={exportHandle}>
        导出
      </Button>
    </>
  );
};
export default ParentCom;
```

子组件

```js
import React, { useState, useImperativeHandle, forwardRef } from "react";

type StatTypeProps = 1 | 2 | 3;

const ChildComponent = (ref: any) => {
  const [statType, setStatType] = useState < StatTypeProps > 1;

  useImperativeHandle(ref, () => ({
    typeFields: {
      statType,
    },
  }));

  return (
    <Select value={statType} onChange={(val) => setStatType(val)}>
      {statTypeOpts.map((item) => (
        <Option value={item.value} key={item.value}>
          {item.label}
        </Option>
      ))}
    </Select>
  );
};
export default forwardRef(ChildComponent);
```

### 结语

`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 `ref` 这样的命令式代码。`useImperativeHandle`应当与 `forwardRef` 一起使用.
