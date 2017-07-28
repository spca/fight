## BaseAniPlayer
        bool selfUpdate_ = true;    //是否自动更新，为true，开启scheduleUpdate
        bool isFilppedX = false;    //
        BaseAniPlayer()=default;    //默认构造函数
        int32_t aniID_=0;           
        int32_t actionID_=0;
		int32_t actionIDBak_ = 0;
        int32_t sequenceID_=0;
		float actionSequenceDuration_=0;
        float motionValue_=1.0f;
		int32_t exDuration_ = 0;
		bool isPlayEnd_ = false;
		bool needRefreshSpriteChildren_ = true;
        AniPlayState playState_=AniPlayState::eCircle;
        AniPlayType playType_=AniPlayType::eNormal;
        AnimationDataSPtr animationData_=nullptr;
		std::map<int32_t, int32_t> suitChangeMap_;
		std::vector<cocos2d::Sprite*> playerAniChildrenVec_;
		//////////////////////////////////////////////////////////////////////////
        bool showColAtkBox = false;
        cocos2d::DrawNode* colBoxDrawNode = nullptr;
        cocos2d::DrawNode* atkBoxDrawNode = nullptr;
		//////////////////////////////////////////////////////////////////////////
        std::function<void(void*)> playOverFunc_ = nullptr;
		//////////////////////////////////////////////////////////////////////////
        bool useCustomBlendFunc_ = false;
        cocos2d::BlendFunc blendFunc_;

## AniPlayer 
    private:
        BaseAniPlayer* aniPlayer_ = nullptr;


