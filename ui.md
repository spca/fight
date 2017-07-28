## uiManager
    std::vector<std::string> aniFileNames_; 存放动画名
    std::vector<std::vector<std::vector<int16_t>>> blocksDataVec_; 存放ui的基本信息通过formName，BlockName，FormDataType 取得基本信息数值
    std::list<BlockTreeData> allBlockTreeData_;  存放所有ui节点树
    std::map<std::string, BlockTreeData*> formBlockTreeDataMap_; 按formName排好节点树
    std::map<std::string, std::map<std::string, UIDataIndex>> uiDataIndexFindMap_;  存放 form，block 的index
    std::vector<UISourceControlType> uiSourceControlTypeVec_; 存放ui控件的名字，及其包含的属性类型。

    std::map<std::string, std::map<std::string, UISourceControlData>> uiSourceControlDataMap_; 存放获取控件属性的UISourceControlData ，index在UISourceControlData的数组存放
    // 以下存放控件的属性值，通过index取得
    std::map<int32_t, int32_t> controlDataIntMap_;           
	std::map<int32_t, uint32_t> controlDataUIntMap_;
	std::map<int32_t, float> controlDataFloatMap_;
	std::map<int32_t, std::string> controlDataStringMap_;
### initUIDataWithFile(string& filePath)  filePath = "UI/uiform.bin"
    
    读取文件，初始化 aniFileNames_ ，blocksDataVec_ ， allBlockTreeData_ ， formBlockTreeDataMap_ ，uiDataIndexFindMap_

### initUIControlDataWithFile(string& filePath)  filePath = "UI/uicontrol.bin" 

    读取文件 ,初始化 controlDataIntMap_ , controlDataUIntMap_ , controlDataFloatMap_,controlDataStringMap_



### createFormByName(const std::string& formName, bool runEnterAction)


gameUI


## UICustomBase 自定义介入UI控件基类

### initWith(CustomControlCreateFuncParameters&& paras)






## ScriptManager及脚本执行逻辑
打完当前场景时：
GameFightScene中有ui： fm1

update()中有：
GameManager::getInstance().doLogic(); 游戏逻辑，执行脚本，关卡计时

    if (!isPauseAllObjectLogic())
		{
			doSlowMotionLogic();
			doScriptLogic();
			doMovingObjDataLogic();
			doObjChangeActionDataLogic();
			doScriptDelayLogic();
		}

        =>
         while (!curScriptVec_.empty())
        {
            //createGuiSkipLayer(nullptr);
            auto& act = *curScriptVec_.front();
            auto currentActType = act.getType();
            auto result = act();
            curScriptVec_.pop_front();
            bool willGoNextScript = true;
            //判斷下一條指令是否也是移動指令
            if (currentActType == scriptAction::ActionType::eActionMovingObject&&!curScriptVec_.empty())
            {
                auto& actNext = *curScriptVec_.front();
                if (actNext.getType() == scriptAction::ActionType::eActionMovingObject)
                    result = scriptAction::ActionRet_t::eActionNext;
            }
            switch (result)
            {
            case scriptAction::ActionRet_t::eActionBreak:
                willGoNextScript = false;
                break;
            case scriptAction::ActionRet_t::eActionPause:
                setScriptPause(true);
                willGoNextScript = false;
                break;
            default:
                break;
            }
            if (!willGoNextScript)
            {
                break;
            }
        }

       std::deque<scriptAction::ScriptActionBaseSharedPtr> curScriptVec_
    在ScriptManager中的runScript（）方法会调用如下方法，将执行脚本抛给GameManager执行。
         void GameManager::addScriptAction(scriptAction::ScriptActionBaseSharedPtr script)
    {
        curScriptVec_.push_back(script);
    }

游戏中脚本的处理方式：
ScriptManager通过initLevelScriptWithFile（std::string path, int32_t lvIndex）读取当前场景脚本数，条件 scriptLevelConditions_，
    和脚本行为 scriptLevelConducts_及对话数，对话内容。

GameObject的update方法中调用ScriptManager的scanScript（obj） 和doScriptLogicBy（obj）

scanScript（obj） 循环判断条件是否达成，达成执行脚本动作。

 void ScriptManager::doScriptLogicBy(GameObject* obj)
    {
        if (scriptRunning_&&obj == scriptObj_)
        {
            if (runScript())
            {
                obj->setScriptRunning(false);
                scriptRunning_ = false;
                scriptObj_ = nullptr;
            }
        }
    }

GameManager的doScript（）真正执行脚本，在Scene 的update方法中调用。

update()方法是场景中的node的update()方法先执行。


