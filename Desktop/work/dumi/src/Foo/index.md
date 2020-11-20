---
nav:
  title: Components
  path: /components
---

## 动态递归渲染(混合开发)

Demo:

```tsx
import React, { useState, useEffect } from 'react';
import { Foo } from 'dumi';
import {
  SchemaForm,
  Field,
  FormButtonGroup,
  Submit,
  Reset,
  FormSpy,
  SchemaField,
  FormEffectHooks,
  createControllerBox,
  createFormActions,
  FormPath,
} from '@formily/antd';
import {
  Input,
  FormStep,
  FormLayout,
  FormCard,
} from '@formily/antd-components';
import { Button, Spin } from 'antd';
import 'antd/dist/antd.css';

const actions = createFormActions();
const { onFormInit$ } = FormEffectHooks;

const fetchSchema = () => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({
        type: 'object',
        properties: {
          'dynamic-1': {
            type: 'string',
            'x-component': 'input',
            title: '字段1',
          },
          'dynamic-2': {
            type: 'string',
            'x-component': 'input',
            title: '字段2',
          },
          'dynamic-3': {
            type: 'string',
            'x-component': 'input',
            title: '字段3',
          },
          'dynamic-4': {
            type: 'string',
            'x-component': 'input',
            title: '字段4',
          },
        },
      });
    }, 2000);
  });
};

const DynamicFields = createControllerBox('dynamics', ({ path }) => {
  const [schema, setSchema] = useState();

  useEffect(() => {
    fetchSchema().then((schema) => {
      setSchema(schema);
    });
  }, []);

  if (!schema) {
    return (
      <div
        style={{
          height: 200,
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        <Spin tip="Loading..." />
      </div>
    );
  }

  return (
    <SchemaField
      path={FormPath.parse(path).parent()}
      schema={schema}
      onlyRenderProperties
    />
  );
});

const useBatchRequired = (name) => {
  const { setFieldState } = createFormActions();

  onFormInit$().subscribe(() => {
    setFieldState(name, (state) => {
      if (state.props.required === false) return;
      state.required = true;
    });
  });
};

const App = () => (
  <SchemaForm
    components={{ Input }}
    actions={actions}
    effects={() => {
      useBatchRequired('*');
    }}
  >
    <FormStep
      style={{ marginBottom: 20 }}
      dataSource={[
        { title: '步骤1', name: 'step-1' },
        { title: '步骤2', name: 'step-2' },
        { title: '步骤3', name: 'step-3' },
      ]}
    />
    <FormCard name="step-1" title="静态字段集">
      <FormLayout labelCol={8} wrapperCol={10}>
        <Field
          name="static-1"
          type="string"
          x-component="Input"
          title="字段1"
        />
        <Field
          name="static-2"
          type="string"
          x-component="Input"
          title="字段2"
        />
        <Field
          name="static-3"
          type="string"
          x-component="Input"
          title="字段3"
        />
        <Field
          name="static-4"
          type="string"
          x-component="Input"
          title="字段4"
        />
      </FormLayout>
    </FormCard>
    <FormCard name="step-2" title="动态字段集">
      <FormLayout labelCol={8} wrapperCol={10}>
        <DynamicFields />
      </FormLayout>
    </FormCard>
    <FormCard name="step-3" title="静态字段集">
      <FormLayout labelCol={8} wrapperCol={10}>
        <Field
          name="static-5"
          type="string"
          x-component="Input"
          title="字段5"
        />
        <Field
          name="static-6"
          type="string"
          x-component="Input"
          title="字段6"
        />
        <Field
          name="static-7"
          type="string"
          x-component="Input"
          title="字段7"
        />
        <Field
          name="static-8"
          type="string"
          x-component="Input"
          title="字段8"
        />
      </FormLayout>
    </FormCard>
    <FormSpy
      selector={FormStep.ON_FORM_STEP_CURRENT_CHANGE}
      initialState={{
        step: { value: 0 },
      }}
      reducer={(state, action) => {
        switch (action.type) {
          case FormStep.ON_FORM_STEP_CURRENT_CHANGE:
            return { ...state, step: action.payload };
          default:
            return { step: { value: 0 } };
        }
      }}
    >
      {({ state }) => {
        return (
          <FormButtonGroup align="center">
            <Button
              disabled={state.step.value === 0}
              onClick={() => {
                actions.dispatch(FormStep.ON_FORM_STEP_PREVIOUS);
              }}
            >
              上一步
            </Button>
            <Button
              type={state.step.value == 2 ? 'primary' : undefined}
              onClick={() => {
                if (state.step.value == 2) {
                  actions.submit();
                } else {
                  actions.dispatch(FormStep.ON_FORM_STEP_NEXT);
                }
              }}
            >
              {state.step.value == 2 ? '提交' : '下一步'}
            </Button>
            <Reset>重置</Reset>​
          </FormButtonGroup>
        );
      }}
    </FormSpy>
  </SchemaForm>
);
export default () => <App />;
```

<!-- More skills for writing demo: https://d.umijs.org/guide/demo-principle -->
