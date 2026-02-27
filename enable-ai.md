---
title: How to enable AI in Omniscope
breadcrumb: AI
---

Omniscope integrates AI capabilities in various places, but they are not enabled by default and need some configuration steps, as follows.

## Enabling labs features

Some AI settings require particular labs features to be enabled. For example, **Report Ninja**. Go to "Admin > Labs" and tick "Report Ninja". Save, then reload the page.

## Finding AI settings

Omniscope AI settings are configurable and customisable at the folder level, currently within the **Create/Edit Permissions dialog**, available from the corner in the folder list page:

![Folder list corner menu](images/enable-ai-labs.png)

![AI Settings location](images/enable-ai-settings.png)

For convenience, you can also access the root folder's AI settings (which will apply server-wide if no subfolders override them) from "Admin > AI Settings".

## AI Settings

The AI settings dialog/section will appear somewhat like this:

![AI Settings dialog](images/enable-ai-providers.png)

AI settings comprise two main concepts:

- **Providers** are services with large language models (LLMs) that Omniscope connects to, in order to perform AI tasks. Currently you are most likely to be using OpenAI, but can use Azure OpenAI or a Custom provider that is OpenAI-compatible, such as a local model via llama.cpp. Commercial providers such as OpenAI require an API key and may require a paid subscription.
- **Integrations** are aspects of Omniscope that take advantage of AI capabilities. There are currently 6 such cases: Report Ninja, Workflow Ninja, Data Q&A View, AI Block, Custom SQL and Workflow Executions.

The set of providers, and each integration, can be separately configured in a given folder, or allowed to inherit from the parent folder. You can also restrict subfolders from overriding the configuration defined at a given level, as a way of applying a policy to control AI use on a given server.

### Providers

By default there won't be any providers, so you won't be able to use AI features. You will typically need to sign up for an account at [https://platform.openai.com/](https://platform.openai.com/), and go to "Settings > API keys", create an All-permissions key, and then come back to Omniscope. Add an OpenAI provider and enter the API key.

![OpenAI provider settings](images/enable-ai-openai-key.png)

You don't need to touch any other settings.

Alternatively, if you have a local / on-prem / other OpenAI-compatible provider, configure it by adding a "Custom" provider. This is beyond the scope of this article, and requires an Enterprise licence.

See [Using a local AI model](local-ai-model.md) or [Setting up an Azure OpenAI provider](https://help.visokio.com/support/solutions/articles/42000112990-setting-up-an-azure-openai-provider).

### Integrations: Report Ninja

Integrations are enabled by default, but will only come to life when you've configured a provider (above), and, currently, have also chosen a Default model. For example here for Report Ninja, we've chosen gpt-4o-mini, a very cost-effective option and surprisingly capable model.

![Report Ninja integration settings](images/enable-ai-add-provider.png)

You can pick other models. Note that the costs shown are a snapshot and may not be correct at a given time. Models that are known to be incompatible are excluded from the list. New models Omniscope does not recognise will show up marked as "unknown". If you choose an unknown model, it may not work correctly.

![Model selection list](images/enable-ai-report-ninja.png)

You will now see Instant Dashboard appear in the "Create project" section, and can use Instant Dashboard report preset in the Workflow "Add block" menu. And for any existing report, you can use the corner menu "Ask Report Ninja" to access it, and get an assistant that helps, suggests, answers questions and edits your dashboard:

![Report Ninja in action](images/enable-ai-integrations.png)

Report Ninja works well with gpt-4o-mini, gpt-4o, and o3-mini from OpenAI, and also with some larger local models such as DeepSeek-R1-Distill-Qwen-32B-Q4_K_M.gguf via llama.cpp on a MacBook Pro M2 or better, although there are many possibilities in terms of providers and models.

### Other integrations

The **Workflow Ninja** helps you understand and explain complex workflows - showing how fields flow and how data is transformed. Answers are enhanced with diagrams and block links that highlight the relevant block.

The **Data Q&A View** lets editors and viewers ask natural language questions across multiple data sources. It uses Omniscope to query and analyse the data, returning answers supported by diagrams, interim results and explanations.

The **AI Block** integration lets you create custom workflow sources/operations/outputs using Python code written by the AI in response to your questions and instructions. Works particularly well with o3-mini.

The **Custom SQL** integration allows you to write a natural language query and automatically generate the SQL query. Find this after enabling this integration in the Database block Options tab upon choosing Custom SQL.

The **Workflow Executions** integration provide AI assistance in workflow executions. The AI Completion block is one such example, which can be used to generate, transform, or analyse text.

## Settings wrap-up

That's it. To summarise:

1. Enable the feature flag Report Ninja (if required).
2. Add a provider, typically OpenAI, and an API key.
3. In (e.g.) Report Ninja integration settings, choose a default model such as gpt-4o.
