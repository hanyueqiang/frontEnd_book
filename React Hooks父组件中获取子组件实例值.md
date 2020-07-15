> 通过`useImperativeHandle`将子组件中值暴露给父组件

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

### 结语

`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 `ref` 这样的命令式代码。`useImperativeHandle`应当与 `forwardRef` 一起使用.
