# Walkthrough of Storybook

##

![](./assets/assets/storybook-dashboard.png)

## Writing stories

- Conventionally stories are written in a `*.stories.js` or `*.stories.ts` file.
- Stories are written in the Component Story Format which is based on ESM
- Additional exports will be used to capture the distinct states of that component.

## Default export

```typescript
import type { Meta } from "@storybook/react";

import { Button } from "./Button";

const meta: Meta<typeof Button> = {
  component: Button,
};

export default meta;
```

## Named export for component state

```typescript
import type { Meta, StoryObj } from "@storybook/react";

import { Button } from "./Button";

const meta: Meta<typeof Button> = {
  component: Button,
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    primary: true,
    label: "Button",
  },
};
```

## We can build stories upon each other

```typescript
export const Primary: Story = {
  args: {
    backgroundColor: "#ff0",
    label: "Button",
  },
};

export const Secondary: Story = {
  args: {
    ...Primary.args,
    label: "😄👍😍💯",
  },
};
```

## Remixing stories

```typescript
import type { Meta, StoryObj } from "@storybook/react";

import { ButtonGroup } from "../ButtonGroup";

//👇 Imports the Button stories
import * as ButtonStories from "./Button.stories";

const meta: Meta<typeof ButtonGroup> = {
  component: ButtonGroup,
};

export default meta;
type Story = StoryObj<typeof ButtonGroup>;

export const Pair: Story = {
  args: {
    buttons: [
      { ...ButtonStories.Primary.args },
      { ...ButtonStories.Secondary.args },
    ],
    orientation: "horizontal",
  },
};
```

## Remixing stories

- When the button signature changes we just need to update the button story and the button group will automatically pick up the changes.

## Parameters

- Parameters are Storybooks metadata that can be used to configure the storybook environment.

##

```typescript
import type { Meta, StoryObj } from "@storybook/react";

import { Button } from "./Button";

const meta: Meta<typeof Button> = {
  component: Button,
  // 👇 Meta-level parameters
  parameters: {
    backgrounds: {
      default: "dark",
    },
  },
};
export default meta;

type Story = StoryObj<typeof Button>;

export const Basic: Story = {};
```

## Decorators

- Decorators allow us to wrap our stories in markup when rendering a story. This is useful for things like setting a theme or a layout wrapper.
- You can configure decorators at the story level, component level, or even the global level.

##

```typescript
import type { Meta, StoryObj } from "@storybook/react";

import { Button } from "./Button";

const meta: Meta<typeof Button> = {
  component: Button,
  decorators: [
    (Story) => (
      <div style={{ margin: "3em" }}>
        {/* 👇 Decorators in Storybook also accept a function. Replace <Story/> with Story() to enable it  */}
        <Story />
      </div>
    ),
  ],
};

export default meta;
```