## SpineAniPlayer
	BoneData.h
	
	typedef enum {
		SP_TRANSFORMMODE_NORMAL,
		SP_TRANSFORMMODE_ONLYTRANSLATION,
		SP_TRANSFORMMODE_NOROTATIONORREFLECTION,
		SP_TRANSFORMMODE_NOSCALE,
		SP_TRANSFORMMODE_NOSCALEORREFLECTION
	} spTransformMode;
	struct spBoneData {
		const int index;
		const char* const name;
		spBoneData* const parent;
		float length;
		float x, y, rotation, scaleX, scaleY, shearX, shearY;
		spTransformMode transformMode;
	}

	SlotData.h

	typedef enum {
		SP_BLEND_MODE_NORMAL, SP_BLEND_MODE_ADDITIVE, SP_BLEND_MODE_MULTIPLY, SP_BLEND_MODE_SCREEN
	} spBlendMode;

	typedef struct spSlotData {
		const int index;
		const char* const name;
		const spBoneData* const boneData;
		const char* attachmentName;
		float r, g, b, a;
		spBlendMode blendMode;
	}

	Skin.h
	
	typedef struct spSkin {
		const char* const name;
	}

	EventData.h
	typedef struct spSkin {
		const char* const name;
	}

	Animation.h

	typedef enum {
		SP_TIMELINE_ROTATE,
		SP_TIMELINE_TRANSLATE,
		SP_TIMELINE_SCALE,
		SP_TIMELINE_SHEAR,
		SP_TIMELINE_ATTACHMENT,
		SP_TIMELINE_COLOR,
		SP_TIMELINE_DEFORM,
		SP_TIMELINE_EVENT,
		SP_TIMELINE_DRAWORDER,
		SP_TIMELINE_IKCONSTRAINT,
		SP_TIMELINE_TRANSFORMCONSTRAINT,
		SP_TIMELINE_PATHCONSTRAINTPOSITION,
		SP_TIMELINE_PATHCONSTRAINTSPACING,
		SP_TIMELINE_PATHCONSTRAINTMIX
	} spTimelineType;
	

	struct spTimeline {
		const spTimelineType type;
		const void* const vtable;
	}

	typedef struct spAnimation {
		const char* const name;
		float duration;
	
		int timelinesCount;
		spTimeline** timelines;
	}
	
	IKConstraintData.h
	
	typedef struct spIkConstraintData {
		const char* const name;
		int order;
		int bonesCount;
		spBoneData** bones;
	
		spBoneData* target;
		int bendDirection;
		float mix;
	}

	TransformConstraintData.h
	typedef struct spTransformConstraintData {
		const char* const name;
		int order;
		int bonesCount;
		spBoneData** const bones;
		spBoneData* target;
		float rotateMix, translateMix, scaleMix, shearMix;
		float offsetRotation, offsetX, offsetY, offsetScaleX, offsetScaleY, offsetShearY;
	}
	
	PathConstraintData.h

	typedef enum {
		SP_POSITION_MODE_FIXED, SP_POSITION_MODE_PERCENT
	} spPositionMode;
	
	typedef enum {
		SP_SPACING_MODE_LENGTH, SP_SPACING_MODE_FIXED, SP_SPACING_MODE_PERCENT
	} spSpacingMode;
	
	typedef enum {
		SP_ROTATE_MODE_TANGENT, SP_ROTATE_MODE_CHAIN, SP_ROTATE_MODE_CHAIN_SCALE
	} spRotateMode;
	
	typedef struct spPathConstraintData {
		const char* const name;
		int order;
		int bonesCount;
		spBoneData** const bones;
		spSlotData* target;
		spPositionMode positionMode;
		spSpacingMode spacingMode;
		spRotateMode rotateMode;
		float offsetRotation;
		float position, spacing, rotateMix, translateMix;
	}

	skeletonData.h
	struct spSkeletonData{
		const char* version;
		const char* hash;
		float width,height;
		
		int bonesCount;
		spBoneData** bones;  
		
		int slotCount;
		spSlotData** slots;

		int skinsCount;
		spSkin** skins;
		spSkin* defautltSkin;
	
		int eventsCount;
		spEventData** events;			
		
		int animationsCount;
		spAnimation** animations;
		
		int ikConstraintsCount;
		spIKConstraintData** ikConstraints;
		
		int transformConstraintsCount;
		spTransformConstraintData** transformConstrains;
		
		int pathconstraintsCount;
		spPathConstraintData** pathConstraints;
	}

	spineAnimationData.hpp
	
	struct spAtlasRegion {
		const char* name;
		int x, y, width, height;
		float u, v, u2, v2;
		int offsetX, offsetY;
		int originalWidth, originalHeight;
		int index;
		int/*bool*/rotate;
		int/*bool*/flip;
		int* splits;
		int* pads;

		spAtlasPage* page;

		spAtlasRegion* next;
	};

	
	typedef enum {
		SP_ATLAS_UNKNOWN_FORMAT,
		SP_ATLAS_ALPHA,
		SP_ATLAS_INTENSITY,
		SP_ATLAS_LUMINANCE_ALPHA,
		SP_ATLAS_RGB565,
		SP_ATLAS_RGBA4444,
		SP_ATLAS_RGB888,
		SP_ATLAS_RGBA8888
	} spAtlasFormat;
	
	typedef enum {
		SP_ATLAS_UNKNOWN_FILTER,
		SP_ATLAS_NEAREST,
		SP_ATLAS_LINEAR,
		SP_ATLAS_MIPMAP,
		SP_ATLAS_MIPMAP_NEAREST_NEAREST,
		SP_ATLAS_MIPMAP_LINEAR_NEAREST,
		SP_ATLAS_MIPMAP_NEAREST_LINEAR,
		SP_ATLAS_MIPMAP_LINEAR_LINEAR
	} spAtlasFilter;
	
	typedef enum {
		SP_ATLAS_MIRROREDREPEAT,
		SP_ATLAS_CLAMPTOEDGE,
		SP_ATLAS_REPEAT
	} spAtlasWrap;
	
	struct spAtlasPage {
		const spAtlas* atlas;
		const char* name;
		spAtlasFormat format;
		spAtlasFilter minFilter, magFilter;
		spAtlasWrap uWrap, vWrap;
	
		void* rendererObject;
		int width, height;
	
		spAtlasPage* next;
	};

	struct spAtlas {
		spAtlasPage* pages;
		spAtlasRegion* regions;

		void* rendererObject;
	};
	class SpineAnimationData{
		
		spAtlas* spAtlas_ = nullptr;
		spSkeletonData* spSkeletonData_ = nullptr;
		std::map<int32_t, std::string> actionNameFindMap_;
		std::map<std::string, int32_t> actionIndexFindMap_;
		std::map<std::string, cocos2d::Rect> actionCollisionRectMap_;
		std::map<std::string, cocos2d::Rect> actionAttackRectMap_;
		
	}
	private:
		spine::SkeletonAnimation* player;
		SpineAnimationDataPtr spineAnimationData_;
		





