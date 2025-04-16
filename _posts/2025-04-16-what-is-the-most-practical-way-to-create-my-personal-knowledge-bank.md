---
tags: AI
---

这几天我特别想做的一件事情，就是建立一个知识库，然后运用AI来进行访问。这是我个人的知识库，可以包含我知道的所有内容，然后通过AI访问，别人也能使用它。可以说，这个知识库就像是我的另一个化身。

从前天开始，我与AI的对话中，大家可能已经看出我想创建这样的知识库了。然而，怎样的方法能够最快地实现这个目标呢？尤其是在当前情况下，最有趣的是我至今还没有创建这个知识库，一切仅仅存在于我脑海中，显得十分混乱。我的知识库内容是一团乱麻，而作为开发者，我的代码能力也是一片混乱。所以，最近我一直被这个问题困扰着。


今天，我似乎找到了方案。对于开发者的我来说，secondme.io 似乎提供了所有代码，我要试一试。
对于项目管理者的我来说，GPT4.1似乎是个非常可行的工具

## 我问
GPT-4.1的发布意味我们终于有了一个价格便宜，同时有1M context window的模型。但是怎样最为合理地使用这个模型，说需要技巧和练习的。
我的手上有很多杂乱无章的信息，我希望GPT-4.1可以    1.阅读这些信息，发现其中的逻辑与关联，2 整理这些信息，3 输出的内容以以后建立知识库为目的，最好是这样的：“要拆解成一个个主题清晰、段落完整的文本块，一则方便后续索引，二则要让返回的内容读起来不至于散乱。可以根据主题、章节或常见问题类型做初步分类。每个分类下，再把长文拆成短一些的内容片段（如每段300~500字左右，或按照逻辑边界拆分），给它们贴上标签或主题关键词，方便检索”

为了达到以上目的，what is the best workflow, and what is the best prompt for each step

## Pro 答

以下是一套面对超大规模上下文（如 1M tokens）模型时，从“读取杂乱信息”到“产出可用于知识库的结构化内容”的推荐工作流程，以及针对每个步骤的示例 Prompt 范本。该流程可根据实际需求灵活增减或合并步骤，但核心思路是：先做粗分与主题抽取，再做细分与文本块切分，最后给出带标签（主题关键词）的内容，方便日后索引与再加工。

━━━━━━━━━━━━━━━━━━━━━━━━━
1. 数据预处理与分卷
━━━━━━━━━━━━━━━━━━━━━━━━━
• 目标：先对原始数据进行基本的预处理与分区，避免一次性在模型中塞入过多且无序的内容，即使模型有大容量上下文，也依旧保持“分步输入-分步处理-分步输出”的思路。  
• 操作要点：  
  – 如果你的资料还没有任何结构，可以先根据文件来源、数据格式、时间顺序或其他简单标准，将资料分割成若干“分卷”或“批次”。  
  – 每个分卷在不超出模型的最大上下文范围（1M tokens）时，尽量做到主题或来源相对集中。  
  – 尽量去除明显重复、无关、或低价值信息，以减少噪音。  

（本步骤不太需要复杂的 Prompt，因为更多是人工或工具对数据进行基础处理。）

━━━━━━━━━━━━━━━━━━━━━━━━━
2. 粗读概览与主题提炼
━━━━━━━━━━━━━━━━━━━━━━━━━
• 目标：让模型对预处理后每一批次的数据先进行“通读”，并给出一个主题或要点的初步列表。  
• 操作要点：  
  – 不要让模型在此时就输出大量细节，要它专注于全局性总结。  
  – 为后续更细的拆解与分类提供大方向。  

示例 Prompt：  
───────────────────────────────────
角色设定：System / 用户  
───────────────────────────────────
[System]  
你是一个善于从海量文本中提炼要点和主题的AI助理。你的首要目标是帮助用户对这批数据进行概览与主题分类。请按照要求行动。

