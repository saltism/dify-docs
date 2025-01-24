# 维护知识库内文档

## 管理知识库中的文档

### 添加文档

知识库是文档的集合。文档支持本地上传，或导入其它在线数据源。知识库内的文档对应数据源中的一个文件单位，例如 Notion 库内的一篇文档或新的在线文档网页。

点击“知识库” → “文档列表” → “添加文件”，在已创建的知识库内上传新的文档。

<figure><img src="https://assets-docs.dify.ai/2024/12/424ab491aaebe09b490a36d26c9fa8da.png" alt=""><figcaption><p>在知识库内上传新文档</p></figcaption></figure>

### 启用 / 禁用 / 归档 / 删除文档

**启用：**&#x5904;于正常使用状态的文档，支持编辑内容与被知识库检索。对于已被禁用的文档，允许重新启用。已归档的文档需撤销归档状态后才能重新启用。

**禁用**：对于不希望在使用 AI 应用时被检索的文档，可以关闭文档右侧的蓝色开关按钮以禁用文档。禁用文档后，仍然可以编辑当前内容。

**归档**：对于一些不再使用的旧文档数据，如果不想删除可以将其归档。归档后的数据就只能查看或删除，无法重新编辑。你可以在知识库文档列表，点击归档按钮；或在文档详情页内进行归档。**归档操作支持撤销。**

**删除**：⚠️ 危险操作。对于一些错误文档或明显有歧义的内容，可以点击文档右侧菜单按钮中的删除。删除后的内容将无法被找回，请进行谨慎操作。

> 以上选项均支持选中多个文档后批量操作。

<figure><img src="https://assets-docs.dify.ai/2024/12/5e0e64859a1ac51602d167ec55ef9350.png" alt=""><figcaption><p>禁用或归档文档</p></figcaption></figure>

**注意：**

如果你的知识库中有部分文档长时间未更新或未检索时，为了确保知识库的高效运行，系统会暂时禁用这部分不活跃的文档。

* 对于 Sandbox/Free 版本用户，未使用知识库的将在 **7 天**后自动禁用；
* 对于 Professional/Team 版本用户，未使用知识库的将在 **30 天**后自动禁用。

你随时可以前往知识库中重新启用它们以恢复正常使用。付费用户可以使&#x7528;**“一键恢复”**&#x529F;能快速启用所有被禁用的文档。

