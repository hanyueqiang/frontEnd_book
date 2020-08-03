## 开发中常用的 moment 时间格式化

#### 中文格式化

```js
import moment from "moment";
import "moment/locale/zh-cn";
moment.locale("zh-cn");
```

#### 转化为字符串格式

```js
const Date = moment().format("YYYY-MM-DD HH:mm");
```

#### 获取当天的前一个月时间

```js
const preMonthDay = moment().subtract(1, "months");
```

#### 获取当天的后一个月时间

```js
const preMonthDay = moment().add(1, "months");
```

#### 获取当天的`00:00:00`

例如 `2020-07-17 00:00:00`

```js
const initToday = moment().startOf("day");
```

#### 获取当天的`23:59:59`

例如 `2020-07-17 23:59:59`

```js
const initToday = moment().endOf("day");
```

#### 获取当天的前一天

```js
const initToday = moment().subtract(1, "days");
```

#### 获取当天的后一天

```js
const initToday = moment().add(1, "days");
```

#### 当前时间的前 15 天至后 15 天

```js
const timeValue = [moment().subtract(15, "days"), moment().add(15, "days")];
```

#### 获取当天的前一个月时间至当天的前一天,且均为当天的`00:00:00`

当天是 7 月 17 日；如`2020-06-17 00:00`-`2020-07-16 00:00`

```js
const timeValue = [
  moment()
    .subtract(1, "months")
    .startOf("day"),
  moment()
    .subtract(1, "days")
    .startOf("day"),
];
```

#### 获取当前月初和月尾

如`2020-07-01 00:00`-`2020-07-31 23:59`

```js
const monthStart = moment()
  .startOf("month")
  .format("YYYY-MM-DD HH:mm");
const monthEnd = moment()
  .endOf("month")
  .endOf("month")
  .format("YYYY-MM-DD HH:mm");
```

#### 获取当前天开始和结束

如`2020-07-17 00:00`-`2020-07-17 23:59`

```js
const dayStart = moment()
  .startOf("day")
  .format("YYYY-MM-DD HH:mm");
const dayEnd = moment()
  .endOf("day")
  .format("YYYY-MM-DD HH:mm");
```

#### 获取当月月初至当天的结束

如`2020-07-01 00:00`-`2020-07-17 23:59`

```js
const start = moment().startOf("month");
const end = moment().endOf("day");
```

#### 选择时间不能超过当天

```js
const disabledDate = (time) => {
  if (!time) {
    return false;
  }
  return time.valueOf() <= moment().valueOf();
}

...
<DatePicker disabledDate={disabledDate} />
```
