### Intro

The Book of Story

### What is Storybook?

> Storybook is a frontend workshop for building UI components and pages in isolation. It helps you develop and share hard-to-reach states and edge cases without needing to run your whole app. Thousands of teams use it for UI development, testing, and documentation. It's open source and free.

-- https://storybook.js.org/docs#what-is-storybook

### Support

Storybook supports:

- React
- Vue
- Angular
- React Native
- Svelte
- Web Components

### Main concepts

- Stories: Captures rendered state of a UI component. Components can be a collection of stories where each story is a different state of that component.
- Docs: Storybook is able to analyze your components and automatically generate documentation for them.
- Testing: When we make components we make stories, but we also need to test them. You can write tests for your stories and they will be run in Storybook with pretty low-effort.
- Sharing: We're able to publish our stories to expose your work with others either internally or publicly. Additionally, you can showcase your stories in apps like Notion and even Figma!

### Why do we need Storybook?

- Overtime web has become more complex
- Component-driven tools have become a great way to break down really complex interfaces into smaller, more manageable pieces. But these are not 100% the exact solution.
- These UI components can be difficult to test due to being tangled up with the rest of the application logic.
- By leveraging Storybook we can build in isolation which is a benefit for both developers, designers, and testers. Developer especially benefits from this as it allows them to focus on the component and not the rest of the application and actually is a good way to right components that are easy to test and not tied to any external dependencies.

### Writing stories

- Conventionally stories are written in a `*.stories.js` or `*.story.ts` file.
- Stories are written in the Component Story Format which is based on ESM with the default export containing information about the component and additional named exports will be used to capture the distinct states of that component.

```
import type { Meta } from '@storybook/react';

import { Button } from './Button';

const meta: Meta<typeof Button> = {
  component: Button,
};

export default meta;
```

Example of a state:

```
import type { Meta, StoryObj } from '@storybook/react';

import { Button } from './Button';

const meta: Meta<typeof Button> = {
  component: Button,
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    primary: true,
    label: 'Button',
  },
};
```

Example of building stories upon each other:

```
import type { Meta, StoryObj } from '@storybook/react';

import { Button } from './Button';

const meta: Meta<typeof Button> = {
  component: Button,
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    backgroundColor: '#ff0',
    label: 'Button',
  },
};

export const Secondary: Story = {
  args: {
    ...Primary.args,
    label: '😄👍😍💯',
  },
};

export const Tertiary: Story = {
  args: {
    ...Primary.args,
    label: '📚📕📈🤓',
  },
};
```

Example of remixing stories:

```
import type { Meta, StoryObj } from '@storybook/react';

import { ButtonGroup } from '../ButtonGroup';

//👇 Imports the Button stories
import * as ButtonStories from './Button.stories';

const meta: Meta<typeof ButtonGroup> = {
  component: ButtonGroup,
};

export default meta;
type Story = StoryObj<typeof ButtonGroup>;

export const Pair: Story = {
  args: {
    buttons: [{ ...ButtonStories.Primary.args }, { ...ButtonStories.Secondary.args }],
    orientation: 'horizontal',
  },
};
```

- When the button signature changes we just need to update the button story and the button group will automatically pick up the changes.

Example of parameters:

- Parameters are Storybooks metadata that can be used to configure the storybook environment. For example, we can leverage parameters to change the background color of the story.

```
import type { Meta, StoryObj } from '@storybook/react';

import { Button } from './Button';

const meta: Meta<typeof Button> = {
  component: Button,
  // 👇 Meta-level parameters
  parameters: {
    backgrounds: {
      default: 'dark',
    },
  },
};
export default meta;

type Story = StoryObj<typeof Button>;

export const Basic: Story = {};
```

Example of using decorators:

- Decorators allow us to wrap our stories in markup when rendering a story. This is useful for things like setting a theme or a layout wrapper.
- Decorators can be pretty complex and are available via addons in Storybook.
- Additionally, you can configure decorators at the story level, component level, or even the global level.

```
import type { Meta, StoryObj } from '@storybook/react';

import { Button } from './Button';

const meta: Meta<typeof Button> = {
  component: Button,
  decorators: [
    (Story) => (
      <div style={{ margin: '3em' }}>
        {/* 👇 Decorators in Storybook also accept a function. Replace <Story/> with Story() to enable it  */}
        <Story />
      </div>
    ),
  ],
};

export default meta;
```