![一键恢复被禁用的文档](https://assets-docs.dify.ai/2024/12/bf6485b17aec716741eb65e307c2274c.png)

***

## 管理文本分段

### 查看文本分段

知识库内已上传的每个文档都会以文本分段（Chunks）形式进行存储。点击文档标题，在详情页中查看当前文档的分段列表，每页默认展示 10 个区块，你可以在网页底部调整每页的展示数量。

每个内容区块展示前 2 行的预览内容。若需要查看更加分段内的完整内容，轻点“展开分段”按钮即可查看。

<figure><img src="https://assets-docs.dify.ai/2024/12/86cc80f17fab1eea75aa73ee681e4663.png" alt=""><figcaption><p>展开内容分段</p></figcaption></figure>

你可以通过筛选栏快速查看所有已启用 / 未启用的文档。

![筛选文档分段](https://assets-docs.dify.ai/2025/01/47ef07319175a102bfd1692dcc6cac9b.png)

不同的[文本分段模式](../create-knowledge-and-upload-documents/chunking-and-cleaning-text.md)对应不同的文本分段查看方式：

{% tabs %}
{% tab title="通用模式" %}
**通用模式**

[通用模式](../create-knowledge-and-upload-documents/#tong-yong)下的文本分段为独立的区块。若希望查看区块内的完整内容，轻点右上角的全屏 icon 进入全屏阅读模式。

![进入全屏阅读模式](https://assets-docs.dify.ai/2024/12/c37a1a247092cda9433a10243543698f.png)

点击顶部文档标题即可快速切换至当前知识库内的其它文档。

![通用模式-内容分段](https://assets-docs.dify.ai/2024/12/4422286c6d254e13c1ab59b147f0ffbf.png)
{% endtab %}

{% tab title="父子模式" %}
**父子模式**

[父子模式](../create-knowledge-and-upload-documents/#fu-zi-fen-duan)下的内容分为父分段和子分段。

*   **父分段**

    选择知识库内的文档后，你将会首先看到父分段的内容。父分段存&#x5728;**“段落”**&#x5206;段&#x4E0E;**“全文”**&#x5206;段两种模式，提供更加完整的上下文信息。下图为不同分段模式的文本预览差异。

<figure><img src="https://assets-docs.dify.ai/2024/12/b3961da2536dc922496ef6646315b9f4.png" alt=""><figcaption><p>段落与全文的预览差异</p></figcaption></figure>

*   **子分段**

    子分段一般为段落中的某个句子（较小的文本块），包含细节信息。各个分块均会展示字符数以及被检索召回的次数。轻点“子分段”即可查看更多详细内容。若希望查看区块内的完整内容，轻点区块右上角的全屏 icon 进入全屏阅读模式。

![父子模式-内容分段](https://assets-docs.dify.ai/2024/12/c0776f91e155bb1c961ae255bb98f39e.png)
{% endtab %}

{% tab title="Q&A 模式（仅用于社区版）" %}
**Q\&A 模式**

在 Q\&A 模式下，一个内容区块包含问题与答案，轻点任意文档标题即可查看文本分段。

![ Q\&A 模式 - 查看文本分段](https://assets-docs.dify.ai/2024/12/98e2486f6c5e06b4ece1b81d078afa08.png)
{% endtab %}
{% endtabs %}

***

### 检查分段质量

文档分段对于知识库应用的问答效果有明显影响，在将知识库与应用关联之前，建议人工检查分段质量。

通过字符长度、标识符或者 NLP 语义分段等机器自动化的分段方式虽然能够显著减少大规模文本分段的工作量，但分段质量与不同文档格式的文本结构、前后文的语义联系都有关系，通过人工检查和订正可以有效弥补机器分段在语义识别方面的缺点。

检查分段质量时，一般需要关注以下几种情况：

* **过短的文本分段**，导致语义缺失；

<figure><img src="https://assets-docs.dify.ai/2024/12/ee081e98c1649aea4a5c2b15b88e11aa.png" alt=""><figcaption><p>过短的文本分段</p></figcaption></figure>

* **过长的文本分段**，导致语义噪音影响匹配准确性；

<figure><img src="https://assets-docs.dify.ai/2024/12/ac47381ae4be183768dd025c37c049fa.png" alt=""><figcaption><p>过长的文本分段</p></figcaption></figure>

* **明显的语义截断**，在使用最大分段长度限制时会出现强制性的语义截断，导致召回时缺失内容；

<figure><img src="https://assets-docs.dify.ai/2024/12/b8ab7ac84028b0b16c3948f35015e069.png" alt=""><figcaption><p>明显的语义截断</p></figcaption></figure>

***

### 添加文本分段

知识库中的文档支持单独添加文本分段，不同的分段模式对应不同的分段添加方法。

> 添加文本分段为付费功能，请前往[此处](https://dify.ai/pricing)升级账号以使用功能。

{% tabs %}
{% tab title="通用模式" %}
**通用模式**

点击分段列表顶部的 “添加分段” 按钮，可以在文档内自行添加一个或批量添加多个自定义分段。

![通用模式 - 添加分段](https://assets-docs.dify.ai/2024/12/552ff4ab9e77130ad09aaef878b19cc9.png)

手动添加文本分段时，你可以选择添加正文和关键词。内容填写后，勾选尾部 **“连续新增”** 钮后，可以继续添加文本。

<figure><img src="https://assets-docs.dify.ai/2024/12/cd769622bc1d85c037277ef6fa5247c9.png" alt=""><figcaption><p>通用模式 - 添加文本分段</p></figcaption></figure>

批量添加分段时，你需要先下载 CSV 格式的分段上传模板，并按照模板格式在 Excel 内编辑所有的分段内容，再将 CSV 文件保存后上传。

<figure><img src="https://assets-docs.dify.ai/2024/12/5e501dd8efba02ff31d2e739417ce864.png" alt=""><figcaption><p>通用模式 - 批量添加自定义分段</p></figcaption></figure>
{% endtab %}

{% tab title="父子模式" %}
**父子模式**

点击分段列表顶部的 「 添加分段 」 按钮，可以在文档内自行添加一个或批量添加多个自定义**父分段。**

<figure><img src="https://assets-docs.dify.ai/2024/12/ed4be3bf178e3a41d53bcc10255ad3b2.png" alt=""><figcaption><p>父子模式 — 添加区块</p></figcaption></figure>

填写内容后，勾选尾部 **“连续新增”** 钮后，可以继续添加文本。

<figure><img src="https://assets-docs.dify.ai/2024/12/ba64232eea364b68f2e38341eb9cf5c1.png" alt=""><figcaption><p>父子模式 - 添加内容区块</p></figcaption></figure>

支持在父分段内单独添加子分段。轻点父分段内子分段右侧的“添加”，即可单独添加子分段。

<figure><img src="https://assets-docs.dify.ai/2024/12/23f68a369eb9c1a2cc9022b99a08341d.png" alt=""><figcaption><p>父子模式 — 添加子分段</p></figcaption></figure>
{% endtab %}

{% tab title="Q&A 模式（仅用于社区版）" %}
**Q\&A 模式**

点击分段列表顶部的 「 添加分段 」 按钮，可以在文档内自行添加一个或批量添加多个问题-答案内容对区块。
{% endtab %}
{% endtabs %}

***

### 编辑文本分段

{% tabs %}
{% tab title="通用模式" %}
**通用模式**

你可以对已添加的分段内容直接进行编辑或修改，包括修改分段内的**文本内容或关键词。**

为避免遗忘导致的重复编辑，编辑后内容区块将出现“已编辑”标签提示。

<figure><img src="https://assets-docs.dify.ai/2024/12/8220e412e4c5a2bf729fb5dfcc1b7f4c.png" alt=""><figcaption><p>编辑文档分段</p></figcaption></figure>
{% endtab %}

{% tab title="父子模式" %}
**父子模式**

父分段包含其本身所包含的子分段内容，两者相互独立。你可以单独修改父分段或子分段的内容。下图为修改父子分段间的流程说明：

![修改父子分段原理图](https://assets-docs.dify.ai/2024/12/aacdb2e95b9b7c0265455caaf0f1f55f.png)

**修改父分段**：轻点父分段右侧的编辑按钮，填写内容。点&#x51FB;**“保存”**&#x540E;将不会影响子分段的内容。如需重新生成子分段内容，轻&#x70B9;**“保存并重新生成子分段”**。

为避免遗忘导致的重复编辑，编辑后内容区块将出现“已编辑”标签提示。

![父子模式 - 修改父分段](https://assets-docs.dify.ai/2024/12/7eedfee59a3c978cc4a29d9cf06fbbcc.png)

**修改子分段：**&#x9009;择任意子分段后进入编辑模式，修改完成后即可保存。修改后不会影响父分段中的内容。被编辑过或新增的子分段区块会被打上 `C-NUMBER-EDITED`的深蓝色标签。

你也可以将子段视作当前父文本块的标签。

![父子模式 - 修改子分段](https://assets-docs.dify.ai/2024/12/a59563614d8f4661ebfb20f6b646b4ea.png)
{% endtab %}

{% tab title="Q&A 模式（仅适用于社区版）" %}
**Q\&A 模式**

在 Q\&A 分段模式下，一个内容区块包含问题与答案。点击需要编辑的文本分段，可以分别对问题和答案内容做出修改；同时也支持修改当前区块的关键词。

![Q\&A 模式 - 修改文本分段](https://assets-docs.dify.ai/2024/12/5c69adc0d4ec470d0677e67a4dd894a1.png)
{% endtab %}
{% endtabs %}

### 修改已上传文档的文本分段

已创建的知识库支持重新配置文档分段。

**较大分段**
- 可在单个分段内保留更多上下文，适合需要处理复杂或上下文相关任务的场景。
- 分段数量减少，从而降低处理时间和存储需求。

**较小分段**
- 提供更高的粒度，适合精确提取或总结文本内容。
- 减少超出模型 token 限制的风险，更适配限制严格的模型。

你可以访问 **分段设置**，点击 **保存并处理** 按钮以保存对分段设置的修改，并重新触发当前文档的分段流程。
当你保存设置并完成嵌入处理后，文档的分段列表将自动更新，无需手动刷新页面。

![Chunk Settings](https://assets-docs.dify.ai/2025/01/35c287a11a7db4f7f1974e9080d86169.png)

![Save & Process](https://assets-docs.dify.ai/2025/01/43a05344a425123f0db1c31a9c92bed6.png)

***

### 元数据管理

除了用于标记不同来源文档的元数据信息，例如网页数据的标题、网址、关键词、描述等。元数据将被用于知识库的分段召回过程中，作为结构化字段参与召回过滤或者显示引用来源。

<figure><img src="https://assets-docs.dify.ai/2024/12/f3b1ff4b559ebc40f18b8980b3719fe8.png" alt=""><figcaption><p>元数据管理</p></figcaption></figure>
