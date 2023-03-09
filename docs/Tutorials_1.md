# 如何构建一个可以回答你网站问题的人工智能

本教程介绍了一个抓取网站(本例中是OpenAI网站)的简单示例，使用[Embeddings API](https://platform.openai.com/docs/guides/embeddings)将抓取的页面转换为Embedding，然后创建一个基本的搜索功能，允许用户询问关于Embedding信息的问题。这旨在成为使用自定义知识库打造更复杂的应用程序的起点。

## 开始

一些Python和GitHub的基本知识对本教程很有帮助。在开始之前，请确保[你有一个OpenAI API key](https://platform.openai.com/docs/api-reference/introduction)并完成了[快速入门教程](https://platform.openai.com/docs/quickstart)。这将使您直观地了解如何充分发挥API的潜力。

使用OpenAI、Pandas、transformer、NumPy和其他流行的包时，Python被用作主要的编程语言。如果您在本教程中遇到任何问题，请在[OpenAI社区论坛](https://community.openai.com/)上提问。

从代码开始，在GitHub上克隆本教程的完整代码。或者，跟随教程并将每个部分复制到Jupyter notebook中，然后一步一步地运行代码，或者只是跟着阅读。避免任何问题的好方法是通过运行以下命令来设置一个新的虚拟环境并安装所需的包：

```
python -m venv env

source env/bin/activate

pip install -r requirements.txt
```

## 设置一个网络爬虫

本教程的主要重点是OpenAI API，所以如果你愿意，你可以跳过如何创建网络爬虫的上下文，只需要下载源代码。或者，你也可以展开下面的部分来完成抓取机制实现。

### 学习如何构建一个网络爬虫

![](https://cdn.openai.com/API/docs/images/tutorials/web-qa/DALL-E-coding-a-web-crawling-system-pixel-art.webp)

获取文本形式的数据是使用Embedding的第一步。本教程通过抓取OpenAI网站来创建一组新的数据，这种技术也可以用于您自己的公司或个人网站。[查看源代码](https://github.com/openai/openai-cookbook/tree/main/apps/web-crawl-q-and-a)

这个爬虫将从下面代码中传入的根URL开始，访问每个页面，找到更多其他的链接，并访问这些页面(只要它们具有相同的根域名)。首先，导入所需的包，设置基本URL，并定义一个HTMLParser类。

```
import requests
import re
import urllib.request
from bs4 import BeautifulSoup
from collections import deque
from html.parser import HTMLParser
from urllib.parse import urlparse
import os

# Regex pattern to match a URL
HTTP_URL_PATTERN = r'^http[s]*://.+'

domain = "openai.com" # <- put your domain to be crawled
full_url = "https://openai.com/" # <- put your domain to be crawled with https or http

# Create a class to parse the HTML and get the hyperlinks
class HyperlinkParser(HTMLParser):
    def __init__(self):
        super().__init__()
        # Create a list to store the hyperlinks
        self.hyperlinks = []

    # Override the HTMLParser's handle_starttag method to get the hyperlinks
    def handle_starttag(self, tag, attrs):
        attrs = dict(attrs)

        # If the tag is an anchor tag and it has an href attribute, add the href attribute to the list of hyperlinks
        if tag == "a" and "href" in attrs:
            self.hyperlinks.append(attrs["href"])
```

下一个函数以URL作为参数，打开URL并读取HTML内容。然后，它返回该页面上找到的所有超链接。

```
# Function to get the hyperlinks from a URL
def get_hyperlinks(url):
    
    # Try to open the URL and read the HTML
    try:
        # Open the URL and read the HTML
        with urllib.request.urlopen(url) as response:

            # If the response is not HTML, return an empty list
            if not response.info().get('Content-Type').startswith("text/html"):
                return []
            
            # Decode the HTML
            html = response.read().decode('utf-8')
    except Exception as e:
        print(e)
        return []

    # Create the HTML Parser and then Parse the HTML to get hyperlinks
    parser = HyperlinkParser()
    parser.feed(html)

    return parser.hyperlinks
```

我们的目标是遍历并只索引OpenAI域名下的内容。为此，需要一个调用get_hyperlinks函数过滤掉不属于指定域名的url。

```
# Function to get the hyperlinks from a URL that are within the same domain
def get_domain_hyperlinks(local_domain, url):
    clean_links = []
    for link in set(get_hyperlinks(url)):
        clean_link = None

        # If the link is a URL, check if it is within the same domain
        if re.search(HTTP_URL_PATTERN, link):
            # Parse the URL and check if the domain is the same
            url_obj = urlparse(link)
            if url_obj.netloc == local_domain:
                clean_link = link

        # If the link is not a URL, check if it is a relative link
        else:
            if link.startswith("/"):
                link = link[1:]
            elif link.startswith("#") or link.startswith("mailto:"):
                continue
            clean_link = "https://" + local_domain + "/" + link

        if clean_link is not None:
            if clean_link.endswith("/"):
                clean_link = clean_link[:-1]
            clean_links.append(clean_link)

    # Return the list of hyperlinks that are within the same domain
    return list(set(clean_links))
```

抓取功能是完成网页抓取任务的最后一步。它跟踪访问的url，以避免因为有些链接在站内的多个页面出现，导致爬虫重复访问同一页面。它还从没有HTML标记的页面中提取原始文本，并将文本内容写入属于该页的本地.txt文件。

```
def crawl(url):
    # Parse the URL and get the domain
    local_domain = urlparse(url).netloc

    # Create a queue to store the URLs to crawl
    queue = deque([url])

    # Create a set to store the URLs that have already been seen (no duplicates)
    seen = set([url])

    # Create a directory to store the text files
    if not os.path.exists("text/"):
            os.mkdir("text/")

    if not os.path.exists("text/"+local_domain+"/"):
            os.mkdir("text/" + local_domain + "/")

    # Create a directory to store the csv files
    if not os.path.exists("processed"):
            os.mkdir("processed")

    # While the queue is not empty, continue crawling
    while queue:

        # Get the next URL from the queue
        url = queue.pop()
        print(url) # for debugging and to see the progress

        # Save text from the url to a <url>.txt file
        with open('text/'+local_domain+'/'+url[8:].replace("/", "_") + ".txt", "w", encoding="UTF-8") as f:

            # Get the text from the URL using BeautifulSoup
            soup = BeautifulSoup(requests.get(url).text, "html.parser")

            # Get the text but remove the tags
            text = soup.get_text()

            # If the crawler gets to a page that requires JavaScript, it will stop the crawl
            if ("You need to enable JavaScript to run this app." in text):
                print("Unable to parse page " + url + " due to JavaScript being required")
            
            # Otherwise, write the text to the file in the text directory
            f.write(text)

        # Get the hyperlinks from the URL and add them to the queue
        for link in get_domain_hyperlinks(local_domain, url):
            if link not in seen:
                queue.append(link)
                seen.add(link)

crawl(full_url)
```

上面示例的最后一行运行了爬虫程序，它会遍历所有可访问的链接，并将这些页面转换为文本文件。这将需要几分钟的时间运行，具体取决于站点的大小和复杂性。

## 构建嵌入索引

![](https://cdn.openai.com/API/docs/images/tutorials/web-qa/DALL-E-woman-turning-a-stack-of-papers-into-numbers-pixel-art.webp)

CSV是存储Embeddings的通用格式。你可以在Python中使用这种格式，将原始文本文件(在文本目录中)转换为Pandas数据帧。Pandas是一个流行的开源库，可以帮助您处理表格数据(存储在行和列中的数据)。

空白行会使文本文件变得混乱，使它们更难处理。一个简单的函数可以删除这些行并整理文件。

```
def remove_newlines(serie):
    serie = serie.str.replace('\n', ' ')
    serie = serie.str.replace('\n', ' ')
    serie = serie.str.replace('  ', ' ')
    serie = serie.str.replace('  ', ' ')
    return serie
```

将文本转换为CSV需要遍历前面创建的文本目录中的文本文件。打开每个文件后，删除额外的空格，并将修改后的文本添加到列表中。然后，将表格中的文本添加到空白的Pandas数据帧中，并将数据帧写入CSV文件。

```
额外的间距和新行会使文本变得混乱，并使Embeddings过程复杂化。这里使用的代码
有助于删除其中一些字符，但您可能会发现第三方库或其他方法可以删除更多不必要的
字符。
```

```
import pandas as pd

# Create a list to store the text files
texts=[]

# Get all the text files in the text directory
for file in os.listdir("text/" + domain + "/"):

    # Open the file and read the text
    with open("text/" + domain + "/" + file, "r", encoding="UTF-8") as f:
        text = f.read()

        # Omit the first 11 lines and the last 4 lines, then replace -, _, and #update with spaces.
        texts.append((file[11:-4].replace('-',' ').replace('_', ' ').replace('#update',''), text))

# Create a dataframe from the list of texts
df = pd.DataFrame(texts, columns = ['fname', 'text'])

# Set the text column to be the raw text with the newlines removed
df['text'] = df.fname + ". " + remove_newlines(df.text)
df.to_csv('processed/scraped.csv')
df.head()
```

Tokenization是将原始文本保存到CSV文件后的下一步。这个过程通过分解句子和单词将输入文本分割成token。通过查看文档中的[Tokenizer](https://platform.openai.com/tokenizer)，可以看到这方面的可视化演示。

```
一个有用的经验法则是，对于普通的英语文本，一个标记通常对应大约4个文本字符。
这大约相当于一个单词的¾(所以100个符号~= 75个单词)。
```

API对Embeddings的输入token的最大数量有限制。为了保持在限制以下，CSV文件中的文本需要被分解成多行。每一行的现有长度需要先被记录下来，以确定需要分割哪些行。

```
import tiktoken

# Load the cl100k_base tokenizer which is designed to work with the ada-002 model
tokenizer = tiktoken.get_encoding("cl100k_base")

df = pd.read_csv('processed/scraped.csv', index_col=0)
df.columns = ['title', 'text']

# Tokenize the text and save the number of tokens to a new column
df['n_tokens'] = df.text.apply(lambda x: len(tokenizer.encode(x)))

# Visualize the distribution of the number of tokens per row using a histogram
df.n_tokens.hist()
```

![](https://cdn.openai.com/API/docs/images/tutorials/web-qa/embeddings-initial-histrogram.png) 
最新的Embedding模型可以处理多达8191个token的输入，因此大多数行不需要任何分块操作，但可能不是每个被抓取的子页都是这种情况，因此下一个代码块将较长的行分割成较小的块。

```
max_tokens = 500

# Function to split the text into chunks of a maximum number of tokens
def split_into_many(text, max_tokens = max_tokens):

    # Split the text into sentences
    sentences = text.split('. ')

    # Get the number of tokens for each sentence
    n_tokens = [len(tokenizer.encode(" " + sentence)) for sentence in sentences]
    
    chunks = []
    tokens_so_far = 0
    chunk = []

    # Loop through the sentences and tokens joined together in a tuple
    for sentence, token in zip(sentences, n_tokens):

        # If the number of tokens so far plus the number of tokens in the current sentence is greater 
        # than the max number of tokens, then add the chunk to the list of chunks and reset
        # the chunk and tokens so far
        if tokens_so_far + token > max_tokens:
            chunks.append(". ".join(chunk) + ".")
            chunk = []
            tokens_so_far = 0

        # If the number of tokens in the current sentence is greater than the max number of 
        # tokens, go to the next sentence
        if token > max_tokens:
            continue

        # Otherwise, add the sentence to the chunk and add the number of tokens to the total
        chunk.append(sentence)
        tokens_so_far += token + 1

    return chunks
    

shortened = []

# Loop through the dataframe
for row in df.iterrows():

    # If the text is None, go to the next row
    if row[1]['text'] is None:
        continue

    # If the number of tokens is greater than the max number of tokens, split the text into chunks
    if row[1]['n_tokens'] > max_tokens:
        shortened += split_into_many(row[1]['text'])
    
    # Otherwise, add the text to the list of shortened texts
    else:
        shortened.append( row[1]['text'] )
```

再次可视化更新后的直方图可以帮助确认行是否被成功分割为较短的部分。

```
df = pd.DataFrame(shortened, columns = ['text'])
df['n_tokens'] = df.text.apply(lambda x: len(tokenizer.encode(x)))
df.n_tokens.hist()
```

![](https://cdn.openai.com/API/docs/images/tutorials/web-qa/embeddings-tokenized-output.png)

内容现在被分解成更小的块，一个简单的请求可以被发送到OpenAI API，指定使用新的text-embedding-ada-002模型来创建Embedding：

```
import openai

df['embeddings'] = df.text.apply(lambda x: openai.Embedding.create(input=x, engine='text-embedding-ada-002')['data'][0]['embedding'])

df.to_csv('processed/embeddings.csv')
df.head()
```

这应该需要大约3-5分钟，但之后你将有你自己的随时可用的Embedding!

<br>

## 用你的Embedding构建一个问答系统

![](https://cdn.openai.com/API/docs/images/tutorials/web-qa/DALL-E-friendly-robot-question-and-answer-system-pixel-art.webp)

Embedding已经准备就绪，这个过程的最后一步是创建一个简单的问答系统。它将获取用户的问题，为问题创建Embedding，并将其与现有Embedding进行比较，以从抓取的网站内容中检索出最相关的文本。然后，text-davinci-003模型将根据检索到的文本生成一个听起来自然的答案。

首先将Embedding转换为NumPy数组，这将使我们能更灵活的使用它，因为有许多可用的函数可以操作NumPy数组。它还将把维度平展为1-D，这是许多后续操作所需的格式。

```
import numpy as np
from openai.embeddings_utils import distances_from_embeddings

df=pd.read_csv('processed/embeddings.csv', index_col=0)
df['embeddings'] = df['embeddings'].apply(eval).apply(np.array)

df.head()
```

现在数据已经准备好了，需要将问题转换为使用简单函数的Embedding。这很重要，因为使用Embedding的搜索，通过余弦距离来比较数字向量的大小(这是原始文本的转换)。如果几个向量在余弦距离上很接近，那么它们很可能是相关的，就可能是问题的答案。OpenAI python包有一个内置的distances_from_embeddings函数，可以在这里使用。

```
def create_context(
    question, df, max_len=1800, size="ada"
):
    """
    Create a context for a question by finding the most similar context from the dataframe
    """

    # Get the embeddings for the question
    q_embeddings = openai.Embedding.create(input=question, engine='text-embedding-ada-002')['data'][0]['embedding']

    # Get the distances from the embeddings
    df['distances'] = distances_from_embeddings(q_embeddings, df['embeddings'].values, distance_metric='cosine')


    returns = []
    cur_len = 0

    # Sort by distance and add the text to the context until the context is too long
    for i, row in df.sort_values('distances', ascending=True).iterrows():
        
        # Add the length of the text to the current length
        cur_len += row['n_tokens'] + 4
        
        # If the context is too long, break
        if cur_len > max_len:
            break
        
        # Else add it to the text that is being returned
        returns.append(row["text"])

    # Return the context
    return "\n\n###\n\n".join(returns)
```

文本被分解成更小的token集合，因此按升序循环并继续添加文本是确保完整答案的关键步骤。如果返回的内容多于预期，max_len也可以修改为更小的值。

前一步只检索了语义上与问题相关的文本块，因此它们可能包含答案，但不能保证一定有答案。通过返回前5个最有可能的结果，可以进一步增加找到答案的机会。

然后，回答prompt将尝试从检索到的上下文中提取相关事实，以形成一个连贯的答案。如果没有相关的答案，prompt将返回“我不知道”。

可以使用text-davinci-003的completion终端，为问题创建听起来更真实的答案。

```
def answer_question(
    df,
    model="text-davinci-003",
    question="Am I allowed to publish model outputs to Twitter, without a human review?",
    max_len=1800,
    size="ada",
    debug=False,
    max_tokens=150,
    stop_sequence=None
):
    """
    Answer a question based on the most similar context from the dataframe texts
    """
    context = create_context(
        question,
        df,
        max_len=max_len,
        size=size,
    )
    # If debug, print the raw model response
    if debug:
        print("Context:\n" + context)
        print("\n\n")

    try:
        # Create a completions using the question and context
        response = openai.Completion.create(
            prompt=f"Answer the question based on the context below, and if the question can't be answered based on the context, say "I don't know"\n\nContext: {context}\n\n---\n\nQuestion: {question}\nAnswer:",
            temperature=0,
            max_tokens=max_tokens,
            top_p=1,
            frequency_penalty=0,
            presence_penalty=0,
            stop=stop_sequence,
            model=model,
        )
        return response["choices"][0]["text"].strip()
    except Exception as e:
        print(e)
        return ""
```

完成了！掌握了OpenAI网站上相关知识的问答系统现在已经准备就绪。现在可以做一些快速测试来查看输出的质量：

```
answer_question(df, question="今天星期几", debug=False)

answer_question(df, question="最新的embeddings模型是哪个?")

answer_question(df, question="ChatGPT是什么?")
```

回复会像下面这样:

```
"我不知道."

'最新的embeddings模型是text-embedding-ada-002.'

'ChatGPT是一个以对话方式进行交互的模型。它能够回答随机问题，承认自己的错误，质疑不正确的说法，并拒绝不适当的要求。'
```

如果系统不能回答预期的问题，那么就有必要搜索原始文本文件，看看预期的信息是否最终被embedded了。最初完成的抓取过程被设置为跳过初始域名之外的所有站点，因此如果有子域名设置，它可能不知道这些信息。

目前，每次回答一个问题都要传递数据帧。对于更多的生产工作流程，应该使用[矢量数据库](https://platform.openai.com/docs/guides/embeddings/how-can-i-retrieve-k-nearest-embedding-vectors-quickly)解决方案，而不是将embeddings存储在CSV文件中，但目前的方法是一个很好的原型选择。