### Walkthrough of our sample Storybook

### We know about stories, what about testing?

- We can write tests for our stories and they will be run in Storybook with pretty low-effort.
- Often times in UI development we leverage a "spot checking" approach where we'll run our stories and eyeball the results or leverage Figma to build the components.
- While this works, it's not ideal as component libraries grow.
- With spot checking, are we ensuring our components are accessible? Do we ensure our components are styled correctly?
- Storybook has a suite of testing tools that we can leverage
- Test runner
- Visual regression testing
- Accessibility testing
- Interaction testing
- Converage testing
- Snapshot testing
- End-to-end testing (simulate real user scenarios)
- Unit testing

### Test runner

Install the test runner (Jest!)
`npm install @storybook/test-runner --save-dev`

Setup a package script

```
{
  "scripts": {
    "test-storybook": "test-storybook"
  }
}
```

With our Storybook instance running, we can run out tests with the following command:

```
npm run test-storybook
```

(show passing screenshot)

- Stories that do not contain a play function will just be tested that they are rendering without any errors.
- Stories that contain a play function will do the same check but also run through the assertion checks on that play function.

### What's a play function?

- Play functions are executed after a story is rendered. They allow you to automate the interaction of your stories.

- Before writing a play function, let's install the interaction addons. We'll talk about addons later.

```
npm install @storybook/test @storybook/addon-interactions --save-dev
```

- High level the interaction addon gives us helpers to control the execution flow of our play function. For example, we can pause, resume, rewind, and step through interactions in our stories!

- We need to add this to the storybook config:

```
import type { StorybookConfig } from "@storybook/react-vite";

const config: StorybookConfig = {
  stories: ["../src/**/*.mdx", "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"],
  addons: [
    "@storybook/addon-onboarding",
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@chromatic-com/storybook",
    "@storybook/addon-interactions",
  ],
  framework: {
    name: "@storybook/react-vite",
    options: {},
  },
};
export default config;

```

- Let's write a play function for our Header components logged out state to show what happens when we click the login button.

```
export const LoggedOut: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    const loginButton = await canvas.getByRole("button", { name: /Log in/i });
    await userEvent.click(loginButton);
  },
};
```

- Very simple example here, but we are getting the canvas element, finding the login button by role and clicking it. This is very similar to React Testing Library!

- We can write assertions on the results

```
export const LoggedOut: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    const loginButton = await canvas.getByRole("button", { name: /Log in/i });
    await userEvent.click(loginButton);

    expect(await canvas.getByText(/Welcome/i)).toBeInTheDocument();
  },
};
```

- Let's do something a bit more cooler! Filling in a form and submitting it.
- For this, I added a loading state to our Button component and also added a play function to our story.

```
export const Loading: Story = {
  args: {
    label: "Button",
    loading: false,
  },
  decorators: [
    (Story) => {
      const [_args, updateArgs] = useArgs();

      const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
        e.preventDefault();
        updateArgs({ loading: true });

        // Simulate a request to the server
        setTimeout(() => {
          updateArgs({ loading: false });
        }, 1000);
      };

      return (
        <form onSubmit={handleSubmit}>
          <Story />
        </form>
      );
    },
  ],
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    const button = await canvas.getByRole("button", { name: /Button/i });
    await userEvent.click(button);
  },
};
```

- Notice the use of decorators on the story level to wrap it within a form. Also notice the useArgs API, this allows us to change the args of the story and update the state of the component.

- Now I'll create a new sign up form component and add a story for it. For brevity, I'll be skipping the actual component code and just focusing on the story.

```
import type { Meta, StoryObj } from "@storybook/react";

import { Signup } from "./Signup";

const meta = {
  title: "Example/Signup",
  component: Signup,
  parameters: {
    layout: "fullscreen",
  },
} satisfies Meta<typeof Signup>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {};
```

- Now we can write our play function and do our assertions

```
export const Default: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    const email = canvas.getByLabelText(/Email/i);
    expect(email).toBeInTheDocument();

    await userEvent.type(email, "test@example.com", {
      delay: 100,
    });

    const password = canvas.getByLabelText(/Password/i);
    expect(password).toBeInTheDocument();

    await userEvent.type(password, "test123", {
      delay: 100,
    });

    const submit = canvas.getByRole("button", {
      name: /Create account/i,
    });

    await userEvent.click(submit);

    const loader = await canvas.getByRole("img", { name: /loader/i });
    expect(loader).toBeInTheDocument();

    await waitFor(() => expect(loader).not.toBeInTheDocument());
  },
};
```

