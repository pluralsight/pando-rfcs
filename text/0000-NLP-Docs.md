- Start Date: (fill me in with today's date, 2023-08-20)
- RFC PR: (leave this empty)
- Pando Issue: (leave this empty)

# Before Reading

Please approach this document with a generous perspective understanding that it
is a Markdown file so all examples are _theory_ and **have not been prototyped**.
You may see "gotchas" which is normal.

If you choose to contribute, please address the overall purpose of the RFC and not
nit-picking the "gotchas" in code examples. :heart:

# Summary

This RFC proposes the integration of Natural Language Processing (NLP) capabilities with the Pando library. This integration aims to enhance the user experience and provide advanced content searching, implementation examples, recommendations, and summarization features within the documentation ecosystem.

# Motivation

<!-- Why are we doing this? What use cases does it support? What is the expected
outcome?

Please focus on explaining the motivation so that if this RFC is not accepted,
the motivation could be used to develop alternative solutions. In other words,
enumerate the constraints you are trying to solve without coupling them too
closely to the solution you have in mind. -->

The motivation behind integrating NLP with the Pando library is to address several key use cases:

1. **Advanced Content Search**: Users often struggle to find specific information within extensive documentation. NLP-powered search can offer more accurate and contextually relevant search results, improving the overall usability of the documentation.

2. **Implementation Examples**: Users unfamiliar with front-end work or the Pando Library can generate implementations for simple or combined components, leading to earlier and more widespread adoption of the Pando design system.

3. **Content Recommendation**: By analyzing user interactions, NLP can suggest related documentation sections, tutorials, or examples, helping users discover additional relevant content.

4. **Automatic Summarization**: Long-form documentation pages can be daunting. NLP can automatically generate concise summaries for sections, making it easier for users to grasp the main concepts.

# Detailed design

<!-- This is the bulk of the RFC. Explain the design in enough detail for somebody
familiar with a Pando library to understand, and for somebody familiar with the
implementation to implement. This should get into specifics and corner-cases,
and include examples of how the feature is used. Any new terminology should be
defined here. -->

I have split the design section into short term goals that focus on implementing a NLP-powered search for Pando's documentation site and long term goals tht focus on improving and expanding capabilities.

## Short Term Integration Goals

1. **NLP Backend**: Select an NLP backend service capable of processing natural language queries and generating relevant content suggestions. We should consider some existing NLP services first to learn what makes sense in terms of security, cost, and performance. Amazon Comprehend (AWS), Apache Spark (Microsoft Azure), ChatGPT (OpenAI), and HuggingFace (Open Source) all offer features and API's that could be used with Pando's data.

2. **Data Collection and Preparation**: The finer details of implementation are dependent on which NLP backend is selected as each have unique pre-trained models they leverage. However most involve some sort of preprocessing and tokenization where text data is tokenized into words/subwords for NLP model consumption; followed by fine-tuning the model on Pando's domain-specific tasks, such as answering questions about components, providing explanations, or generating descriptions.

3. **Frontend Component**: Create frontend components that interact with the NLP backend to provide search results, content recommendations, and summaries. An implementation example is below using ChatGPT where `userInput` would be the result from a search bar or chat window.

```js
import * as openai from "openai";

openai.apiKey = "YOUR_OPENAI_API_KEY";

async function getChatGPTResponse(userInput: string): Promise<string> {
  const conversation = [{ role: "user", content: userInput }];

  const response = await openai.Completion.create({
    model: "gpt-3.5-turbo",
    messages: conversation,
  });

  return response.choices[0].message.content;
}

const userInput = "How do I use the button component?";
getChatGPTResponse(userInput)
  .then((response) => {
    console.log(response);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```

## Long Term Integration Goals

1. **User Interaction Tracking**: Implement mechanisms to track user interactions (e.g., clicks, searches) to refine NLP models and improve recommendation accuracy. FullStory is a great tool for tracking user interactions throughout a website.

2. **VSCode Extension**: Create a plugin that allows Pando to interact directly with front-end components in a Pando user's environment, enabling easier and quicker adoption of the Pando Library.

# Drawbacks

<!-- Why should we *not* do this? Please consider:

- implementation cost, both in term of code size and complexity
- whether the proposed feature can be implemented in user space
- the impact on teaching people a Pando library
- integration of this feature with other existing and planned features
- cost of migrating existing Pando applications (is it a breaking change?)

There are tradeoffs to choosing any path. Attempt to identify them here. -->

- Implementation complexity and potential performance impact due to NLP processing.
- Integration might require additional dependencies and potentially increase the bundle size.
- NLP may not always produce accurate results, leading to user frustration.
- Potentially costly depending on service chosen.

# Alternatives

<!-- What other designs have been considered? What is the impact of not doing this? -->

- **External Search Engines**: Instead of building NLP capabilities, rely on external search engines like Algolia (already implemented) for advanced search features.
- **Manual Content Tagging**: Allow authors to manually tag content for improved search and recommendations.

# Adoption strategy

<!-- If we implement this proposal, how will existing Pando developers adopt it? Is
this a breaking change? Can we write a codemod? Should we coordinate with
other projects or libraries? -->

As proposed, this feature should have little affect on existing Pando users implementations of the design system. However, there should be release notes and a guide to leveraging Pando's new feature upon release so user's are quickly able to integrate Pando's NLP model into their workflow for using and understanding the design system.

# How we teach this

<!-- What names and terminology work best for these concepts and why? How is this
idea best presented? As a continuation of existing Pando patterns?

Would the acceptance of this proposal mean the Pando documentation must be
re-organized or altered? Does it change how Pando is taught to new developers
at any level?

How should this feature be taught to existing Pando developers? -->

There shouldn't be much teaching needed outside of release notes, documentation, and a blog post outlining the NLP search functionality and how to best leverage it. This feature is built to help interact with Pando's docs and should be intuitive and enhance the existing documentation, not necessarily change it.

# Unresolved questions

- How can we ensure the performance of NLP processing remains acceptable for consumers of Pando docs?
- What service should we use to provide a pre-trained model?
- How much will this cost and is that cost worth the benefits?
- How accurate is the NLP model's analysis of the documentation content?
- How are cases handled where the NLP model might generate false positives or interpret user queries incorrectly?
- How much effort is required to train or fine-tune the NLP model for Pando's domain?
