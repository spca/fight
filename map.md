## CameraManager

### ---------
	GameFightScene中调用CamereaManager的setFoucusGameObject(obj) 将focusGameObject_对象设置为Hero对象，在所有的FightObject的update方法中判断是否为CameraManager中的focusGameObject对象，是则会调用CameraManager的doLogicByFocusObj（this）方法，
	doLogicByObj（obj）{
		lookTo（Point）{
			updateCamera（bool）{
				updateFunc_(bool);
			}
		}
	
	}
	updateFunc_(bool)则是在GamePreLoadScene中传入。

