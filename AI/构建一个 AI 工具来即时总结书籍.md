原文：[Build an AI Tool to Summarize Books Instantly](https://levelup.gitconnected.com/build-an-ai-tool-to-summarize-books-instantly-828680c1ceb4)

---
> 译注：总体思想就是分而治之

# 构建一个 AI 来即时总结书籍

> 无需从头到尾阅读，即可掌握任何一本书的要点。

在本文中，我们将使用 Python、Langchain 和 OpenAI Embeddings 构建一个简单但功能强大的书籍摘要器。

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*DFqc1P6PnOZ8S95puZqSEg.png) ⬆️ 使用 DALL·E 3 生成。

# 挑战

像 GPT-3 和 GPT-4 这样的人工智能模型非常强大，但它们也有其局限性。一个重要的限制是上下文窗口，它限制了模型在任一时间可以考虑的文本量。这意味着，您不能只是将整本书输入到模型中就期望能够得到连贯的摘要。此外，处理大文本的成本可能会很高。

# 解决方案

为了克服这些挑战，我们设计了一种既经济又高效的方法。过程如下：

# 简化流程

以下是我们如何将一本完整的书转化为简明摘要的方法：

1. **分割和嵌入：**我们将书分解成更小的块，然后将它们转换为嵌入。这一步的成本令人惊讶。
2. **聚类：** 接着，我们对这些嵌入进行聚类，以找到书中最具代表性的部分。
3. **总结：** 然后，我们使用更具成本效益的 GPT-3.5 模型来总结这些关键部分。
4. **组合摘要：** 最后，我们使用 GPT-4 将这些摘要拼接成一个流畅的叙述。

通过仅在最后一步使用 GPT-4 来设法保持较低的成本。

现在，让我们分解代码和每个步骤背后的基本原理。构建摘要器

让我们深入研究代码并逐步构建我们的摘要器。

# 步骤一：加载书籍

首先，我们需要读取书本内容。我们将支持 PDF 和 EPUB 格式。


```py
import os
import tempfile
from langchain.document_loaders import PyPDFLoader, UnstructuredEPubLoader

def load_book(file_obj, file_extension):
    """Load the content of a book based on its file type."""
    text = ""
    with tempfile.NamedTemporaryFile(delete=False, suffix=file_extension) as temp_file:
        temp_file.write(file_obj.read())
        if file_extension == ".pdf":
            loader = PyPDFLoader(temp_file.name)
            pages = loader.load()
            text = "".join(page.page_content for page in pages)
        elif file_extension == ".epub":
            loader = UnstructuredEPubLoader(temp_file.name)
            data = loader.load()
            text = "\n".join(element.page_content for element in data)
        else:
            raise ValueError(f"Unsupported file extension: {file_extension}")
        os.remove(temp_file.name)
    text = text.replace('\t', ' ')
    return text
```

# 步骤二：分割和嵌入文本

AI 模型有令牌限制，这意味着它们不能一次处理一整本书。通过将文本分块，我们确保书中的每个部分都能够被喂给 AI。

我们将把文本分块并将其转换为嵌入。嵌入可以通过最少的计算快速将文本转换为紧凑的数字形式，从而使该过程既快速又经济高效。


```py
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.embeddings import OpenAIEmbeddings

def split_and_embed(text, openai_api_key):
    text_splitter = RecursiveCharacterTextSplitter(separators=["\n\n", "\n", "\t"], chunk_size=10000, chunk_overlap=3000)
    docs = text_splitter.create_documents([text])
    embeddings = OpenAIEmbeddings(openai_api_key=openai_api_key)
    vectors = embeddings.embed_documents([x.page_content for x in docs])
    return docs, vectors
```

# 步骤三：对嵌入进行聚类

我们使用 KMeans 聚类算法对相似的块进行分组。在我的版本中，正如您在下面所看到的，我发现对于大多数书籍来说，11 个簇就可以很好地工作。但您可以根据您的用例进行调整。

这里，我们将整本书分块，然后转化为嵌入。根据其相似性对这些嵌入进行分组。对于每个组，我们选择最具代表性的嵌入并将其映射回其相应的文本块。


