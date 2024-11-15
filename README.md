<p>
   An AI agent that writes and fixes code for you.
</p>

<p>
   <a href="https://www.npmjs.com/package/@khulnasoft.com/micro-ai"><img src="https://img.shields.io/npm/v/@khulnasoft.com/micro-ai" alt="Current version"></a>
</p>
<br>

# Micro AI

Just run `micro-ai`, give it a prompt, and it'll generate a test and then iterate on code until all test cases pass.

## Why?

LLMs are great at giving you broken code, and it can take repeat iteration to get that code to work as expected.

So why do this manually when AI can handle not just the generation but also the iteration and fixing?

### Why a "micro" agent?

AI agents are cool, but general-purpose coding agents rarely work as hoped or promised. They tend to go haywire with compounding errors. Think of your Roomba getting stuck under a table, x1000.

The idea of a micro ai is to

1. Create a definitive test case that can give clear feedback if the code works as intended or not, and
2. Iterate on code until all test cases pass

Read more on [why Micro AI exists](https://www.khulnasoft.com/blog/micro-ai).

<img width="1270" alt="Micro AI Diagram" src="https://github.com/khulnasoft/micro-ai/assets/844291/406496dd-3be8-491b-a5f0-2960dd924013">

### What this project is not

This project is not trying to be an end-to-end developer. AI agents are not capable enough to reliably try to be that yet (or probably very soon). This project won't install modules, read and write multiple files, or do anything else that is highly likely to cause havoc when it inevitably fails.

It's a micro ai. It's small, focused, and does one thing as well as possible: write a test, then produce code that passes that test.

## Installation

> Micro AI requires [Node.js](https://nodejs.org/) v18 or later.

```bash
npm install -g @khulnasoft.com/micro-ai
```

## Getting Started

The best way to get started is to run Micro AI in interactive mode, where it will ask you questions about the code it generates and use your feedback to improve the code it generates.

```bash
micro-ai
```

Look at that, you're now a test-driven developer. You're welcome.

## Running Manually

### Add an LLM API key

Micro AI works with Claude, OpenAI, Ollama, or any OpenAI compatible provider such as Groq. You need to add your API key to the CLI:

```bash
micro-ai config set OPENAI_KEY=<your token>
micro-ai config set MODEL=gpt-4o
```

Or, for Claude:

```bash
micro-ai config set ANTHROPIC_KEY=<your token>
micro-ai config set MODEL=claude
```

To use a custom OpenAI API endpoint, such as for use with Ollama or Groq, you can set the endpoint with:

```bash
micro-ai config set OPENAI_API_ENDPOINT=<your endpoint>
micro-ai config set OPENAI_API_ENDPOINT=https://api.groq.com/openai/v1
```

### Unit test matching

![Demo](https://cdn.khulnasoft.com/api/v1/file/assets%2FYJIGb4i01jvw0SRdL5Bt%2F4e8b02abb3e044118f070d9a7253003e)

To run the Micro AI on a file in unit test matching mode, you need to provide a test script that will run after each code generation attempt. For instance:

```bash
micro-ai ./file-to-edit.ts -t "npm test"
```

This will run the Micro AI on the file `./file-to-edit.ts` running `npm test` and will write code until the tests pass.

The above assumes the following file structure:

```bash
some-folder
├──file-to-edit.ts
├──file-to-edit.test.ts # test file. if you need a different path, use the -f argument
└──file-to-edit.prompt.md # optional prompt file. if you need a different path, use the -p argument
```

By default, Micro AI assumes you have a test file with the same name as the editing file but with `.test.ts` appended, such as `./file-to-edit.test.ts` for the above examples.

If this is not the case, you can specify the test file with the `-f` flag. You can also add a prompt to help guide the code generation, either at a file located at `<filename>.prompt.md` like `./file-to-edit.prompt.md` or by specifying the prompt file with the `-p`. For instance:

```bash
micro-ai ./file-to-edit.ts -t "npm test" -f ./file-to-edit.spec.ts -p ./path-to-prompt.prompt.md
```

### Visual matching (experimental)

![Visual Demo](https://cdn.khulnasoft.com/api/v1/file/assets%2FYJIGb4i01jvw0SRdL5Bt%2Fe90f6d4158b44a8fb9adeee3be3dbe82)

> [!WARNING]
> This feature is experimental and under active development. Use with caution.

Micro AI can also help you match a design. To do this, you need to provide a design and a local URL to your rendered code. For instance:

```bash
micro-ai ./app/about/page.tsx --visual localhost:3000/about
```

Micro ai will then generate code until the rendered output of your code more closely matches a screenshot file that you place next to the code you are editing (in this case, it would be `./app/about/page.png`).

The above assumes the following file structure:

```bash
app/about
├──page.tsx # The code to edit
├──page.png # The screenshot to match
└──page.prompt.md # Optional, additional instructions for the AI
```

### Adding an Anthropic API key

> [!NOTE]
> Using the visual matching feature requires an Anthropic API key.

OpenAI is simply just not good at visual matching. We recommend using [Anthropic](https://anthropic.com/) for visual matching. To use Anthropic, you need to add your API key to the CLI:

```bash
micro-ai config set ANTHROPIC_KEY=<your token>
```

Visual matching uses a multi-agent approach where Anthropic Claude Opus will do the visual matching and feedback, and then OpenAI will generate the code to match the design and address the feedback.

![Visual of the multi agent approach](https://cdn.khulnasoft.com/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2F427929ba84b34ac6a0f1fda104e60ecd)

### Integration with Figma

Micro AI can also integrate with [Visual Copilot](https://www.khulnasoft.com/c/docs/visual-copilot) to connect directly with Figma to ensure the highest fidelity possible design to code, including fully reusing the exact components and design tokens from your codebase.

Visual Copilot connects directly to Figma to assist with pixel perfect conversion, exact design token mapping, and precise reusage of your components in the generated output.

Then, Micro AI can take the output of Visual Copilot and make final adjustments to the code to ensure it passes TSC, lint, tests, and fully matches your design including final tweaks.

![Visual Copilot demo](https://cdn.khulnasoft.com/api/v1/file/assets%2FYJIGb4i01jvw0SRdL5Bt%2Fa503ad8367d746f3879db1a155728cb2)

## Configuration

### Max runs

By default, Micro AI will do 10 runs. If tests don't pass in 10 runs, it will stop. You can change this with the `-m` flag, like `micro-ai ./file-to-edit.ts -m 20`.

### Config

You can configure the CLI with the `config` command, for instance to set your OpenAI API key:

```bash
micro-ai config set OPENAI_KEY=<your token>
```

or to set an Anthropic key:

```bash
micro-ai config set ANTHROPIC_KEY=<your token>
```

By default Micro AI uses `gpt-4o` as the model, but you can override it with the `MODEL` config option (or environment variable):

```bash
micro-ai config set MODEL=gpt-3.5-turbo
```

or, if you supply an Anthropic key, you can use any Claude model. by default `claude` is an alias to `claude-3-5-sonnet-20241022`:

```bash
micro-ai config set MODEL=claude
```

#### Config UI

To use a more visual interface to view and set config options you can type:

```bash
micro-ai config
```

To get an interactive UI like below:

```bash
◆  Set config:
│  ○ OpenAI Key
│  ○ Anthropic Key
│  ○ OpenAI API Endpoint
│  ● Model (gpt-4o)
│  ○ Done
└
```

#### Environment variables

All config options can be overridden as environment variables, for instance:

```bash
MODEL=gpt-3.5-turbo micro-ai ./file-to-edit.ts -t "npm test"
```

### Upgrading

Check the installed version with:

```bash
micro-ai --version
```

If it's not the [latest version](https://github.com/khulnasoft/micro-ai/tags), run:

```bash
micro-ai update
```

Or manually update with:

```bash
npm update -g @khulnasoft.com/micro-ai
```

## Contributing

We would love your contributions to make this project better, and gladly accept PRs. Please see [./CONTRIBUTING.md](CONTRIBUTING.md) for how to contribute.

If you are looking for a good first issue, check out the [good first issue](https://github.com/khulnasoft/micro-ai/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) label.

## Feedback

If you have any feedback, please open an issue or @ me at [@steve8708](https://twitter.com/steve8708) on Twitter.

## Usage

```bash
Usage:
  micro-ai <file path> [flags...]
  micro-ai <command>

Commands:
  config        Configure the CLI
  update        Update Micro AI to the latest version

Flags:
  -h, --help                      Show help
  -m, --max-runs <number>         The maximum number of runs to attempt
  -p, --prompt <string>           Prompt to run
  -t, --test <string>             The test script to run
  -f, --test-file <string>        The test file to run
  -v, --visual <string>           Visual matching URL
      --thread <string>           Thread ID to resume
      --version                   Show version
```

<br><br>
