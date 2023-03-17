# 模型
[查看原版文档](https://platform.openai.com/docs/models)

## 概述

OpenAI API由一组具有不同功能和价格的模型组成。你还可以通过调优对我们的原始基本模型进行有限的定制。

| 模型                                                                 | 描述                            |
| ------------------------------------------------------------------ | ----------------------------- |
| [GPT-3.5](#gpt-3-5)         | 一组基于GPT-3改进的模型，可以理解和生成自然语言或代码 |
| [DALL·E](#dall-e) ```Beta```          | 一个可以根据给定的自然语言prompt生成和编辑图像的模型 |
| [Whisper](#whisper) ```Beta```        | 可以将音频转换为文本的模型                 |
| [Embeddings](#embeddings)   | 一组可以将文本转换为数值形式的模型             |
| [Codex](#codex)```Limited beta``` | 一组能够理解和生成代码的模型，包括将自然语言转换为代码   |
| [Moderation](#moderation)   | 一个经过微调的模型，可以检测文本是否敏感或不安全      |
| [GPT-3](#gpt-3)             | 一组能够理解和生成自然语言的模型              |

我们还发布了包括[Point-E](https://github.com/openai/point-e)、[Whisper](https://github.com/openai/whisper)、 [Jukebox](https://github.com/openai/jukebox),和 [CLIP](https://github.com/openai/CLIP)在内的开源模型。

访问我们[为研究人员提供的模型索引](https://platform.openai.com/docs/model-index-for-researchers)，了解更多在我们的研究论文中被精选出的模型，以及像InstructGPT和GPT-3.5这样的模型系列之间的差异。

## <a id="gpt-3-5">GPT-3.5</a>

GPT-3.5模型可以理解和生成自然语言或代码。我们最有能力和最具成本效益的模型是```gpt-3.5-turbo```，它针对聊天进行了优化，但也适用于传统的completion任务。

| 最新模型               | 描述                                                                                                                                               | 最大请求数        | 训练数据      |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------ | --------- |
| gpt-3.5-turbo      | 为聊天优化过的，功能最强大的GPT-3.5模型，成本为text-davinci-003的十分之一。将与我们最新的模型迭代更新。                                                                                  | 4,096 tokens | 截至2021年9月 |
| gpt-3.5-turbo-0301 | 2023年3月1日gpt-3.5-turbo的快照。与gpt-3.5-turbo不同的是，该型号将不接受更新，并且只支持三个月的时间，截止到2023年6月1日。                                                                 | 4,096 tokens | 截至2021年9月 |
| text-davinci-003   | 与curie, babbage或ada模型相比，能以更好的质量，更长的输出和一致的指令遵循来完成任何语言任务。还支持在文本中[插入](https://platform.openai.com/docs/guides/completion/inserting-text)completion。 | 4,000 tokens | 截至2021年6月 |
| text-davinci-002   | 与text-davinci-003功能类似，但使用监督微调而不是强化学习进行训练                                                                                                         | 4,000 tokens | 截至2021年6月 |
| code-davinci-002   | 为代码completion任务优化过                                                                                                                               | 4,000 tokens | 截至2021年6月

我们建议在实验时使用```gpt-3.5-turbo```，因为它将产生最好的结果。一旦你让事情运转起来，你就可以看看其他模型是否能以更低的延迟和成本产生相同的结果。

OpenAI模型是非确定性的，这意味着相同的输入可以产生不同的输出。将[temperature](https://platform.openai.com/docs/api-reference/completions/create#completions/create-temperature)设置为0将使输出大部分是确定的，但仍可能会保留少量的可变性。

## Feature-specific模型

新的```gpt-3.5-turbo```模型针对聊天进行了优化的同时，对传统的completion任务也非常有效。原始的GPT-3.5模型针对[文本completion](https://platform.openai.com/docs/guides/completion)进行了优化。

我们用于创建[embeddings](https://platform.openai.com/docs/guides/embeddings) 和[编辑文本](https://platform.openai.com/docs/guides/completion/editing-text)的端点使用它们自己的专用模型集。

## Turbo

Turbo与支持ChatGPT的模型属于同一个模型家族。它针对会话聊天的输入和输出进行了优化，但与Davinci模型家族相比，它在completion方面表现同样出色。任何在ChatGPT中可以很好地完成的用例都应该在Turbo模型家族中表现良好。

Turbo也是第一个如ChatGPT一样接受模型定期更新的模型家族。

擅长：会话和文本生成

## Davinci

Davinci是最有能力的模型家族，它能执行其他模型(ada、curie和babbage)可以执行的任何任务，而且通常只需要更少的指令。对于需要大量理解内容的应用程序，如针对特定受众的摘要和创造性内容的生成，Davinci将产生最好的结果。这些增加的功能需要更多的计算资源，因此Davinci每次API调用的成本更高，速度也不如其他模型快。

Davinci的另一个亮点是对文本意图的理解。Davinci善于解决各种逻辑问题，善于解释人物的动机。Davinci已经能够解决一些涉及因果关系的最具挑战性的人工智能问题。

擅长：复杂的意图，因果关系，为受众做摘要

## Curie

Curie非常强大，而且非常快。虽然Davinci在分析复杂文本方面更强，但Curie在处理情感分类和做摘要等微妙任务方面相当有能力。Curie还非常擅长回答问题和进行问答，并能做一个优秀的通用聊天机器人。

擅长：语言翻译、复杂分类、文本感悟、摘要

## Babbage

Babbage可以执行类似简单的分类这种不复杂的任务。当涉及到语义搜索时，它也能很好地根据查询结果的匹配程度对搜索结果进行排名。

擅长：适度分类，语义搜索分类

## Ada

Ada通常是最快的模型，可以执行文本解析、地址更正和某些元素间相似度较低的的分类任务。Ada的表现通常可以通过提供更多的上下文来提高。

擅长：文本解析，简单分类，地址更正，关键词

注：任何由Ada等更快的模型执行的任务都可以由Curie或Davinci等更强大的模型执行。

## 找到正确的模型

试验```gpt-3.5-turbo```是了解API功能的好方法。在你知道你想要做什么之后，你可以继续使用```gpt-3.5-turbo```或其他模型，并尝试围绕它的功能进行优化。

你可以使用[GPT比较工具](https://gpttools.com/comparisontool)，该工具允许你并排运行不同的模型来比较输出、设置和响应时间，然后将数据下载到Excel电子表格中。

##<a id="dall-e">DALL·E ```Beta```</a>

DALL·E是一个可以根据自然语言的描述创建逼真的图像和艺术品的人工智能系统。我们目前支持以下功能，在给定prompt的情况下创建固定尺寸的新图像、编辑现有图像或创建用户提供的图像的变体。

当前我们API提供的DALL·E模型是第二次迭代的成果，它产生的图像比一代DALL·E模型更真实、准确，并且分辨率提高了4倍。你可以通过我们的[实验室接口](https://labs.openai.com/)或[API](https://platform.openai.com/docs/guides/images/introduction)进行尝试。

## <a id="whisper">Whisper ```Beta```</a>

Whisper是一种通用的语音识别模型。它是在有各种音频的大型数据集上训练的，也是一个多任务模型，可以执行多语言语音识别以及语音翻译和语言识别。Whisper v2-large模型目前可以使用Whisper -1模型的名称，通过我们的API访问。

目前，[Whisper的开源版本](https://github.com/openai/whisper)和通过我们的API可用的版本之间没有区别。然而，通过我们的API，我们提供了一个优化的推理过程，这使得通过我们的API运行Whisper比通过其他方式快得多。关于Whisper的更多技术细节，你可以阅读[相关论文](https://arxiv.org/abs/2212.04356)。

## <a id="embeddings">Embeddings</a>

Embedding是一种文本的数字表示，可用于测量两段文本之间的相关性。我们的第二代Embedding模型，```text-embedded-ada-002```，是一种低成本的模型，它被用来取代之前的16个第一代Embedding模型。Embedding对于搜索、聚类、推荐、异常检测和分类任务非常有用。你可以在[公告博客文章](https://openai.com/blog/new-and-improved-embedding-model)中了解更多关于我们最新的Embedding模型的信息。

## <a id="codex">Codex ```Limited beta```</a>

Codex模型是GPT-3模型的后代，它可以理解和生成代码。它们的训练数据既包含自然语言，也包含来自GitHub的数十亿行公共代码。[了解更多](https://help.openai.com/en/articles/5480054)。

它们最擅长Python，精通十多种语言，包括JavaScript、Go、Perl、PHP、Ruby、Swift、TypeScript、SQL甚至Shell。

我们目前提供两种Codex模型:

| 最新模型             | 描述                                                                                                                   | 最大请求数              | 训练数据      |
| ---------------- | -------------------------------------------------------------------------------------------------------------------- | ------------------ | --------- |
| code-davinci-002 | 能力最强的Codex模型。特别擅长将自然语言转换为代码。除了完成代码，还支持在代码中在代码中[添加](https://platform.openai.com/docs/guides/code/inserting-code)补全结果。 | 8,000 tokens       | 截至2021年6月 |
| code-cushman-001 | 几乎和davinci Codex一样，但速度稍快。这种速度优势可能使它更适合于实时应用程序。                                                                       | Up to 2,048 tokens |           |

欲了解更多，请访问我们的[Codex工作指南](https://platform.openai.com/docs/guides/code)。

## <a id="moderation">Moderation</a>

Moderation模型用于检查内容是否符合OpenAI的[使用政策](Usage_Policies.md)。这些模型提供了分类功能，可以在以下类别中查找内容:仇恨、仇恨/威胁、自残、性、性/未成年人、暴力和暴力/图像。你可以在我们的[审核指南](https://platform.openai.com/docs/guides/moderation/overview)中找到更多信息。

| 模型                     | 描述                           |
| ---------------------- | ---------------------------- |
| text-moderation-latest | 最有能力的moderation模型。精度将略高于稳定模型 |
| text-moderation-stable | 能力几乎和最新型号一样，只是稍微不那么新。        |

## <a id="gpt-3">GPT-3</a>
GPT-3模型能够理解和生成自然语言。这些模型被更强大的GPT-3.5代模型所取代。然而，最初的GPT-3基础模型(```davinci```、```curie```、```ada```和```babbage```)是目前唯一可用于微调的模型。

| 最新模型             | 描述                                   | 最大请求数        | 训练数据       |
| ---------------- | ------------------------------------ | ------------ | ---------- |
| text-curie-001   | 非常有能力，比davinci更快，成本更低。               | 2,048 tokens | 截至2019年10月 |
| text-babbage-001 | 能够完成简单任务，非常快，成本更低。                   | 2,048 tokens | 截至2019年10月 |
| text-ada-001     | 能够完成非常简单的任务，通常是GPT-3系列中速度最快的模型，成本最低。 | 2,048 tokens | 截至2019年10月 |
| davinci          | 功能最强大的GPT-3模型。能做任何其他模型可以做的任务，往往质量更高。 | 2,048 tokens | 截至2019年10月 |
| curie            | 非常有能力，但比davinci更快，成本更低。              | 2,048 tokens | 截至2019年10月 |
| babbage          | 能够完成简单任务，非常快，成本更低。                   | 2,048 tokens | 截至2019年10月 |
| ada              | 能够完成非常简单的任务，通常是GPT-3系列中速度最快的模型，成本最低。 | 2,048 tokens | 截至2019年10月

## 模型终端的兼容性

| 模型名称                 | 终端                                        |
| ----------------------- | ------------------------------------------------ |
| gpt-3.5-turbo           | /v1/chat/completions                             |
| gpt-3.5-turbo-0301      | /v1/chat/completions                             |
| text-davinci-003        | /v1/completions                                  |
| text-davinci-002        | /v1/completions`                                |
| text-davinci-edit-001   | /v1/edits                                        |
| code-davinci-edit-001   | /v1/edits                                        |
| whisper-1               | /v1/audio/transcriptions, /v1/audio/translations |
| text-curie-001          | /v1/completions                                  |
| text-babbage-001        | /v1/completions                                  |
| text-ada-001            | /v1/completions                                  |
| davinci                 | /v1/completions, /v1/fine-tunes                  |
| curie                   | /v1/completions, /v1/fine-tunes                  |
| babbage                 | /v1/completions, /v1/fine-tunes                  |
| ada                     | /v1/completions, /v1/fine-tunes                  |
| text-embedding-ada-002  | /v1/embeddings                                   |
| text-search-ada-doc-001 | /v1/embeddings                                   |
| text-moderation-stable  | /v1/moderations                                  |
| text-moderation-latest  | /v1/moderations                                  |

此列表不包括我们的[first-generation embedding](https://platform.openai.com/docs/guides/embeddings/similarity-embeddings) 模型以及 [DALL·E](https://platform.openai.com/docs/guides/images/image-generation-beta)模型.