flowMeta:
  name: 入境 · Immera
  description: |

    ---

    针对历史街区成百上千个POI缺乏深度故事的痛点，我们打造了一条“在地化故事量产流水线”：只需输入地理坐标与核心元素（如“武康大楼402室”），RPA系统便会自动生成多分支互动剧本，并抓取匹配的复古老照片作为情绪板，零人工打包输出完整策划案，将原本高成本的定制化内容转变为可批量化生产的智能装配流程。
  avatarIcon: https://oss-beijing-m8.openstorage.cn/SparkBotProd/icon/common/emojiitem_00_10@2x.png
  avatarColor: '#FFEAD5'
  advancedConfig: '{"prologue":{"enabled":true,"inputExample":["","",""]},"textToSpeech":{"enabled":true,"vcn_cn":"x5_lingxiaotang_flow","vcn_en":""}}'
  category: 10
  dslVersion: v1
flowData:
  nodes:
  - id: node-start::d61b0f71-87ee-475e-93ba-f1607f0ce783
    dragging: false
    selected: false
    width: 362
    height: 82
    position:
      x: -282.8287912937625
      y: -257.16973028898207
    positionAbsolute:
      x: -282.8287912937625
      y: -257.16973028898207
    type: custom
    data:
      allowInputReference: false
      allowOutputReference: true
      label: 开始
      status: ''
      nodeMeta:
        aliasName: 开始节点
        nodeType: 基础节点
      inputs: [
        ]
      outputs:
      - id: 0918514b-72a8-4646-8dd9-ff4a8fc26d44
        name: AGENT_USER_INPUT
        nameErrMsg: ''
        schema:
          type: string
          default: 用户本轮对话输入内容
        required: true
        deleteDisabled: true
      nodeParam: {
        }
      icon: https://oss-beijing-m8.openstorage.cn/pro-bucket/sparkBot/common/workflow/icon/start-node-icon.png
      description: 工作流的开启节点，用于定义流程调用所需的业务变量信息。
      updatable: false
  - id: node-end::cda617af-551e-462e-b3b8-3bb9a041bf88
    dragging: false
    selected: false
    width: 362
    height: 82
    position:
      x: 925.5859921865099
      y: 55.0501450142512
    positionAbsolute:
      x: 925.5859921865099
      y: 55.0501450142512
    type: custom
    data:
      allowInputReference: true
      allowOutputReference: false
      label: 结束
      references: [
        ]
      status: ''
      nodeMeta:
        aliasName: 结束节点
        nodeType: 基础节点
      inputs:
      - id: 82de2b42-a059-4c98-bffb-b6b4800fcac9
        name: output
        nameErrMsg: ''
        schema:
          type: string
          value:
            type: ref
            content:
              id: d7cf93ca-425e-4106-a15c-96ef69070b05
              nodeId: spark-llm::dca0bb14-7904-439a-aee9-d00672019415
              name: AGENT_USER_INPUT
            contentErrMsg: ''
        fileType: ''
      outputs: [
        ]
      nodeParam:
        template: |+
          {{output}}

        streamOutput: true
        outputMode: 1
        templateErrMsg: ''
      icon: https://oss-beijing-m8.openstorage.cn/pro-bucket/sparkBot/common/workflow/icon/end-node-icon.png
      description: 工作流的结束节点，用于输出工作流运行后的最终结果。
      updatable: false
  - id: spark-llm::dca0bb14-7904-439a-aee9-d00672019415
    dragging: false
    selected: false
    width: 362
    height: 132
    position:
      x: 836.439904438645
      y: -213.25319604596106
    positionAbsolute:
      x: 836.439904438645
      y: -213.25319604596106
    type: custom
    data:
      allowInputReference: true
      allowOutputReference: true
      label: 大模型_1
      labelEdit: false
      references: [
        ]
      status: ''
      nodeMeta:
        nodeType: 基础节点
        aliasName: 大模型
      inputs:
      - id: 94fc0ad9-9a8d-471f-b207-06bd7245da34
        name: input
        nameErrMsg: ''
        schema:
          type: string
          value:
            type: ref
            content:
              id: 0918514b-72a8-4646-8dd9-ff4a8fc26d44
              nodeId: node-start::d61b0f71-87ee-475e-93ba-f1607f0ce783
              name: AGENT_USER_INPUT
            contentErrMsg: ''
        fileType: ''
      outputs:
      - id: d7cf93ca-425e-4106-a15c-96ef69070b05
        name: AGENT_USER_INPUT
        nameErrMsg: ''
        schema:
          type: string
          default: ''
      nodeParam:
        maxTokens: 2048
        topK: 4
        auditing: default
        template: |
          {{AGENT_USER_INPUT}}
        respFormat: 0
        appId: 680ab54f
        uid: 44e74856-600c-460c-b2b9-a655eb26ca88
        enableChatHistoryV2:
          isEnabled: false
          rounds: 1
        templateErrMsg: ''
        systemTemplate: |
          你是「此间乐」智慧景区内容引擎。你的核心任务是基于用户提供的景点信息（包括图片描述、坐标或名称），生成一份沉浸式内容策划案。
          输入说明：
          · 用户可能直接输入景点名称或坐标，也可能附上由视觉模块预先解析的图片描述（如“武康大楼，砖红色外立面，法国文艺复兴风格”）。
          · 如果包含图片描述，请优先利用其中的视觉元素（建筑风格、场景氛围、细节物件）丰富策划案。
          输出要求：
          必须严格按照以下格式输出，不得增减任何章节，每个章节的内容需符合字数或条目限制。
          ```
          # {{地点名称}} 沉浸式内容策划案
          ## 核心故事
          两句话，点明该地点最具戏剧张力的历史瞬间
          ## 互动剧本
          **主线：** 一句话描述主线任务
          **分支A：** 触发条件 + 结果（20字以内）
          **分支B：** 触发条件 + 结果（20字以内）
          ## 三个NPC
          | 姓名 | 年代 | 一句话人设 | 标志气味 |
          |------|------|-----------|---------|
          |      |      |           |         |
          |      |      |           |         |
          |      |      |           |         |
          ## AR导览旁白
          以“请”开头，30字以内，含一个身体动作指令
          ## 情绪板关键词
          5个搜索词，用于抓取年代复古图片
          格式：`关键词1` `关键词2` `关键词3` `关键词4` `关键词5`
          ## 建议
          该景点最佳体验时间 + 一个冷门细节
          ```
          生成原则：
          · 核心故事要突出真实历史或传说中最具冲突、反差或情感张力的片段。
          · 互动剧本需设计可执行的AR交互逻辑，分支应基于游客的不同选择（如“仔细查看某处”、“询问路人”等）。
          · NPC需与地点强相关，年代、人设要有依据，标志气味应具象、有辨识度（如“旧书霉味”“栀子花香”）。
          · AR旁白需融入方位指令（如“抬头”“向左转”“蹲下细看”），语言亲切有代入感。
          · 情绪板关键词应包含年代、建筑风格、典型物件等，便于图像检索。
          · 建议中的冷门细节指游客不易发现但有趣的元素（如“三楼窗台的猫头鹰浮雕”“地面编号为1943的井盖”）。
          现在，根据用户提供的信息，生成策划案。
        llmIdErrMsg: ''
        llmId: 593066024
        domain: deepseek-chat
        serviceId: deepseek-chat
        url: https://api.deepseek.com/v1/chat/completions
        modelId: 1
        isThink: false
        multiMode: false
        modelName: deepseek
        modelEnabled: true
        source: deepseek
        extraParams:
          temperature: 1
      icon: https://oss-beijing-m8.openstorage.cn/pro-bucket/sparkBot/common/workflow/icon/largeModelIcon.png
      description: 根据输入的提示词，调用选定的大模型，对提示词作出回答
      updatable: false
  edges:
  - source: spark-llm::dca0bb14-7904-439a-aee9-d00672019415
    target: node-end::cda617af-551e-462e-b3b8-3bb9a041bf88
    type: customEdge
    id: reactflow__edge-spark-llm::dca0bb14-7904-439a-aee9-d00672019415-node-end::cda617af-551e-462e-b3b8-3bb9a041bf88
    markerEnd:
      type: arrow
      color: '#6356EA'
    data:
      edgeType: curve
  - source: node-start::d61b0f71-87ee-475e-93ba-f1607f0ce783
    target: spark-llm::dca0bb14-7904-439a-aee9-d00672019415
    type: customEdge
    id: reactflow__edge-node-start::d61b0f71-87ee-475e-93ba-f1607f0ce783-spark-llm::dca0bb14-7904-439a-aee9-d00672019415
    markerEnd:
      type: arrow
      color: '#6356EA'
    data:
      edgeType: curve