[User]  
我将向你提供第一批（分卷）文本内容。请你：  
1. 先列出这批内容中最突出的主题、子主题或主要类别。  
2. 不要尝试输出所有细节；只要概括性的总结即可。  
3. 对每个主题，可给简短描述或关键要点。

以下是文本内容：  
「…（粘贴第一批文本）…」  

───────────────────────────────────

在这个阶段的输出示例：  
• 主题 A：用于描述与该主题相关的关键词、核心观点  
• 主题 B：用于描述与该主题相关的另一些关键词、核心观点  
…  

━━━━━━━━━━━━━━━━━━━━━━━━━
3. 深度分析与分章节汇总
━━━━━━━━━━━━━━━━━━━━━━━━━
• 目标：在已经识别出的主题框架下，深入查看每一份文本的逻辑、关联和细节，并做分章节或分块的汇总说明。  
• 操作要点：  
  – 可以要求模型“根据上一步的主题列表”逐段、逐条地分析。  
  – 将与该主题不相关的部分可标记为“可能需要排除或归类在其他主题”。  
  – 仍然无需一次性输出过长的文本，但要比粗读阶段更细致。  

示例 Prompt：  
───────────────────────────────────
角色设定：System / 用户  
───────────────────────────────────
[System]  
你是一个擅长信息结构化和多级归类的AI助手。你需要帮助用户将上一阶段识别的主题进一步细分、并为后续知识库建设作准备。

[User]  
1. 回顾上一步你给出的主题列表。现在请针对第一批数据，以这些主题为纲，每个主题下再根据逻辑将内容分成几个小节或小块，并提供详细的段落总结。  
2. 如果发现有与主题无关或冲突的信息，请注明并归入“待定”或“其他”。  
3. 输出时，尽量控制在每个块 300~500 字左右，或以逻辑段落为界。  
4. 在每个章节/段落开头，附上主题关键词或标签。  

请基于以下文本来执行操作：  
「…（重复或引用与之相关的文本，或直接让模型结合记忆与上一环节结果）…」  
───────────────────────────────────

输出示例可参考：  
• [标签/主题关键词] 段落内容1（300~500字以内）  
• [标签/主题关键词] 段落内容2（300~500字以内）  
…  

━━━━━━━━━━━━━━━━━━━━━━━━━
4. 细化与交叉校验
━━━━━━━━━━━━━━━━━━━━━━━━━
• 目标：在对不同主题的细分内容完成后，可能还需要校对、合并或拆分。并且如果有多个分卷，需要查看是否存在跨卷的重复与交叉。  
• 操作要点：  
  – 如需要，可以多次调用模型，让它帮你发现不同分卷或不同主题间的重叠、矛盾或可合并的部分。  
  – 若有需要，可额外让模型产出一个“交叉索引表”，注明这部分信息可连接到哪里。  

示例 Prompt（跨分卷交叉对比）：  
───────────────────────────────────
[System]  
你是一个能够进行多文档对比和交叉验证的AI工程师助手。

[User]  
我已经处理了多个分卷，得到了若干主题与对应拆分段落。现在请你：  
1. 找出在不同分卷中可能重复或相似的内容，并做简要对照。  
2. 如果有主题名称可以合并，请提出合并方案，以及合并后更合理的主题名称/关键词。  
3. 如果有互相矛盾的地方，也请列出并给出可能的解释或原因。  
───────────────────────────────────

━━━━━━━━━━━━━━━━━━━━━━━━━
5. 产出可供知识库使用的最终版本
━━━━━━━━━━━━━━━━━━━━━━━━━
• 目标：最终生成一个“主题清晰、段落完整”的知识库原料，每个段落都有标签或关键词，方便后续索引。  
• 操作要点：  
  – 要保证输出格式统一、易于程序化解析或导入知识库系统。（比如用 JSON / Markdown / CSV 等形式呈现）  
  – 再次提醒模型保持每段长度在合理范围内，既不碎片化到难理解，也不冗长到不利检索。  
  – 如果需要嵌入向量检索，可以先只关注文本本身的分段与标签，然后再跑嵌入。  