- Now let's open up the story in Storybook and see what happens!

(play demo video on passing)

- This is just the surface of what the play functions can do. We are able to showcase interactions and also assert behaviour of our components right in Storybook!

### Visual regression testing

- Visual tests help us catch bugs in appearances of our components. It works by taking a screenshot of every story and will store that history and do a comparison. This is a great way to catch bugs that are related to layout, colour, and any visual aspects of our components.

- Luckily Storybook supports this through their other service called Chromatic.

- Chromatic has a free plan for visual testing for 5,000 free snapshots per month on unlimited projects w/ Git and CI integrations. Pretty generous!
- I will be using this for my demo.

- First let's isntall Chromatic

```
npm install --save-dev chromatic
```

- And now we can manually publish our Storybook to Chromatic

```
npx chromatic --project-token=chpt_859f9e7808c5ab2
```

- This will authenticate with Chromatic, retrieve git information, collect metadata on Storybook, build our Storybook, and publish that to Chromatic.

- Now that we've published our Storybook to Chromatic. Let's wire it up to our Storybook instance locally! We do this by adding the Chromatic addon to our Storybook config.

```
npx storybook@latest add @chromatic-com/storybook
```

- Now when viewing a story we can click "Visual Tests" menu and SSO into Chromatic.

(chromatic waiting change)

- Chromatic has built our initial Storybook from the manual command we ran earlier. It is now awaiting for a change to be to the Storybook. Let's change the colour of our button. We can do that directly in Storybook.

(show chromatic ui change video)

- Chromatic detected the change and did a comparison and we had to approve this. This doesn't have to be a developer, it could be a designer, product manager, etc!

- Now when we run the visual tests again we can see no changes are detected.

(show chromatic no changes)

- From this point, we would establish a CI/CD integration with Chromatic which will handle all the Storybook uploading piece and then tie the actions required to the specific pull request to approve the regressions or deny if they are not intended.

### Accessibility testing

- Storybook can help build inclusive experiences for all users by ensuring we're passing common accessibility checks.
- We can leverage the Accessibility addon to do this.

- Let's install the addon

```
npm install @storybook/addon-a11y --save-dev
```

- And add it to our Storybook config

```
import type { StorybookConfig } from "@storybook/react-vite";

const config: StorybookConfig = {
  stories: ["../src/**/*.mdx", "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"],
  addons: [
    "@storybook/addon-onboarding",
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@chromatic-com/storybook",
    "@storybook/addon-interactions",
    "@storybook/addon-a11y",
  ],
  framework: {
    name: "@storybook/react-vite",
    options: {},
  },
};
export default config;
```

- And that's it!

(show accessibility picture)

### Recap

- Storybook lets us build UI components and pages in isolation. It helps us develop and share hard-to-reach states and edge cases without needing to run our whole app.
- Storybook offers a suite of testing tools that we can leverage to test our stories.
- Storybook has a service called Chromatic that helps teams conduct visual regression testing.
- Storybook has an accessibility addon that helps us ensure our components are accessible.
- In addition, Storybook has many many other addons that can be leveraged in your Storybooks

### Showcase

- Lots of companies are showcasing their Storybook on the Storybook website. Here are a few notable ones:
- Shopify
- NASA
- Microsoft
- GitHub
- Supabase
- Wordpress

https://storybook.js.org/showcase/projects

- I encourage you to check them out and see any patterns that we could apply to our own Storybook setup!

### Additional features I didn't discuss

- Loaders (fetch API data, personally, I don't prefer to leverage this feature in stories.)
- Mocking (Following component driven development, this isn't really a feature I leverage in stories.)
- Mocking providers (This one is basically just using a context provider and hardcoding the properties being given to it.)
- Documentation (Leveraging MDX you are able to write documentation for your Storybook and stories.)

E.g.,

```
import React from 'react';

import { Preview } from '@storybook/react';

import { ThemeProvider } from 'styled-components';

const preview: Preview = {
  decorators: [
    (Story) => (
      <ThemeProvider theme="default">
        {/* 👇 Decorators in Storybook also accept a function. Replace <Story/> with Story() to enable it  */}
        <Story />
      </ThemeProvider>
    ),
  ],
};

export default preview;
```

### Questions?
