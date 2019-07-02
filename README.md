## 中文 问答 / 聊天机器人

一个利用模板匹配、问题检索、联网搜索、以及答案生成等方法，完成问答交互的中文聊天机器人。

- #### 聊天效果
    <pre><code>
    >> 你好
    你好，你是哪位
    >> 我是你爸爸
    真不怕给自己折寿...
    >> 风寒感冒
    风寒感冒： 
    ·所属科室：疾病百科、中医科、中医综合 
    ·症状表现：小儿发烧后身上起红点、风寒咳嗽、喉部有痰、扁桃体发炎、流鼻涕、鼻塞、恶寒、外寒内热、背心冷、情绪性感冒 
    ·易患人群：无特发人群 
    ·传染途径：通过呼吸道传染 
    ·（可以通过 疾病名称+病因、预防等继续查询）
    >> 感冒的症状
    感冒的症状：头痛、浑身忽冷忽热、咽痛、发烧、咽喉干燥及灼热感、流鼻涕、鼻塞、发热伴寒战、情绪性感冒等
    >> 怎么预防感冒
    感冒的预防方法：本病全年皆可发病，冬春季节多发，可通过含有病毒的飞沫或被污染的用具传播，多数为散发性，但常在气候突变时流行，由于病毒的类型较多，人体对各种病毒感染后产生的免疫力较弱且短暂，并无交叉免疫，同时在健康人群中有病毒携带者，故一个人一年内可有多次发病。
    日常预防
    四种简易预防感冒的方法
    1、冷水洗脸、热水泡足法：每日晨、晚养成用冷水浴面、热水泡足的习惯，这有助于提高身体抗病能力。
    2、盐水漱口：每日早晚、餐后用淡盐水漱口，以清除口腔病菌。在流感流行的。
    3、食醋熏蒸法：把陈醋加热，关上门窗，隔一段时间在房间里熏蒸一次，可有效杀除感冒等病毒。
    4、饮用姜茶法：晚上睡觉前，用萝卜加醋熬汤，或以生姜、红糖适量煮水代茶饮，对防止感冒有很好的效果。
    专业指导
    1、补充维生素E、维生素C。维生素E、维生素C都能有效提高人体免疫力。
    2、保证足够的睡眠。数据显示，只睡半宿的人，免疫力会下降大约三成。而在睡足8小时后，免疫力会立刻恢复。
    3、进行鼻部按摩。大部分感冒中，鼻咽部是最初感染的部位，因此鼻部按摩能有效预防感冒。
    </code></pre>
    

- #### 项目介绍

    1. 模板匹配：利用 XML 文件配置相关人格等固定信息，根据正则表达式匹配答案
    2. 问题检索：利用倒排索引，用余弦相似度筛选答案；或根据知识图谱搜索问题
    3. 联网搜索：利用搜狗问问的搜索引擎对问题进行实时搜索
    4. 答案生成：利用 Seq2Seq 模型以及机器学习方法生成答案（尚未加入该模块）
    
    问答处理是层级线性的，处理流程如下：
    - 首先将问题送入模板匹配模块，匹配到后会从答案中随机返回。如果没有匹配到答案则送入下一模块
    - 进入检索模块，并根据问题相似度选出匹配问题的答案，如果没有匹配到则送入下一模块
    - 到联网搜索模块，意味着本地语料库没有匹配答案，则根据搜狗问问的接口搜索答案
    

- #### 代码组织结构
    
    - layer.py
        - 类 BaseLayer 基础类，父类
        - 类 Template 是模板匹配模块    
        - 类 CorpusSearch 是问题检索模块
        - 类 MedicalSearch 从医疗知识图谱中检索问题
        - 类 Generate 是答案生成模块（尚未添加功能）
        - 类 Internet 是联网搜索模块
    - factory.py 是处理模块，提供分词、提取关键字、清洗词句等功能
    - filter.py 是层级处理模块，主要控制各模块的顺序运行（添加 score.py 会加入 re-rank 机制）
    - chatbot.py 聊天机器人，拥有联网微信以及本地两种聊天模式
    - run.py 启动机器人


- #### 运行方法
    
    运行 run.py 文件<br>
    <pre><code>
    from chatbot import WXChatBot

    if __name__ == '__main__':
        bot = WXChatBot()
        
        # bot.inter_start()     #微信自动回复
        bot.local_start()   #本地使用   
      
    </code></pre>
   
- #### config 文件

    - default_answer.xml 配置了默认答案，当没有联网的情况下且未找到匹配答案时返回默认答案<br>
    需要注意的是，<robot_info>下的 tag 名称要与 <temp id='name'>的 id 值一致
    - robot_template.xml 配置了机器人的固定人格信息即相应回答
    - special_words.txt 配置了特殊不想被且分开的词汇
    - chat_log.txt 记录了运行中多有的对话
    - inverse_index.json 语料库的倒排索引文件
    - vocab_from_q.json 从语料库中提取出的词典，以 key-value 形式
    - word2vec.model 利用 gensim 进行 Word Embedding，训练得出的 word2vec 模型，

- #### 聊天数据

    保存在 corpus/conversation_text.txt 中<br>
    形如：
    <pre><code>
    你好
    你好，你是哪位
    在吗
    在，怎么了？
    </code></pre>
    