## GameObjectManager
    setSceneObjectNode(sceneObjectNode) 添加场景节点

    cc::GameObjectManager::getInstance().createAndAddAllSceneGameObejctToSceneNode(); 创建游戏对象并添加到场景

    => 通过SceneDataManager获取classId，在获取AIType，通过调用createGameObjectByAIID（AIType） 创建游戏对象。再将创建对象添加至场景

    => createGameObjectByAIID（AiType） 通过Aitype作为键，取到游戏对象方法创建游戏对象

    => 游戏对象创建方法在游戏打开是预加载场景中调用一下方法添加。
    void registerObjCreateFunc(int32_t AITypeV, std::function<BaseObject*(void)> func)
		{
			objCreateFuncMap_[AITypeV] = func;
		}


    在游戏对象创建时，保存激活的游戏对象，碰撞对象

## ControlFieldObject

## ControlManager 
    GamePreloadScene 中： 
     cc::ControlManager::getInstance().initJoyControlCount(joyControlCount);    //1
     cc::ControlManager::getInstance().initButtonControlCount(buttonControlCount); //10



## UIJoyStick
    onTouchBegan :



## AI
    AICondType  id typeList_
    AIActionType    id typeList_
    AIType  name_  vectorStates_ (std::vector<AIState>)


     class AIState
    {
    public:
        AIState(std::string name) :name_(name)
        {
        }
        std::string name_;
        std::vector<AICondActionBlock> vectorBlocks_;
    };

      class AICondActionBlock
    {
    public:
        std::vector<AICond> vectorConds_;
        std::vector<AIAction> vectorConfirmActions_;
        std::vector<AIAction> vectorNoConfirmActions_;
    };

### AIMachine 状态机

    class AIMachine
    {
    public:
        typedef enum : uint32_t
        {
            eActionNext,   //执行下一个Block
            eActionBreak,  //返回执行流，当次循环结束
        }ActionRet_t;
        virtual void changeGroup(const std::string&) = 0;
    };


class AIContext
    {
    private:
        uint32_t counter_ = 0;
        uint32_t counterByPcSet_ = 0;
        ivy::GameObject* ownerObject_ = nullptr;
        ivy::GameObject* targetObject_ = nullptr;
        std::string preGroupName_ = "";
    public:
        explicit AIContext(ivy::GameObject* obj = nullptr, ivy::GameObject* targetObject = nullptr)
            :ownerObject_(obj), targetObject_(targetObject)
        {}

        class Data                                        uint32_t counter;
        

        class DataInGame : public Data                    int32_t gameValue = -1;


        class GameObjectDataInGame : public DataInGame    ivy::GameObject* obj;
        typedef std::shared_ptr<DataInGame> DataPtr;
        typedef std::shared_ptr<GameObjectDataInGame> GameObjectPtr;

        void onRun(AIMachine& machine)
        {
            GMacroUnUsedVar(machine);
            ++counter_;
            ++counterByPcSet_;
            targetObject_ = nullptr;
        }

        void onChangeGroup(AIMachine& machine)
        {
            GMacroUnUsedVar(machine);
            setCounterByGroupChange(0);
        }

        void onPreRun(AIMachine& machine)
        {
            GMacroUnUsedVar(machine);
        }

        uint32_t getCounter() const
        {
            return counter_;
        }

        ivy::GameObject* getTargetObject()
        {
            return targetObject_;
        }

        void setTargetObject(ivy::GameObject* obj)
        {
            targetObject_ = obj;
        }

        ivy::GameObject* getOwnerObject()
        {
            return ownerObject_;
        }

        void setCounterByGroupChange(uint32_t c)
        {
            counterByPcSet_ = c;
        }

        uint32_t getCounterByGroupChange() const
        {
            return counterByPcSet_;
        }

        void setPreGroupName(const std::string& name)
        {
            preGroupName_ = name;
        }

        std::string getPreGroupName()
        {
            return preGroupName_;
        }

    };

## AINameContext

      class AINameContext : public AIContext
    {
    public:
        AINameContext(ivy::GameObject* obj = nullptr)
            :AIContext(obj)
        {}
        bool contains(const std::string& idx)
        {
            return dict_.find(idx) != dict_.end();
        }
        get()
        set()
    private:
        std::map<std::string, DataPtr> dict_;
    };

