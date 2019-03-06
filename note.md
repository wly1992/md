# 随手记

# import

import * as xxx from 'xxx' 会将若干export导出的内容组合成一个对象返回

例如：

```
export const getStatus = () => {}

export const getStatus1 = () => {}

export const getStatus2 = () => {}
```

import xxx from 'xxx' 只会导出这个默认的对象作为一个对象

例如：

```
export default const getStatus = () => {}
```

import { getStatus } form 'xxx' 按需引入

例如：

```
export const getStatus = () => {}
export const getStatus1 = () => {}
```

# import