# Showcasing Storybook

## We know about stories, what about testing?

- We can write tests for our stories and they will be run in Storybook with pretty low-effort.
- Often times in UI development we leverage a "spot checking" approach where we'll run our stories and eyeball the results or leverage Figma to build the components.

## Tools available

- Test runner
- Visual regression testing
- Accessibility testing
- Interaction testing

## Additionally (not focusing for this talk)

- Coverage testing
- Snapshot testing
- End-to-end testing (simulate real user scenarios)
- Unit testing

## Test runner

Let's install the test runner that is running on Jest.

##

```shell
npm install @storybook/test-runner --save-dev
```

## Setup a package script in our package.json

```json
{
  "scripts": {
    "test-storybook": "test-storybook"
  }
}
```

##

With our Storybook instance running, we can run out tests with the following command:

```shell
npm run test-storybook
```

##

![](./assets/assets/test-storybook-passing.png)

## What tests are passing?

- Stories that do not contain interaction tests will just be tested that they are rendering without any errors.
- Stories that contain interaction tests will do the same check but also run through the assertion checks on that component.

## What's a play function?

- Play functions are executed after a story is rendered. They allow you to automate the interaction of your stories.

## Let's write a play function

- First, let's install the interaction addons.

##

```shell
npm install @storybook/test @storybook/addon-interactions --save-dev
```

##

High level the interaction addon gives us helpers to control the execution flow of our play function. For example, we can pause, resume, rewind, and step through interactions in our stories!

## Update the Storybook config in .storybook/main.ts

```typescript
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

## Now we can write a interaction test

- We'll focus on the header component for now

##

```typescript
export const LoggedOut: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    const loginButton = await canvas.getByRole("button", { name: /Log in/i });
    await userEvent.click(loginButton);
  },
};
```

- Very simple example here, but we are getting the canvas element, finding the login button by role and clicking it. This is very similar to React Testing Library!

##

We can write assertions on the results

```typescript
export const LoggedOut: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    const loginButton = await canvas.getByRole("button", { name: /Log in/i });
    await userEvent.click(loginButton);

    expect(await canvas.getByText(/Welcome/i)).toBeInTheDocument();
  },
};
```

## Let's do something a bit more cooler!

- Filling in a form and submitting it.
- For this I added a loading state to our Button component and also added a play function to our story for the interaction tests

##

```typescript
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

##

Notice the use of decorators on the story level to wrap it within a form. Also notice the `useArgs` API, this allows us to change the args of the story and update the state of the component.

##

With this done, let's create a new sign up form component and add a story for it. I'll be skipping the actual component code and just focusing on the story.

##

```typescript
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

##

Now we can write our play function and do our assertions

```typescript
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

##

![](./assets/assets/signup-passing-demo.mp4)

##

This is just the surface of what the play functions can do. We are able to showcase interactions and also assert behaviour of our components right in Storybook!

## Visual regression testing

- Helps us catch bugs in appearances of our components.
- Works by capturing a screenshot of every story and storing that history and do a comparison.
- Great support with Storybook through Chromatic

## Chromatic

- Storybook has a service called Chromatic that helps teams conduct visual regression testing.
- Chromatic has a free plan for visual testing for 5,000 free snapshots per month on unlimited projects w/ Git and CI integrations.
- Pretty generous!

##

Signing up to Chromatic is easy throught GitHub. Just authorize and then select the repository you want to use for your Storybook.

##

Let's install Chromatic

```shell
npm install --save-dev chromatic
```

##

And now we can manually publish our Storybook to Chromatic

```shell
npx chromatic --project-token=chpt_TOKEN_HERE
```

##

This will authenticate with Chromatic, retrieve git information, collect metadata on Storybook, build our Storybook, and publish that to Chromatic.

##

We can see our demo components in Chromatic now

![](./assets/assets/chromatic-publish.png)

##

Now that we've published our Storybook to Chromatic. Let's wire it up to our Storybook instance locally!

## Add the Chromatic addon to our Storybook config

```typescript
npx storybook@latest add @chromatic-com/storybook
```

##

Now when viewing a story we can click "Visual Tests" menu and SSO into Chromatic.

##

![](./assets/assets/chromatic-waiting-change.png)

##

Chromatic has built our initial Storybook from the manual command we ran earlier. It is now awaiting for a change to be to the Storybook.

##

![](./assets/assets/chromatic-diff.mp4)

##

Chromatic detected the change and did a comparison and we had to approve this. This doesn't have to be a developer, it could be a designer, product manager, etc!

##

Now when we run the visual tests again we can see no changes are detected.

![](./assets/assets/no-visual-changes.png)

##

From this point, we would establish a CI/CD integration with Chromatic which will handle all the Storybook uploading piece and then tie the actions required to the specific pull request to approve the regressions or deny if they are not intended.

## Accessibility testing

- Storybook can help build inclusive experiences for all users by ensuring we're passing common accessibility checks.
- We can leverage the Accessibility addon to do this.

## Install

```shell
npm install @storybook/addon-a11y --save-dev
```

##

And add it to our Storybook config

```typescript
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

##

And that's it!

##

![](./assets/assets/storybook-accessibility-results.png)

## Recap

![](./assets/assets/sleepy.gif)