## AIMachineT
         template<typename Context>
    class AIMachineT : public AIMachine
    {
        // 限定模版Context必须为AIContext的子类
        static_assert(std::is_base_of<AIContext, Context>::value, "Context must be inherit form AIContext");
    public:
        //使用函数对象实现条件判定和动作调用
        typedef std::function<bool(AIMachineT& self, Context& context)> CondCall;
        typedef std::function<ActionRet_t(AIMachineT& self, Context& context)> ActionCall;
        
        void addChangeGroupCallFunc(std::function<void()>&& call)
        {
            changeGroupCallFunc_.push_back(std::move(call));
        }
    private:
        std::vector<std::function<void()>> changeGroupCallFunc_;
        struct CondActionBlock
        {
            CondCall cond;
            ActionCall action;
            ActionCall elseAction;
        };

        typedef std::vector<CondActionBlock> Blocks;

        struct ListUnit
        {
            std::string name;
            std::vector<CondActionBlock> blocks;
        };

        typedef std::vector<ListUnit> List;

        Context context_;

        std::vector<ListUnit> list_;

        typename List::iterator pc_;
        bool hadInit = false;
    public:
        bool isInit() const
        {
            return hadInit;
        }
        void setInit(bool is)
        {
            hadInit = is;
        }
        AIMachineT(ivy::GameObject* obj = nullptr)
            :context_(obj)
        {}

        void namePush(const std::string& name)
        {
            list_.push_back({ name, Blocks() });
        }

        template<typename Itr>
        void namePush(Itr b, Itr e)
        {
            for (; b != e; ++b)
                list_.push_back(ListUnit{ *b, Blocks() });
        }

        void blockPush(const std::string& name, CondCall&& cond, ActionCall&& action,
            ActionCall&& elseAction = ActionCall())
        {
            auto itr = std::find_if(list_.begin(), list_.end(),
                [&name](const ListUnit& l)
            {
                return name == l.name;
            }
            );
            assert(itr != list_.end());
            itr->blocks.push_back({ std::move(cond), std::move(action), std::move(elseAction) });
        }

        temp    
        void blockPush(const std::string& name, Itr b, Itr e)
        {
            auto itr = std::find_if(list_.begin(), list_.end(),
                [&name](const ListUnit& l)
            {
                return name == l.name;
            }
            );
            assert(itr != list_.end());
            for (; b != e; ++b)
                itr->blocks.push_back(std::move(*b));
        }

        //AI运行前调用 初始化迭代器位置
        void preRun()
        {
            pc_ = list_.begin();
            context_.onPreRun(*this);
        }

        void run()
        {
            context_.onRun(*this);
            for (auto itr = pc_, end = list_.end(); itr != end; ++itr)
            {
                //从当前迭代器位置循环判断条件和执行动作
                auto& blocks = itr->blocks;
                for (auto bitr = blocks.begin(), bend = blocks.end(); bitr != bend; ++bitr)
                {
                    auto const condRet = bitr->cond(*this, context_);
                    if (condRet == false)
                    {
                        if (!bitr->elseAction)
                            continue;
                        auto const actionRet = bitr->elseAction(*this, context_);
                        switch (actionRet)
                        {
                        case eActionNext:
                            continue;
                        case eActionBreak:
                            return;
                        }
                    }
                    auto const actionRet = bitr->action(*this, context_);
                    switch (actionRet)
                    {
                    case eActionNext:
                        break;
                    case eActionBreak:
                        return;
                    }
                }
            }
        }

        bool empty() const
        {
            return list_.empty();
        }

        bool findGroup(const std::string& name)
        {
            auto itr = std::find_if(list_.begin(), list_.end(),
                [&name](const ListUnit& l)
            {
                return name == l.name;
            }
            );
            if (itr != list_.end())
                return true;
            else
                return false;
        }

        void changeGroup(const std::string& name)
        {
            if (list_.size() <= 0)
            {
                return;
            }
            for (auto func : changeGroupCallFunc_)
            {
                func();
            }
            context_.setPreGroupName(pc_->name);
            auto itr = std::find_if(list_.begin(), list_.end(),
                [&name](const ListUnit& l)
            {
                return name == l.name;
            }
            );
            if (itr == list_.end())
                assert(itr != list_.end());
            pc_ = itr;
            context_.onChangeGroup(*this);
        }

        std::string getCurGroupName() const
        {
            if (empty())
                return "";
            return pc_->name;
        }

        Context& getContext()
        {
            return context_;
        }
    private:
        void log(const std::string& msg)
        {
            GMacroUnUsedVar(msg);
            //#ifdef LOG_AI_STATE
            //                auto& owner = *context_.getOwnerObject();
            //            const auto& actorID = owner.getActorID();
            //            if (actorID.invalid())
            //            {
            //                cocos2d::log("AIMachine::run: %p-%s", &owner, msg.c_str());
            //                return;
            //            }
            //            cocos2d::log("AIMachine::run: classID=%d, actorID=%d; -> %s",
            //                owner.getClassID(), actorID.getValue(), msg.c_str()
            //            );
            //#endif
        }
    };
}

 AIMachine ，先调用PreRun()方法，将pc_（iterator 位置）置为list_(一种ai状态列表，列表中的每一项包含ai状态的名字及一个ai条件函数，两个ai行为函数，)的begin()，
            接着调用AIContext的OnPreRun（）
                run（）方法使用pc开始迭代list_，执行对应的Blocks中的ai条件及ai行为。


## FightObject  每一个游戏对象拥有一个AIMachine
    FightObject 中有initAI() 通过类AIMachineIniter 及FightObject的objAIID_ 实现aiMachine_的初始化。
        objAIID_在对象创建时初始化。
        update（）中使用doAIlogic（）


        