示例 Prompt：  
───────────────────────────────────
[System]  
你是一位知识库构建专家AI，请将已整理好的内容转换成可用于后续索引的最终版本，规格如下：  
1. 按照主题进行大分类，再在每个主题下顺序放置多个“内容块”。  
2. 每个内容块需包含：短标签（或关键词数组）/ 段落文本 / 原始数据来源（如可用）。  
3. 控制每块文本在 300~500 字左右，如有需要可再细分。  

[User]  
以下是已经分类和结构化的草稿内容，请你按照以上要求进行排版并输出。  
「…（引用或粘贴最终确认后的分类、总结等，或让模型基于记忆执行）…」  
───────────────────────────────────

输出示例（可用 JSON 或 Markdown 结构）：  
───────────────────────────────────
[
  {
    "theme": "主题A名称",
    "chunks": [
      {
        "label": "关键词1, 关键词2",
        "text": "这里是一段 300~500 字的真实内容",
        "source": "原始数据来源或分卷标识"
      },
      {
        "label": "关键词3",
        "text": "这里是另一段内容",
        "source": "原始数据来源或分卷标识"
      }
    ]
  },
  {
    "theme": "主题B名称",
    "chunks": [
      …
    ]
  }
]
───────────────────────────────────

━━━━━━━━━━━━━━━━━━━━━━━━━
总结：最佳调用 GPT-4.1（或其他大模型）的工作流程
━━━━━━━━━━━━━━━━━━━━━━━━━
• 第 1 步（数据预处理）：先人工或用简单脚本将数据做基础去重、分类或批次划分，保证不会超出模型的最大上下文，并减少噪音。  
• 第 2 步（粗读概览）：让模型进行高层次总结，抽取主题与关键点。  
• 第 3 步（深度分析与分章）：按主题深入挖掘内容、进行小段落拆分。  
• 第 4 步（细化与交叉校验）：校对跨分卷重复或矛盾点，必要时合并或修订主题。  
• 第 5 步（最终知识库结构输出）：给出分门别类、带标签的文本块，方便后续索引与知识库系统的接入。  

有了上述步骤和 Prompt 范例，你就能够较稳妥地将大量杂乱信息，循序渐进地交给 GPT-4.1 做结构化整理，最后得到便于管理与检索的结果。在实际使用中，可根据业务需求增减细节或对输出格式进行定制。通过“多轮分步-多Prompt迭代”的方式，让 GPT-4.1 在每一阶段都能专注于相对明确且可控的任务目标，既能提高准确度，也能有效利用其大容量上下文与强大推理能力。




## 我又问

我很喜欢这个work flow，你能不能把他做成一个或几个python code files so that I can follow this work flow easily


## pro 答

以下示例提供了一套基于 Python + OpenAI API 的多步骤脚本，用于实现前面所述的工作流。各步骤对应不同的函数与提示词 (Prompt)，你可以根据实际需求进行裁剪或组合。示例中假设你已经成功安装并配置了 openai 库，并且可以正常调用 GPT-4.1（或其它大模型，如 GPT-4）。

目录结构示例：
  
  gpt_workflow/
  ├── README.md
  ├── config.py
  ├── main.py
  ├── requirements.txt
  └── utils.py

下面给出几个关键文件的代码示例：  

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. requirements.txt
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
openai>=0.27.0  
tiktoken>=0.4.0  

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
2. config.py
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# 这里存放配置信息，如API Key、模型名称、默认超参数等
# 也可以通过环境变量实现更安全的管理

OPENAI_API_KEY = "YOUR_OPENAI_API_KEY"  # 最好用环境变量方式
# 假设模型名称，目前官方并没有 GPT-4.1 这一名称，仅作演示
MODEL_NAME = "gpt-4"


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
3. utils.py
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
import openai
import re
from config import OPENAI_API_KEY, MODEL_NAME

