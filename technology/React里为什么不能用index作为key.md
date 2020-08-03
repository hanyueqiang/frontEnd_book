之前在做项目时经常遇到后端返回的数据没有主键的情况，于是在遍历数组的时候`key`使用了`index`,在只涉及到数据展示的时候并不会出现什么问题，但是当我们在操作数据时候，很容易出现问题。

```
import React, { useState } from 'react';
const list = [
  {
    id: '1',
    name: 'name1',
    value: '',
  },
  {
    id: '2',
    name: 'name2',
    value: '',
  },
  {
    id: '3',
    name: 'name3',
    value: '',
  },
];
export default () => {
  const [data, setData] = useState(list);

  const deleteFirstHanlde = () => {
    const newdata = JSON.parse(JSON.stringify(data));
    newdata.shift();
    setData(newdata);
  };

  return (
    <div>
      {data.map((item, index) => {
        return (
          <div key={index}>
            {item.name}
            <input type="value" />
          </div>
        );
      })}
      <div>
        <button onClick={deleteFirstHanlde}>删除第一项</button>
      </div>
    </div>
  );
};
```

页面渲染好了之后，3 个 input 输入框依次输入随机内容，当我们用 index 作为 key 的时候，点击删除第一项按钮会发现，左侧文字正确改变，input 输入框最后一项没了，这不是我们希望的样子。
因为当我们使用 index 作为 key 时，此时 key 为 0、1、2，删掉第一项后 key 变为 0、1，此时 react 在执行 diff 算法过程中，任务 key=0 存在，只需要更新子节点的值，所以左侧的 name 成功改变，而 input 的值非受控，不会更新。同时计算出少了 key=2 这项，删除了最后一项。

当我们使 id 作为 key 时，此时 key 为 1、2，3，删掉第一项后 key 变为 2、3，根据 react 的 diff 算法，react 计算出删除一个子节点即可更新.
