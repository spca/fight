# BaseObject

//方向
enum class Dir : uint8_t
		{
			eLeft,
			eRight,
			eEnumCount
		};


enum ObjectBaseInfo
		{   


            //对象id
			eActorID,
			eClassID,
			eFlag,
			eParaSize,
			eLayerID,
			eScriptID,
			eActionID,
			// 出生坐标x
			eBornPosX,
			eBornPosY,
			eActiveX0,
			eActiveY0,
			eActiveX1,
			eActiveY1,
			eDataID,
			eEnumCount
		};



# GameObject 
  enum ObjectPropertyType
        {
            //等级
            eLv,
            //血量
            eHp,
            //最大血量
            eMaxHp,
            //体力
            eStamina,
            //最大体力
            eMaxeStamina,
            //体力恢复值
            eStaminaRecover,
            //攻击力
            eAtk,
            //防御力
            eDef,
            //暴击几率
            eCriPro,
            //暴击伤害
            eCriDmg,
           //格挡值
            eBlock,
            //霸体值
            eSuperBlock,
            //霸体值状态 0-普通 1-恢复中
            eSuperBlockState,
            //霸体恢复值
            eSuperBlockRecover,
            //移动速度
            eWalkSpeed,
            eRunSpeed,
            eMaxBlock,
            eMaxSuperBlock,
            eCurSkillPercentDamage,
            //不能浮空 0-无效 1-有效
            eUnableToAir,
            //是否可碰撞 0-无效 1-有效
            eCollidable,
            eCollideLimitHeight,
			eExp,
			eBurstValue,
			eMaxBurstValue,
			eBurstIncreaseValue,
			eBurstDamageValue,
			eBurstState,//0-储存状态 1-释放状态
			eBurstChangeValue,
			eScore,
			eSuperArmorTimer,
            eEnumCount,
        };




## 正常攻击调用堆栈：
    FightObject->update(float) => FightObject->doAILogic() => AIMachineT->run() =>ActionMultiT::operator() 
        =>ActionAttackGameObjectByAniAtkSequence::operator(){
	    private:
			 std::vector<int32_t> objGroupIDs_;
             if(srcObj->doKeyFrameLogicTo()){
         
                if(atkVaild){
                    srcObj->doLogicWhenKeyFrameValid(){
                        atkObj_ == true;
                    }
                }
        
             }

        }  => update(float) {

            doKeyFrameLogic(){



            }
        }


## 使用技能：
    玩家点击技能按钮，触发AI条件，
    =》 ActionUseSkill(){

        obj->useSkill(skillID_){

            通过skillID获取技能对象，判断对象是否在CD，并且打断正在使用的技能
            判断技能消耗，false return
             curUsingSkill_ = checkSkill;
            curUsingSkill_->doReadyLogic();
        }
    }
    FightObject ->update(float)  => FightObject -> doSkillLogic(){


        if(objDataType_ == Hero){

            if(curUsingSkill_){   指向技能对象的指针。
                判断是否有技能进度条，或是否需要显示。

               curUsingSkill_ -> doLogicByObject(this) {

                    判断技能当前状态（state_）{
                        eStart: 
                            设置技能cd时间，
                            将技能状态置为eRunning,
                            设置fightObject技能动画ActionID及playSTate，技能伤害。
                        eRunning:
                            先判断技能动画是否播放完：true -> doEndLogicByObject(fightObj){

                                 SkillBase::doEndLogicByObject(obj){
                                        isUsingSkill标志置false
                                        将任务的AI组切换为正常状态
                                        调用fightObj->useSkill(-1);
                                        设置
                                 }
                                 将技能状态置为eStart
                            }
                    }
                 
               }

            }
        }
    }

 在角色播放技能动画时，技能动画中定义一些关键帧，或是某帧中创建粒子对象。
	FightObject的doKeyFrameLogic（）中有如下判断：
		  if (objAniPlayer_->isKeyFrameCustomDataExist(CustomKeyFrameType::eCreateParticle))
			 创建对应的粒子对象，执行doKeyFrameLogicTo(beAtkObj)