## ClipAniPlayer
	:BaseAniPlayer:Node
	
	std::map<int32_t, int32_t> suitChangeMap_;
	std::vector<cocos2d::Sprite*> playerAniChildrenVec_;	
	std::function<void(void*)> playOverFunc_ = nullptr;

	


## AnimationData
	private:
		AnimationType aniType_;  // 动画类型
	        uint16_t mlgCount_;      // 使用图片数
	
	        std::vector<std::string> srcImgNames_;   //图片名组
	        std::vector<std::vector<std::string>> suitImgNames_; //同图片名组大小
	
	        int16_t templateCount_;
	        int16_t templateSpriteCount_;
	
	        uint16_t moduleCount_; 
	        std::vector<uint8_t> moduleImgIds_;
	        std::vector<cocos2d::Rect> moduleRects_;
	
	        uint16_t frameCount_; 			//图片帧数
	        std::vector<uint8_t> frameBoxIndexs_;	//2倍的图片帧数
	        std::vector<uint8_t> frameSpriteCounts_; //当前帧图元数
	        std::vector<uint16_t> frameSpriteOffset_;	// 当前帧的图元偏移量 （下标从1取，所有size为frameCount+1）
			int32_t frameSpriteMaxCount_;		//帧中图元最多的那一帧的图元数
	
	        uint16_t spritesCount_;                          //图元数
	
	        std::vector<uint8_t> spriteFlips_;		
	        std::vector<uint16_t> spriteModules_;
	        std::vector<cocos2d::Point> spritePositions_;
	        std::vector<int16_t> spriteParticles_;
	        std::vector<int16_t> spriteEffects_;
	        std::vector<int16_t> spriteRotateIndexs_;
	        std::vector<int16_t> spriteSkeletons_;
	
	        int16_t actionCount_; 				//对应编辑器中状态数（动作数）
	        std::vector<int8_t> actionMechModelIndexs_;	
	        std::vector<uint8_t> actionSequenceCounts_;	//当前动作中序列个数
	        std::vector<int16_t> actionSequenceOffset_;	// 当前动作序列数据偏移
	
	        int16_t sequenceCount_;				// 动作序列总数		
	        //优化数据存储结构 取消以下屏蔽的数据以map结构存储	
	        //std::vector<int16_t> seqHurtStates_;		
	        //std::vector<int16_t> seqStiffFrameCounts_;
	        //std::vector<int16_t> seqMoveDistances_;
	        //std::vector<int16_t> seqReserveValues_;
	        std::vector<uint8_t> seqDurations_;		//动作序列重复次数
	        std::vector<int16_t> seqFrameIds_;		//帧id
	        std::vector<int16_t> seqSoundIds_;		
	        std::vector<int16_t> seqReserve1Values_;	//保留数据
	        std::vector<int16_t> seqFlyDatas_;		//浮空
	
	        std::vector<bool> frameHadDataNeedToDealVec_;	//当前动作序列是否有数据要处理
	        std::map<int32_t, int16_t> mapKeyFrameBaseData_;	//
	        std::map<int32_t, int16_t> mapKeyFrameCustomData_;
	        std::map<int32_t, cocos2d::Rect> mapKeyFrameCustomZoneData_;
	
	        int16_t colBoxCount_;			
	        int16_t attBoxCount_;
	        std::vector<cocos2d::Rect> colRects_;
	        std::vector<cocos2d::Rect> attRects_;
	
	        int16_t mechCount_;
	        std::vector<uint8_t> mechModels_;
	
	        int16_t transformSpriteCount_;				//转换图元个数
	        std::vector<int16_t> transformRotations_;		//index从spriteRotateIndexs_获取,
	        std::vector<float> transformScaleXs_;			
	        std::vector<float> transformScaleYs_;
	        std::vector<int32_t> transformOpacitys_;
	        std::vector<cocos2d::Color3B> transformColors_;
	
			std::string animationName_ = "";
	        static std::vector<std::vector<std::string> > particles;// 动画关联粒子索引