# 初始化OpenAI
openai.api_key = OPENAI_API_KEY

def call_gpt(messages, temperature=0.2, max_tokens=2000):
    """
    封装OpenAI ChatCompletion的调用。
    参数:
        messages (list): 聊天消息列表，每个元素为{"role": "system"/"user"/"assistant", "content": "..."}。
        temperature (float): 生成的随机度。
        max_tokens (int): 返回的最大token数量。
    返回:
        str: 模型返回的文本内容
    """
    response = openai.ChatCompletion.create(
        model=MODEL_NAME,
        messages=messages,
        temperature=temperature,
        max_tokens=max_tokens
    )
    return response.choices[0].message["content"]


def chunk_text(text, chunk_size=2000, overlap=200):
    """
    将长文本分块，以避免超过模型的上下文限制，并控制后续处理的粒度。
    参数:
        text (str): 需要分块的文本字符串。
        chunk_size (int): 单个文本块的最大长度(按字符或token预估)。
        overlap (int): 块与块之间的重叠字符数，可减少在分块边界损失上下文。
    返回:
        list: 分块后的文本列表，每个元素为字符串。
    """
    # 简单的按字符分片方法；你也可以使用 tiktoken 根据token数来分片
    start = 0
    chunks = []
    while start < len(text):
        end = start + chunk_size
        chunk = text[start:end]
        chunks.append(chunk)
        start += chunk_size - overlap
    return chunks


