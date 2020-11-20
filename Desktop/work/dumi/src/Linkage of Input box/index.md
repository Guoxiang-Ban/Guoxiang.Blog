## 一对多联动

##### 简介

- 当 AA Select 框的值选择为 visible 时显示 BB CC DD input 框

#### Dome

```tsx
import React, { useEffect } from 'react';
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
import 'antd/dist/antd.css';

const { onFieldValueChange$ } = FormEffectHooks;
const useOneToManyEffects = () => {
  const { setFieldState } = createFormActions();
  onFieldValueChange$('aa').subscribe(({ value }) => {
    setFieldState('*(bb,cc,dd)', (state) => {
      state.visible = value;
    });
  });
};
const One = () => {
  return (
    <SchemaForm
      components={{ Input, Select }}
      onSubmit={(values) => {
        console.log(values);
      }}
      effects={() => {
        useOneToManyEffects();
      }}
    >
      <Field
        type="string"
        enum={[
          { label: 'visible', value: true },
          { label: 'hidden', value: false },
        ]}
        default={false}
        name="aa"
        title="AA"
        x-component="Select"
      />
      <Field type="string" name="bb" title="BB" x-component="Input" />
      <Field type="string" name="cc" title="CC" x-component="Input" />
      <Field type="string" name="dd" title="DD" x-component="Input" />
    </SchemaForm>
  );
};

export default () => <One />;
```

## 多对一联动

##### 简介

- 多对一联动其实就是一对一联动，只不过作用的对象是同一个字段
- BB 控制 AA 显示隐藏，CC 控制 AA 的值

#### Dome

```tsx
import React, { useEffect } from 'react';
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
import 'antd/dist/antd.css';

const { onFieldValueChange$ } = FormEffectHooks;
const useManyToOneEffects = () => {
  const { setFieldState } = createFormActions();
  onFieldValueChange$('bb').subscribe(({ value }) => {
    setFieldState('aa', (state) => {
      state.visible = value;
    });
  });
  onFieldValueChange$('cc').subscribe(({ value }) => {
    setFieldState('aa', (state) => {
      state.value = value;
    });
  });
};
const Two = () => {
  return (
    <SchemaForm
      components={{ Input, Select }}
      onSubmit={(values) => {
        console.log(values);
      }}
      effects={() => {
        useManyToOneEffects();
      }}
    >
      <Field type="string" name="aa" title="AA" x-component="Input" />
      <Field
        type="string"
        enum={[
          { label: 'visible', value: true },
          { label: 'hidden', value: false },
        ]}
        default={false}
        name="bb"
        title="BB"
        x-component="Select"
      />
      <Field type="string" name="cc" title="CC" x-component="Input" />
    </SchemaForm>
  );
};

export default () => <Two />;
```

## 多级联动

##### 简介

- AA 控制 DD 的显示与隐藏
- DD 框的值为 11 时 显示 aa1、bb1
- BB 框的值以 CC 框的值变化而变化

#### Dome

```tsx
import React, { useEffect } from 'react';
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
import 'antd/dist/antd.css';

const { onFieldValueChange$ } = FormEffectHooks;
const useManyToOneEffects = () => {
  const { setFieldState } = createFormActions();
  onFieldValueChange$('aa').subscribe(({ value }) => {
    setFieldState('*(bb,dd)', (state) => {
      state.visible = value;
    });
  });
  onFieldValueChange$('cc').subscribe(({ value }) => {
    setFieldState('bb', (state) => {
      state.value = value;
    });
  });
  setFieldState('*(AA1,BB1)', (state) => {
    state.visible = false;
  });
  onFieldValueChange$('dd').subscribe(({ value }) => {
    setFieldState('*(AA1,BB1)', (state) => {
      state.visible = Number(value) === 11;
    });
  });
};

const Two = () => {
  return (
    <SchemaForm
      components={{ Input, Select }}
      onSubmit={(values) => {
        console.log(values);
      }}
      effects={() => {
        useManyToOneEffects();
      }}
    >
      <Field
        type="string"
        enum={[
          { label: 'visible', value: true },
          { label: 'hidden', value: false },
        ]}
        default={false}
        name="aa"
        title="AA"
        x-component="Select"
      />
      <Field type="string" name="bb" title="BB" x-component="Input" />
      <Field type="string" name="cc" title="CC" x-component="Input" />

      <Field type="string" name="dd" title="DD" x-component="Input" />

      <Field type="string" name="AA1" title="aa1" x-component="Input" />
      <Field type="string" name="BB1" title="bb1" x-component="Input" />
    </SchemaForm>
  );
};

export default () => <Two />;
```
