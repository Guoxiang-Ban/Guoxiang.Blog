---
nav:
  title: 动态联动
  path: /components
---

## 动态递归渲染(混合开发)

##### 简介

- 借助 x-linkages 可以实现简单的联动效果

* target 是一个 FormPathPattern 匹配表达式，在这里我们可以使用 FormPath 的各种匹配语法

##### Demo:

```tsx
import React, { useEffect, useState } from 'react';
import ReactDOM from 'react-dom';
import {
  SchemaForm,
  SchemaMarkupField as Field,
  FormButtonGroup,
  createFormActions,
  FormEffectHooks,
  Submit,
  Reset,
} from '@formily/antd'; // 或者 @formily/next
import { Input, Select } from '@formily/antd-components';
// import Printer from '@formily/printer';
import 'antd/dist/antd.css';

const { onFieldValueChange$ } = FormEffectHooks;

const mockSchema = {
  type: 'object',
  properties: {
    aa: {
      type: 'boolean',
      enum: [
        { label: 'visible', value: true },
        { label: 'hidden', value: false },
      ],
      default: false,
      title: 'AA',
      'x-component': 'Select',
      'x-linkages': [
        {
          type: 'value:visible',
          target: '*(bb,cc,dd)',
          condition: '{{!!$value}}',
        },
      ],
    },
    bb: {
      type: 'string',
      title: 'BB',
      'x-component': 'Input',
    },
    cc: {
      type: 'string',
      title: 'CC',
      'x-component': 'Input',
    },
    dd: {
      type: 'string',
      title: 'DD',
      'x-component': 'Input',
    },
  },
};
const Dynamic = () => {
  const [schema, setSchema] = useState({
    type: 'object',
  });
  useEffect(() => {
    setTimeout(() => {
      setSchema(mockSchema);
    }, 1000);
  }, []);

  return (
    <SchemaForm
      schema={schema}
      components={{ Input, Select }}
      onSubmit={(values) => {
        console.log(values);
      }}
    >
      <FormButtonGroup offset={6}>
        <Submit />
        <Reset />
      </FormButtonGroup>
    </SchemaForm>
  );
};

export default () => <Dynamic />;
```