def step2_extract_themes(text_chunk):
    """
    第2步：粗读概览与主题提炼的Prompt函数。
    返回元信息（如主题列表、简要描述）以便后续深度分析。
    """
    system_msg = {
        "role": "system",
        "content": (
            "你是一个善于从海量文本中提炼要点和主题的AI助理。"
            "你的首要目标是帮助用户对这批数据进行概览与主题分类。"
        )
    }
    user_msg = {
        "role": "user",
        "content": (
            "下面是一段文本内容，请先列出其中最突出的主题、子主题或主要类别，并给出简要描述：

"
            + text_chunk
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result


def step3_deep_analysis(text_chunk, extracted_themes):
    """
    第3步：深入分析与分章节汇总，结合上一步的主题表。
    要求拆分成更细的段落并加上标签。
    """
    system_msg = {
        "role": "system",
        "content": (
            "你是一个擅长信息结构化和多级归类的AI助手，需要帮助用户将文本根据主题进行小段落拆分。"
        )
    }
    user_msg = {
        "role": "user",
        "content": (
            f"这些是前一步提炼的主题:
{extracted_themes}

"
            "现在，请根据这些主题为以下文本内容做深度分析，"
            "并将与每个主题相关的内容整理成若干小段落(300~500字左右)，每段前注明主题标签：

"
            + text_chunk
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result


def step4_cross_check(compare_list):
    """
    第4步：细化与交叉校验示例。
    compare_list: list，包含不同分卷或不同分析结果的文本，拿来交叉对比。
    返回一个可能的合并建议或重复冲突提示。
    """
    system_msg = {
        "role": "system",
        "content": "你是能够进行多文档对比和交叉验证的AI助手。"
    }
    user_msg = {
        "role": "user",
        "content": (
            "下面是来自不同分卷或阶段的结构化内容，请你找出可能重复或相似之处，"
            "若有可合并的主题，请提出合并方案；若有矛盾也请列出：

"
            + "

---

".join(compare_list)
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result


def step5_final_pack(organized_text):
    """
    第5步：最终生成可用于知识库的结构版本示例（JSON格式）。
    传入的是已经确认后的整理文本，或者可再让模型自己转换。
    """
    system_msg = {
        "role": "system",
        "content": (
            "你是一位知识库构建专家AI，请将已整理好的内容转换成可用于后续索引的最终版本，"
            "规格如下：
"
            "1. 按照主题进行大分类，再在每个主题下顺序放置多个“内容块”。
"
            "2. 每个内容块需包含：短标签（或关键词数组）/ 段落文本 / 原始数据来源（如可用）。
"
            "3. 控制每块文本在 300~500 字左右，如有需要可再细分。"
        )
    }
    user_msg = {
        "role": "user",
        "content": (
            "以下是结构化的草稿内容，请按上述要求输出成JSON或Markdown格式，"
            "便于后续索引或导入知识库系统：

" + organized_text
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
4. main.py
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
import os
from utils import (
    chunk_text,
    step2_extract_themes,
    step3_deep_analysis,
    step4_cross_check,
    step5_final_pack
)

def main():
    """
    演示一个可能的多步骤流程：
      1. 读取/预处理数据
      2. 粗读概览 -> 提炼主题
      3. 深度分析 -> 分章节汇总
      4. 细化与交叉校验
      5. 产出最终结构化文本
    """
    # =========================================================================
    # Step 1: 读取/预处理数据 (这里仅做示例，可自行改造成读取文件、数据库等)
    # =========================================================================
    text_data_1 = """这里是一段比较长的文本内容，代表第一批分卷数据..."""
    text_data_2 = """这里是第二批待处理的文本数据..."""

    # 简单去重、清洗等操作（这里略）

    # 分块处理，避免一次传入过长文本
    chunks_1 = chunk_text(text_data_1, chunk_size=1500, overlap=100)
    chunks_2 = chunk_text(text_data_2, chunk_size=1500, overlap=100)

    # =========================================================================
    # Step 2: 粗读概览 & 主题提炼 (以第一批内容为例)
    # =========================================================================
    extracted_themes_list_1 = []
    for chunk in chunks_1:
        # 只做示范：把每块的主题提炼合并
        result = step2_extract_themes(chunk)
        extracted_themes_list_1.append(result)

    # 将多个chunk的主题提炼结果合并成一个字符串或列表
    # 这里简单拼接
    extracted_themes_1 = "

".join(extracted_themes_list_1)

    # 可以重复对第二批数据做同样的操作
    extracted_themes_list_2 = []
    for chunk in chunks_2:
        result = step2_extract_themes(chunk)
        extracted_themes_list_2.append(result)
    extracted_themes_2 = "

".join(extracted_themes_list_2)

    # =========================================================================
    # Step 3: 深度分析 & 分章节整理
    # =========================================================================
    detailed_1_list = []
    for chunk in chunks_1:
        result = step3_deep_analysis(chunk, extracted_themes_1)
        detailed_1_list.append(result)
    # 合并
    detailed_1 = "

".join(detailed_1_list)

    # 同理对第二批进行
    detailed_2_list = []
    for chunk in chunks_2:
        result = step3_deep_analysis(chunk, extracted_themes_2)
        detailed_2_list.append(result)
    detailed_2 = "

".join(detailed_2_list)

    # =========================================================================
    # Step 4: 细化 & 跨批次交叉校验 (可选)
    # =========================================================================
    cross_check_result = step4_cross_check([detailed_1, detailed_2])
    # 可能拿这个结果再去改进主题名或合并相似段落，这里仅做示例

    # =========================================================================
    # Step 5: 最终产出可供知识库使用的版本
    # =========================================================================
    # 假设我们已经决定 detailed_1 + detailed_2 的合并版本作为最后成果
    final_structured = step5_final_pack(detailed_1 + "

---

" + detailed_2)

    # 输出结果
    print("===== Step 4: Cross Check Result =====")
    print(cross_check_result)
    print("
===== Step 5: Final Structured Output =====")
    print(final_structured)


if __name__ == "__main__":
    main()

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
5. 运行说明 (README.md 示例)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. 安装依赖:  
   pip install -r requirements.txt  

2. 设置OpenAI Key:  
   - 直接在 config.py 中替换  
   - 或者 export OPENAI_API_KEY='你的key'  (同时修改 config.py 以使用 os.environ.get("OPENAI_API_KEY") )  

3. 运行:  
   python main.py  

4. 自定义与扩展:  
   - 如果你有更多分卷文本，只需重复“分块处理 -> 主题提炼 -> 深度分析 -> 交叉校验”的流程。  
   - 如果需要更细的拆分策略，请自行修改 utils.py 里的 chunk_text()。  
   - 如果想要输出Markdown或写入文件，可在 main.py 里自行添加写文件逻辑。  

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
总结
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
通过以上示例，你可以较为便捷地运用 Python 脚本调用 GPT-4.1（或 GPT-4）实现前面所述的工作流程：  
1) 预处理 → 2) 粗读提炼主题 → 3) 深度分析拆分 → 4) 交叉校验 → 5) 打包输出可用于知识库的结构化文本。  

你可以随时根据业务需求，调整 chunk_text() 函数（如基于实际 token 数切分），以及对 Prompt（提示词）作进一步定制，以获得更符合你场景的整理结果。祝你一切顺利！




## I asked


for me, it is interesting to see Chinese in the code. even though they are in promt only. 
Please translate you above anwser into English completely, because most of time, I still need to work and present completely in english

##  pro answere

Below is a complete English translation of the previous answer, including all code examples. The workflow remains the same, but all comments, prompts, and explanations are now in English.

────────────────────────────────────────────────────────────────────────────
1. Directory Structure (example):
────────────────────────────────────────────────────────────────────────────
  
  gpt_workflow/
  ├── README.md
  ├── config.py
  ├── main.py
  ├── requirements.txt
  └── utils.py

────────────────────────────────────────────────────────────────────────────
2. requirements.txt
────────────────────────────────────────────────────────────────────────────
openai>=0.27.0  
tiktoken>=0.4.0  

────────────────────────────────────────────────────────────────────────────
3. config.py
────────────────────────────────────────────────────────────────────────────
# Store configuration information here, such as the API key, model name, default parameters, etc.
# For better security, you might want to fetch your API key from environment variables.

OPENAI_API_KEY = "YOUR_OPENAI_API_KEY"  # Preferably use an environment variable
# Example model name. In practice, GPT-4.1 may not be an official name; adapt as needed.
MODEL_NAME = "gpt-4"

────────────────────────────────────────────────────────────────────────────
4. utils.py
────────────────────────────────────────────────────────────────────────────
import openai
import re
from config import OPENAI_API_KEY, MODEL_NAME

# Initialize OpenAI
openai.api_key = OPENAI_API_KEY

def call_gpt(messages, temperature=0.2, max_tokens=2000):
    """
    Wrapper for making OpenAI ChatCompletion calls.
    Args:
        messages (list): A list of chat messages, each being a dict like 
                         {"role": "system"/"user"/"assistant", "content": "..."}.
        temperature (float): Sampling temperature.
        max_tokens (int): Maximum number of tokens to be generated in the response.
    Returns:
        str: The text content returned by the model.
    """
    response = openai.ChatCompletion.create(
        model=MODEL_NAME,
        messages=messages,
        temperature=temperature,
        max_tokens=max_tokens
    )
    return response.choices[0].message["content"]


def chunk_text(text, chunk_size=2000, overlap=200):
    """
    Splits a long text into smaller chunks to avoid exceeding the model's context limit 
    and to maintain more granular processing.
    Args:
        text (str): The text to be split.
        chunk_size (int): Maximum length of each chunk (in characters or approximate tokens).
        overlap (int): Number of overlapping characters between consecutive chunks 
                       to mitigate context loss at boundaries.
    Returns:
        list: A list of text chunks (strings).
    """
    # A simple character-based splitting approach; 
    # you could also use tiktoken to split by token count if desired.
    start = 0
    chunks = []
    while start < len(text):
        end = start + chunk_size
        chunk = text[start:end]
        chunks.append(chunk)
        start += chunk_size - overlap
    return chunks


def step2_extract_themes(text_chunk):
    """
    Step 2: Coarse reading (overview) and theme extraction.
    Returns meta-information (theme list, short descriptions) for subsequent deeper analysis.
    """
    system_msg = {
        "role": "system",
        "content": (
            "You are an AI assistant skilled at extracting main points and themes from large volumes of text. "
            "Your primary goal is to help the user obtain an overview of this batch of data and categorize it by theme."
        )
    }
    user_msg = {
        "role": "user",
        "content": (
            "Below is a text segment. Please list its most prominent themes, sub-themes, or main categories, "
            "and give a brief description for each:

"
            + text_chunk
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result


def step3_deep_analysis(text_chunk, extracted_themes):
    """
    Step 3: Deep analysis and chapter-wise summary, based on the extracted themes from the previous step.
    The goal is to split text into smaller, labeled paragraphs.
    """
    system_msg = {
        "role": "system",
        "content": (
            "You are an AI assistant adept at structuring information into multiple levels of categorization. "
            "You need to help the user organize their text by theme into smaller paragraphs."
        )
    }
    user_msg = {
        "role": "user",
        "content": (
            f"Here are the themes identified in the previous step:
{extracted_themes}

"
            "Now, please perform an in-depth analysis of the following text, and "
            "for each relevant theme, structure the content into several small paragraphs (approx. 300–500 words). "
            "Please prepend each paragraph with a theme label:

"
            + text_chunk
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result


def step4_cross_check(compare_list):
    """
    Step 4: Refinement and cross-checking example.
    Compare_list is a list of structured text from different batches or results; we compare them here.
    It returns potential suggestions for merging or notes about conflicts.
    """
    system_msg = {
        "role": "system",
        "content": "You are an AI assistant capable of multi-document comparison and cross-verification."
    }
    user_msg = {
        "role": "user",
        "content": (
            "Below is structured content from different batches or steps. Please find potentially duplicated or similar items. "
            "If there are themes that can be merged, propose how to merge them. "
            "If there are contradictions, please list them as well:

"
            + "

---

".join(compare_list)
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result


def step5_final_pack(organized_text):
    """
    Step 5: Produces the final version that can be used in a knowledge base, for example in JSON format.
    The input is the text that has been consolidated and approved, or you can let the model do the transformation itself.
    """
    system_msg = {
        "role": "system",
        "content": (
            "You are an AI expert in building knowledge bases. Please convert the organized text into a final version "
            "for subsequent indexing, with the following specifications:
"
            "1. Group by major themes, then list multiple 'content chunks' under each theme.
"
            "2. Each content chunk should include: a short label (or an array of keywords), the paragraph text, and the source of the original data (if applicable).
"
            "3. Keep each chunk at around 300–500 words; if needed, split further."
        )
    }
    user_msg = {
        "role": "user",
        "content": (
            "Below is the structured draft content. Please format it into JSON or Markdown for easy indexing "
            "or import into a knowledge base system:

" + organized_text
        )
    }
    result = call_gpt([system_msg, user_msg])
    return result

────────────────────────────────────────────────────────────────────────────
5. main.py
────────────────────────────────────────────────────────────────────────────
import os
from utils import (
    chunk_text,
    step2_extract_themes,
    step3_deep_analysis,
    step4_cross_check,
    step5_final_pack
)

def main():
    """
    Demonstration of a possible multi-step flow:
      1. Read/Preprocess data
      2. Coarse reading -> Extract themes
      3. Deep analysis -> Summarize in chapters/paragraphs
      4. Refinement & cross-check (optional multi-batch comparison)
      5. Output the final structured text for knowledge base usage
    """
    # =========================================================================
    # Step 1: Read/Preprocess data (simple example; modify to read from files, DB, etc.)
    # =========================================================================
    text_data_1 = """Here is a longer text representing the first batch of data..."""
    text_data_2 = """Here is another text representing the second batch of data..."""

    # Simple deduplication, cleaning, etc. (omitted)

    # Splitting into chunks to avoid sending overly long text in a single request
    chunks_1 = chunk_text(text_data_1, chunk_size=1500, overlap=100)
    chunks_2 = chunk_text(text_data_2, chunk_size=1500, overlap=100)

    # =========================================================================
    # Step 2: Coarse reading & theme extraction (example using the first batch)
    # =========================================================================
    extracted_themes_list_1 = []
    for chunk in chunks_1:
        result = step2_extract_themes(chunk)
        extracted_themes_list_1.append(result)

    # Merge the theme extraction results from multiple chunks (simple concatenation here)
    extracted_themes_1 = "
".join(extracted_themes_list_1)

    # Similarly for the second batch
    extracted_themes_list_2 = []
    for chunk in chunks_2:
        result = step2_extract_themes(chunk)
        extracted_themes_list_2.append(result)
    extracted_themes_2 = "
".join(extracted_themes_list_2)

    # =========================================================================
    # Step 3: Deep analysis & summarizing into chapters/paragraphs
    # =========================================================================
    detailed_1_list = []
    for chunk in chunks_1:
        result = step3_deep_analysis(chunk, extracted_themes_1)
        detailed_1_list.append(result)
    detailed_1 = "
".join(detailed_1_list)

    # Same for the second batch
    detailed_2_list = []
    for chunk in chunks_2:
        result = step3_deep_analysis(chunk, extracted_themes_2)
        detailed_2_list.append(result)
    detailed_2 = "
".join(detailed_2_list)

    # =========================================================================
    # Step 4: Refinement & cross-checking across batches (optional)
    # =========================================================================
    cross_check_result = step4_cross_check([detailed_1, detailed_2])
    # Based on cross-check_result, you can revise theme names or merge duplications as needed.

    # =========================================================================
    # Step 5: Produce the final version for knowledge base usage
    # =========================================================================
    # Suppose we've decided to combine detailed_1 + detailed_2 as our final result
    final_structured = step5_final_pack(detailed_1 + "

---

" + detailed_2)

    # Print results
    print("===== Step 4: Cross Check Result =====")
    print(cross_check_result)
    print("
===== Step 5: Final Structured Output =====")
    print(final_structured)


if __name__ == "__main__":
    main()

────────────────────────────────────────────────────────────────────────────
6. README.md (example)
────────────────────────────────────────────────────────────────────────────
1. Installing dependencies:  
   pip install -r requirements.txt  

2. Setting up the OpenAI Key:  
   - Directly replace in config.py  
   - Or export OPENAI_API_KEY='your_key_here' and modify config.py to use os.environ.get("OPENAI_API_KEY")  

3. Running the code:  
   python main.py  

4. Customizing and extending:  
   - If you have multiple batches of data, repeat the same “chunking -> theme extraction -> deep analysis -> cross-check” process for each.  
   - If you need finer granularity in splitting, adjust the chunk_text() function in utils.py (for example, using tiktoken to split by token count).  
   - If you want to output Markdown or write to a file, add the relevant logic to main.py.  

────────────────────────────────────────────────────────────────────────────
Summary
────────────────────────────────────────────────────────────────────────────
The above example shows how to invoke GPT-4.1 (or GPT-4) with a multi-step Python script to implement the previously described workflow:

1) Preprocess → 2) Overview & theme extraction → 3) Deep-dive & paragraph splitting → 4) Cross-check & refinement → 5) Output structured text for knowledge base.

You can adapt the code as needed for your specific business requirements, refining functions like chunk_text() or adjusting the prompts to produce more customized results. Good luck with your project!