```py
from sklearn.cluster import KMeans
import numpy as np

def cluster_embeddings(vectors, num_clusters):
    kmeans = KMeans(n_clusters=num_clusters, random_state=42).fit(vectors)
    closest_indices = [np.argmin(np.linalg.norm(vectors - center, axis=1)) for center in kmeans.cluster_centers_]
    return sorted(closest_indices)
```

# 步骤四：总结代表性块

我们将使用 GPT-3.5 来只总结那些选定的块。


```py
from langchain.chains.summarize import load_summarize_chain
from langchain.prompts import PromptTemplate

def summarize_chunks(docs, selected_indices, openai_api_key):
    llm3_turbo = ChatOpenAI(temperature=0, openai_api_key=openai_api_key, max_tokens=1000, model='gpt-3.5-turbo-16k')
    map_prompt = """
    You are provided with a passage from a book. Your task is to produce a comprehensive summary of this passage. Ensure accuracy and avoid adding any interpretations or extra details not present in the original text. The summary should be at least three paragraphs long and fully capture the essence of the passage.
    ```{text}```
    SUMMARY:
    """
    map_prompt_template = PromptTemplate(template=map_prompt, input_variables=["text"])
    selected_docs = [docs[i] for i in selected_indices]
    summary_list = []

    for doc in selected_docs:
        chunk_summary = load_summarize_chain(llm=llm3_turbo, chain_type="stuff", prompt=map_prompt_template).run([doc])
        summary_list.append(chunk_summary)
    
    return "\n".join(summary_list)
```

# 步骤五：创建最终摘要

我们使用 GPT-4 将各个摘要合并为一个有凝聚力的最终摘要。


```py
from langchain.schema import Document
from langchain.chat_models import ChatOpenAI

def create_final_summary(summaries, openai_api_key):
    llm4 = ChatOpenAI(temperature=0, openai_api_key=openai_api_key, max_tokens=3000, model='gpt-4', request_timeout=120)
    combine_prompt = """
    You are given a series of summarized sections from a book. Your task is to weave these summaries into a single, cohesive, and verbose summary. The reader should be able to understand the main events or points of the book from your summary. Ensure you retain the accuracy of the content and present it in a clear and engaging manner.
    ```{text}```
    COHESIVE SUMMARY:
    """
    combine_prompt_template = PromptTemplate(template=combine_prompt, input_variables=["text"])
    reduce_chain = load_summarize_chain(llm=llm4, chain_type="stuff", prompt=combine_prompt_template)
    final_summary = reduce_chain.run([Document(page_content=summaries)])
    return final_summary
```

# 将它们放在一起

现在，我们将所有步骤合并到一个函数中，该函数接受上传的文件并生成摘要。


```py
# ... (previous code for imports and functions)

def generate_summary(uploaded_file, openai_api_key, num_clusters=11, verbose=False):
    file_extension = os.path.splitext(uploaded_file.name)[1].lower()
    text = load_book(uploaded_file, file_extension)
    docs, vectors = split_and_embed(text, openai_api_key)
    selected_indices = cluster_embeddings(vectors, num_clusters)
    summaries = summarize_chunks(docs, selected_indices, openai_api_key)
    final_summary = create_final_summary(summaries, openai_api_key)
    return final_summary
```

# 测试总结器

最后，我们可以用一本书来测试我们的摘要器。


```py
# Testing the summarizer
if __name__ == '__main__':
    load_dotenv()
    openai_api_key = os.getenv('OPENAI_API_KEY')
    book_path = "path_to_your_book.epub"
    with open(book_path, 'rb') as uploaded_file:
        summary = generate_summary(uploaded_file, openai_api_key, verbose=True)
        print(summary)
```

# 总结

这个工具可以帮助你快速理解任何书籍的要点。我们采取的方法不仅成本低廉，而且适用于具有任何长度的书籍。

请记住，摘要的质量取决于聚类和摘要提示，因此请随意根据您的需要进行调整。

您可以尝试 Streamlit 应用程序并在此处查看摘要器：[GPT Summarizer App](https://gptsummarizer.streamlit.app/)。

我希望这篇文章对您有所帮助。如果您有任何问题或反馈，请发表评论或联系我们。

搬砖快乐！:)
