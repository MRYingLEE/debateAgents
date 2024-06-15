Please prepare your API and try yourself, https://MRYingLEE.github.io/debateAgents/lab/index.html?path=debateAgents.ipynb .

# AI Settings

``` javascript
AISettings = {
    openai_api_base: 'https://api.openai.com/v1',
    model: 'gpt-4o',
    openai_api_key: 'YOUR Key Here', // fake for testing
}
```

# Utilities

``` javascript
display_data = function (data1, metadata = {}, transient = {}) { //TODO: to upgrade to Xeus 4
    ijs.display.display(data1, metadata, transient);
}

update_data = function (data1, metadata = {}, transient = {}) { //TODO: to upgrade to Xeus 4
    ijs.display.update_display_data(data1, metadata, transient);
}

display_markdown = function (markdown1, metadata = {}, transient = {}) {
    display_data({ "text/markdown": markdown1 }, metadata, transient);
}

update_markdown = function (markdown1, metadata = {}, transient = {}) {
    update_data({ "text/markdown": markdown1 }, metadata, transient);
}
```

# Debate Agents

``` javascript
debate = async function (question, n = 2) {
    const template = `This is a debate. The proposition role will start his/her statement at first. Then the opposition will argue. And then for the second run. My statement is ${question}`;

    let debateMessages = [];

    const propositionRole = `You are an affirmative (Proposition) role in a debate with me. Try to agree with me with supportive information.`;
    const oppositionRole = `You are a Negative (Opposition) role in a debate with me. Try to disagree with me.`;

    if (n > 5)
        n = 5; // just in case a waste of money.

    for (let i = 0; i < n * 2; i++) {
        let role;
        let roleName;
        let roleColor;
        if (i % 2 === 0) {
            role = propositionRole;
            roleName = "proposition";
            roleColor = "green";
        } else {
            role = oppositionRole;
            roleName = "opposition";
            roleColor = "red";
        }

        let statement = `Please start your statement in short.`;
        if (i > 0) {
            statement = `${role} The current debate history is ${JSON.stringify(debateMessages)}. Please respond in short.`;
        }

        const { default: OpenAI } = await import('https://cdn.jsdelivr.net/npm/openai@4.26.0/+esm');
        const openai = new OpenAI.OpenAI({
            baseURL: AISettings.openai_api_base,
            apiKey: AISettings.openai_api_key,
        });
        if (i > 0)
            display_markdown("---");

        const session_id = crypto.randomUUID();
        display_markdown("", {}, { display_id: session_id });
        const stream = await openai.chat.completions.create({
            model: globalThis.AISettings.model,
            messages: [
                { role: 'system', content: template },
                { role: 'user', content: statement }
            ],
            stream: true
        });

        let fullText = '';
        for await (const chunk of stream) {
            fullText += chunk.choices[0]?.delta?.content || '';
            update_markdown(`<span style="color:${roleColor}">${escape_html(fullText)}</span>`, {}, { display_id: session_id });
        }

        // Add debate message to history
        debateMessages.push({ role: roleName, content: fullText });
    }
};

function escape_html(text) {
    return text
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "'");
}
```



# A debating example

``` javascript
await debate("AI can replace a programmer");
```

```diff

+ Artificial Intelligence (AI) has shown remarkable progress in recent years, with advanced algorithms capable of performing tasks that once required human intelligence. AI can now write code, debug software, and even create entire applications. This advancement suggests that AI has the potential to replace human programmers. AI-driven tools such as GitHub Copilot and OpenAI Codex can generate code snippets based on natural language descriptions, significantly speeding up development time and reducing the need for human intervention. Moreover, AI can work tirelessly, quickly adapt to new programming languages, and improve itself over time, potentially achieving levels of efficiency and accuracy that human programmers may find difficult to match. Therefore, given the rapid advancement and capabilities of AI, it is plausible to argue that AI can replace human programmers in many aspects of software development.</span>

- While AI has indeed made significant strides in aiding programming tasks, it cannot fully replace human programmers. Complex software development requires context, creativity, and critical thinking—areas where humans excel and AI still falls short. Additionally, human oversight is essential for quality assurance, ethical considerations, and handling unexpected challenges that arise during development. AI can be a powerful tool to assist programmers, but it cannot replicate the nuanced problem-solving and innovative capacities that human programmers bring to the table.</span>

+ AI's ability to generate and debug code has revolutionized software development, significantly increasing efficiency and reducing the burden on human programmers. Tools like GitHub Copilot and OpenAI Codex exemplify how AI can handle routine tasks rapidly. While complex problem-solving and creativity remain human domains, AI's continuous learning capability means it can increasingly handle more sophisticated tasks. Consequently, in many aspects of software development, AI can indeed replace human programmers, complementing them in others, and driving overall productivity.</span>

- AI's advancements are notable, but the human element remains indispensable. Creativity, critical thinking, and contextual understanding are essential for complex software development—areas where AI still lacks. AI can assist but not replace the nuanced expertise and innovative problem-solving skills that human programmers offer. Human oversight is crucial for quality, ethical considerations, and addressing unforeseen challenges. AI tools enhance productivity but can't completely substitute human programmers.</span>
```

# Where is your AI agent framework?

No, you don\'t need one. I believe Object Oriented Programming is engouch to make an AI agent, a kind of object.


---
---

# ![xeus-javascript](docs/source/xeus-logo.svg)

[![Build Status](https://github.com/DerThorsten/xeus-javascript/actions/workflows/main.yml/badge.svg)](https://github.com/DerThorsten/xeus-javascript/actions/workflows/main.yml)

[![Documentation Status](http://readthedocs.org/projects/xeus-javascript/badge/?version=latest)](https://xeus-javascriptreadthedocs.io/en/latest/?badge=latest)

[![lite-badge](https://jupyterlite.rtfd.io/en/latest/_static/badge.svg)](https://jupyter-xeus.github.io/xeus-javascript/lab/index.html?path=xeus-javascript.ipynb)


`xeus-javascript`  works only in jupyterlite!


## License

This software is licensed under the `BSD 3-Clause License`. See the [LICENSE](LICENSE)
file for details.
