## Ref
	void retain(){ ++_referenceCount; }
	void release(){ 
		--_referenceCount;
		if (_referenceCount == 0)
  	  {
		if (!poolManager->getCurrentPool()->isClearing() && poolManager->isObjectInPools(this))
        	{
			assert(false) + log(msg);
		}
		delete this;
	 }
		
	}
	Ref* autorelease(){
		PoolManager.getCurrentPool.addObject(this);
		
	}
	unsigned int getReferenceCount() const{}
	protected:
		unsigned int _referenceCount;
		friend class AutoreleasePool;


### AutoreleasePool
	std::vector<Ref*> _managedObjectArray;
	void addObject(Ref* object){
		_managedObjectArray.push_back(object);
	}
	
	void clear(){
		std::vector<Ref*> releasings;
  	  	releasings.swap(_managedObjectArray);
 		 for (const auto &obj : releasings)
   		 {
      			  obj->release();
  	  	}
	}

### PoolManager
	  void push(AutoreleasePool *pool);
   	 void pop();
   	 static PoolManager* s_singleInstance;
   	 std::vector<AutoreleasePool*> _releasePoolStack;


## Application -> run(){
	Director -> mainLoop(){
		 PoolManager::getInstance()->getCurrentPool()->clear();
	}
	}



## Node::getNodeToParentTransform()
