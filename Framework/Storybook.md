# StoryBook

## **Introduction**

Storybook is a library which helps create and test UI components in isolation. It's used to build UI's using Component Driven Development approach.

## **Setup**

Storybook needs an exisiting project to work on. So to create a very basic React project, use

For react

`npx create-react-app storybook-test`

For Vue (using vue-cli),

`vue create storybook-test`

Then run

`npx sb int`

which sets up a storybook project and starts a server at 6006 port.

## **Concepts**

Using storybook UI developers can create components in various states and test their behaviour. Each of these states are referred to as "story". The recommended way of writing "stories" is by using the CSF (Component Syntax Format)

Files ending with `.stories.ts || .stories.js` are story files.

### **CSF**

CSF is the new way for defining stories. Older way was to define stroies using `storiesOf()` function. CSF format works across all UI frameworks except ReactNative.

Parts of a CSF

**Default export**

Default export consists of component metadata. It is a required of a CSF.

```js
export default {
title: "Hierarchy/For/Component", //Placement of the component in the treeview would be Hierarchy -> For -> Component
component: YourCustomComponent, //Optional but encouraged
decorator: [...],
parameters: {...}
};
```

**Named Exports**

These are all the stories defined for the component. The name given to the export is used as the storyname after passing it through `startCase` lodash function.

A named export is a function referred to as story function.

> `startCase` function converts something like `helloWorld` to Hello World.

<br>
Ex.

```js
export const OutlinedComponent = () => <YourCustomComponent />;

OutlinedComponent.storyName = "Dashed Outlined Component"; // This name will appear in the treeview
```

To make the story more dynamic, story arguments can be passed.

Ex.

```js
export const OutlinedComponent = ({outlineColor, onClick}) => <YourCustomComponent color={outlineColor} action={onClick}>;
```

Non-story exports can be handled by using either includeStories or excludeStories metadata option ie. as a key in the default export.

#### **Using Args to write stories**

Writing different stories for a component can mean a lot of code duplication.

Ex.

```js
// Arguments passed with minor changes, lot of code rewritten.
const PrimaryBtn = () => <Button color={primaryColor} label="Submit" />;
const DangerBtn = () => <Button color={primaryColor} label="Error" />;
const SuccessBtn = () => <Button color={primaryColor} label="Success" />;
```

To make stories more maintainable and easy to update whenever the underlying component changes, we can use a template and pass arguments using a spread operator.

The `bind` method basically makes a copy of the function. Using `...` to spread the object props makes a copy of the original object.

Ex.

```js
const TemplateBtn = (arguments) => <Button {...args} />;

export const PrimaryBtn = TemplateBtn.bind({});
PrimayBtn.args = {
    color:"blue",
    label:"Submit
}

export const DangerBtn = TemplateBtn.bind({});
DangerBtn.args = {
    ...PrimaryBtn.args,
    label:"Error"
}
```

Above approach means less code duplication and more felxibility.

Also one thing to note is, args are exported along with the component, so they can be used in other stories. So when writing stories for a composite component, the args for the individual components can be imported and passed down.

```js
import PrimaryBtn from "./Btn.stories.js";

const BtnGrp = (args) => <ButtonGroup {...args} />;

export HorizontalBtnGrp = BtnGrp.bind({});

HorizontalBtnGrp.args = {
    orientation:'horizontal',
    btns:[...PrimaryBtn.args]
}
```

### **Arguments**

Arguments are a way to manipulate the component. Args are reactive meaning, if they are changed, the component re-renders.

There are 2 levels at which arguments can be passed,

- Story Level

  ```js
  const OutlineCompoentTemplate = (args) => <YourCustomComponent {...args} />;

  const DashedOutlineComponent = OutlineCompoentTemplate.bind({});

  DashedOutlineComponent.args = {
    style: "dashed",
  };
  ```

  These args are only specific to a story.

- Component Level

  ```js
  export default {
    title: "OutlinedComponent",
    component: OutlinedComponent,
    args: {
      style: "dashed",
    },
  };
  ```

  `style:"dashed"` is now passed as an arg to every story as it is defined at component level.

### **Parameters**

Parameters are a way to configure Storybook addons.

There are 3 levels of parameters.

> **Note**: Below code configures the background storybook addon.

- Story Level Parameters

  Ex.

  ```js
  PrimaryBtn.parameters = {
    backgrounds: {
      values: [
        { name: "red", value: "#f00" },
        { name: "green", value: "#0f0" },
      ],
    },
  };
  ```

- Component Level Parameters

  Ex.

  ```js
  export default {
    title: "Button",
    component: Button,
    parameters: {
      backgrounds: {
        values: [
          { name: "red", value: "#f00" },
          { name: "green", value: "#0f0" },
        ],
      },
    },
  };
  ```

- Global Level Parameters

  These changes are done in `preview.js` which is storybook's file to define global configs.

  Ex.

  ```js
  export const parameters = {
    backgrounds: {
      values: [
        { name: "red", value: "#f00" },
        { name: "green", value: "#0f0" },
      ],
    },
  };
  ```

The precedence of these configs is as follows:

        Story Level > Component Level > Global Level

Also parameters are merged, so no params are dropped, same params are overwritten.

### **Decorators**

In storybook, components are developed in isolation. But in real life use case, we might need to wrap the component inside of another component like a Theme component or a Layout component.

In some cases, you might want to see the component aligned in the middle of the screen.

These things can be achieved by using decorators. So decorators are extra markup surrounding the isolated component.

A simple example of decorator is:

```js
import { ThemeProvider } from "styled-components";

export const decorators = [
  (Story) => (
    <ThemeProvider theme="default">
      <Story />
    </ThemeProvider>
  ),
];
```

> One thing to note is the `<ThemeProvider>`'s theme is hardcoded to default. But you may want to change it to a different theme depending upon a certain story. To do this, the second argument to the deocrator function has global and story level args that can be used.

<br>
There are 3 levels

- Story Level

  ```js
  Primary.decorators = [
    (Story) => (
      <div style={{ margin: "3em" }}>
        <Story />
      </div>
    ),
  ];
  ```

- Component Level

  ```js
  export default {
    title: "Button",
    component: Button,
    decorators: [
      (Story) => (
        <div style={{ margin: "3em" }}>
          <Story />
        </div>
      ),
    ],
  };
  ```

- Global Level

  ```js
  export const decorators = [
    (Story) => (
      <div style={{ margin: "3em" }}>
        <Story />
      </div>
    ),
  ];
  ```

## Addons

<br>

### Controls

Controls addon is a default addon which comes out of the box. It is way to manipulate args. Depending upon the arg type control type is inferred. This can be changed using `argTypes`

Ex.

```js
export default {
  title: "Button",
  component: Button,
  argTypes: {
    backgroundColor: { control: "color" },
  },
};
```

This will generate a colorpicker UI for backgroundColor arg.
