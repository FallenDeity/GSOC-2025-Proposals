"HTML5 Article Renderer for learning-equality" for Google Summer of Code 2025

| Categories | Details                                         |
| ---------- | ----------------------------------------------- |
| Difficulty | Medium                                          |
| Size       | 175hrs                                          |
| Key Skills | JavaScript, Vue.js, HTML5, CSS, Web Development |

- [1. Abstract](#1-abstract)
  - [1.1 Overview](#11-overview)
  - [1.2 Goals](#12-goals)
  - [1.3 Benefits](#13-benefits)
- [2. Approach](#2-approach)
  - [2.1 Research and Planning](#21-research-and-planning)
  - [2.2 Design and Prototyping](#22-design-and-prototyping)
  - [2.3 Integration and Testing](#23-integration-and-testing)
- [3. Timeline](#3-timeline)
  - [3.1 Community Bonding Period (May 8 - June 1)](#31-community-bonding-period-may-8---june-1)
  - [3.2 Renderer Development (June 2 - July 14)](#32-renderer-development-june-2---july-14)
  - [3.3 Testing and Stretch Goals (July 15 - August 15)](#33-testing-and-stretch-goals-july-15---august-15)
  - [3.4 Final Review and Documentation (August 16 - August 31)](#34-final-review-and-documentation-august-16---august-31)
- [4. About Me](#4-about-me)
  - [4.1 Contact Information](#41-contact-information)


# 1. Abstract

## 1.1 Overview

`Kolibri` is an open-source platform for offline learning, and it provides a way to create and share educational content. Like any other content platform, it needs a way to render HTML5 documents in a user-friendly manner. The current method of rendering HTML5 documents in `Kolibri` is through sandboxed iframes, which can be limiting for content creators who may not have extensive knowledge of HTML and CSS. The goal of this project is to create a new HTML5 article renderer that allows for a more flexible and responsive rendering of HTML5 documents, while also ensuring accessibility and consistent styling.

This project involves building on existing work that handles the sanitization of HTML5 documents to remove disallowed HTML tags and creating a new renderer that can take either an HTML5 document with inlined assets or an HTML5 article zip file. The renderer will be designed to meet specific styling and accessibility requirements, as outlined in a provided Figma spec. Additionally, there are stretch goals to integrate existing renderers for multimedia content to enhance the overall rendering experience.

Two baseline PRs are currently in work to help with the implementation of this project. 

- [HTML Renderer Plugin](https://github.com/learningequality/kolibri/pull/13274)
- [New Constants for File Assets](https://github.com/learningequality/le-utils/pull/163)

## 1.2 Goals

The main goals of this project are:

- Create a new HTML5 article renderer for `Kolibri` that can render HTML5 documents outside of iframes.
- Ensure that the renderer meets the styling and accessibility requirements outlined in the provided [Figma spec](https://www.figma.com/design/aJIh0jUr7NhYA7nbxkhK4h/HTML5-rendering-support).
- Implement responsive behaviors to ensure that the rendered content is user-friendly on various devices and screen sizes.
- Integrate existing renderers for multimedia content (audio, video, etc.) to enhance the overall rendering experience.
- Maintain a focus on accessibility conformance, aiming for WCAG 2.1 level AA compliance as closely as possible.

## 1.3 Benefits

The benefits of this project include:
- Improved user experience for content creators and learners using `Kolibri`, as the new renderer will allow for more flexible and responsive rendering of HTML5 documents.
- Reduced burden on content creators, as they will not need extensive knowledge of HTML and CSS to create rich text content.
- Increased accessibility of educational content, making it more usable for learners using assistive technologies.
- Enhanced rendering of multimedia content, providing a richer learning experience for users.

# 2. Approach

## 2.1 Research and Planning

The first step in this project will be to conduct research on the current HTML5 rendering capabilities of `Kolibri` and the existing work that has been done on sanitizing HTML5 documents. I will start by identifying the key components of the current renderer and understanding how it works. This will involve reviewing the existing codebase, documentation, and any relevant discussions in the `Kolibri` community.

Research will also involve reviewing the provided Figma spec to understand the styling and accessibility requirements for the new renderer. I will take note of specific design elements, i.e Allowed HTML tags, CSS styles, and responsive behaviors that need to be implemented in the new renderer. Additionally, I will research best practices for creating accessible web content and how to ensure that the new renderer meets WCAG 2.1 level AA compliance.

An example of how this step pan out can be the following:

- HTML Tag
  
| Tag | Description | Allowed Attributes | CSS Classes                                  |
| --- | ----------- | ------------------ | -------------------------------------------- |
| h1  | Heading 1   | id, class          | `text-bold`, `font-[32px]`, `leading-[130%]` |


- WCAG 2.1 Level AA Compliance
  
| Guideline | Description       | Implementation Strategy                                                                                                                                                                      |
| --------- | ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.1       | Text Alternatives | Ensure all images have alt attributes and provide text alternatives for non-text content                                                                                                     |
| 1.3       | Adaptable         | Use semantic HTML elements and ensure content is structured in a logical order                                                                                                               |
| 1.4       | Distinguishable   | Ensure sufficient color contrast between text and background, and provide options for resizing text without loss of content or functionality, i.e use of color, text spacing, and font size. |

## 2.2 Design and Prototyping

The next step will be to create a design and prototype for the new HTML5 article renderer. This will involve creating wireframes and mockups based on the provided Figma spec, as well as developing a prototype of the renderer itself. The prototype will be built using `Vue.js` and will include the key features and functionality outlined in the project goals.

Here are some example of how design and prototyping can be done:

- Create styling for headings, paragraphs, lists, and other HTML elements based on the provided Figma spec.
```scss
.safe-html {
  &h1 {
    font-size: 32px;
    line-height: 130%;
    font-weight: bold;
  }
  &h2 {
    font-size: 28px;
    line-height: 130%;
    font-weight: bold;
  }
}
```

> **Note**  
> `.safe-html` is a class that's applied to every HTML element that is rendered by the new renderer due to recursive subrendering. Hence, `&h1` and `&h2` are used to target the `h1` and `h2` elements inside the `.safe-html` class.

- Create a responsive layout for the rendered content, ensuring that it adapts to different screen sizes and devices.

```scss
.safe-html-container {
  max-width: 640px;
  margin: 0 auto;
  padding: 16px;
  @media (max-width: 768px) {
    max-width: 100%;
    padding: 8px;
  }
}
```

- Create sample test `HTML5` documents to test the renderer's functionality and ensure that it meets the styling and accessibility requirements.
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sample HTML5 Document</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1>Sample HTML5 Document</h1>
  <p>This is a sample paragraph to test the <strong>HTML5</strong> article renderer.</p>
  <h2>Subheading</h2>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
  </ul>
  <table>
    <thead>
      <tr>
        <th>Header 1</th>
        <th>Header 2</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Data 1</td>
        <td>Data 2</td>
      </tr>
    </tbody>
  </table>
  <img src="image.jpg" alt="Sample Image">
</body>
</html>
```

## 2.3 Integration and Testing

The final step will be to integrate support for the new HTML5 article renderer into `Kolibri` and test its functionality. This will involve updating the existing codebase to include the new renderer and ensuring that it works seamlessly with the rest of the platform. Content samples can be created to test the renderer's functionality and ensure that it meets the styling and accessibility requirements.

The new renderer will be tested using a combination of unit tests and manual testing. Unit tests will be created to test individual components and tags of the renderer, while manual testing will be used to test the overall functionality and user experience of the renderer. The testing process will include:
- Testing the rendering of various HTML5 documents, including those with inlined assets and HTML5 article zip files.
- Verifying accessibility compliance with WCAG 2.1 standards.
- Ensuring responsiveness across different devices and screen sizes.

Here is a sample test:
```javascript
import { mount } from '@vue/test-utils';
import HTML5Renderer from '@/components/HTML5Renderer.vue';

describe('HTML5 Article Renderer', () => {
  it('should render headings correctly', () => {
    const html = '<h1>Test Heading</h1>';
    const rendered = mount(HTML5Renderer, {
      propsData: { html }
    }).html();
    expect(rendered).toContain('<h1 class="safe-html">Test Heading</h1>');
    expect(rendered).toHaveStyle('font-size: 32px');
    expect(rendered).toHaveStyle('line-height: 130%');
    expect(rendered).toHaveStyle('font-weight: bold');
  });
});
```

Any necessary documentation will be updated to reflect the changes made to the renderer, and any relevant discussions in the `Kolibri` community will be documented. 

> **Note**  
> Currently tests are setup only for python and functional testing for plugins. Hence, to run the above test, we will need to set up a testing environment for `Vue.js` components. This can be done using `Jest` or `Mocha` as the testing framework and `Vue Test Utils` for mounting and testing Vue components.
> 
> Additionally, we should consider integrating continuous integration (CI) tools to automate the testing process and ensure that all tests are run consistently with each code change.

# 3. Timeline

The project is expected to be completed in `350` hours over the course of `12` weeks. The timeline is divided into `4` phases, each with its own set of tasks and milestones. On average, I would be devoting 15 hours per week to this project, with roughly 2-3 hours on weekdays and 4-5 hours on weekends. 

## 3.1 Community Bonding Period (May 8 - June 1)

- Familiarize myself with the `Kolibri` codebase and existing HTML5 rendering capabilities.
- Review the provided Figma spec and research best practices for creating accessible web content.
- Set up the development environment and tools needed for the project.

## 3.2 Renderer Development (June 2 - July 14)

Work on supporting most of the HTML5 tags and CSS styles as per the provided Figma spec. This will include:
- Implementing the new HTML5 article renderer using `Vue.js`.
- Creating styling for divs, headings, paragraphs, lists, tables, and other HTML elements based on the provided Figma spec.

## 3.3 Testing and Stretch Goals (July 15 - August 15)

- Conduct thorough testing of the new HTML5 article renderer, including unit tests and manual testing.
- Ensure that the renderer meets the styling and accessibility requirements outlined in the provided Figma spec.
- Integrate existing renderers for multimedia content (audio, video, etc.) to enhance the overall rendering experience.

## 3.4 Final Review and Documentation (August 16 - August 31)

- Conduct a final review of the project and ensure that all tasks and milestones have been completed.
- Update any necessary documentation to reflect the changes made to the renderer.
- Document any relevant discussions in the `Kolibri` community and share the results of the project with the community.

# 4. About Me

I am `Triyan Mukherjee` and currently a third year undergraduate student at the `Manipal Institute of Technology` in India, pursuing a degree in Computer and Communication Engineering. I have a strong background in Javascript/Typescript and have worked with multiple web frameworks. I have experience with documentation/technical writing and making guides, and have developed my own libraries and packages in the past and published them to PyPI. In the past I have worked on article renderers using `Next.js` and [Next MDX Remote](https://github.com/hashicorp/next-mdx-remote) for rendering markdown files, integrating with Sanity.io for content management.

- [Portfolio Website](https://github.com/FallenDeity/Nextjs-Portfolio/blob/dev/src/components/mdx/mdx-remote.tsx): A portfolio website built using Next.js and Next MDX Remote, for blogging using markdown content. All of github flavored features are implemented, and beyond those such as lightboxes, Callouts, Keyboard Shortcuts, Latex etc.
- [WhatsApp Web 2.0](https://github.com/FallenDeity/WhatsAppClone): A responsive and modern real-time WhatsApp clone made with Typescript, Next.js, Pusher, Prisma and integrating zegocloud for voice and video calls. This project implements all the features of WhatsApp Web, including sending and receiving messages, creating groups, and making voice and video calls. It also includes a responsive design that works on both desktop and mobile devices.

## 4.1 Contact Information

- Email: [triyanmukherjee@gmail.com](mailto:triyanmukherjee@gmail.com)
- LinkedIn: [triyanmukherjee](https://www.linkedin.com/in/triyan-mukherjee/)
- GitHub: [FallenDeity](https://github.com/FallenDeity)
- Timezone: GMT+5:30 (IST)
