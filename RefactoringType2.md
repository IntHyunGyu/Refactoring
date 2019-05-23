## Java 추상화 적용하기

각 이미지 별로 진행시간, 효과 등을 출력하는 로직을 만들고 있었다.

그 중 효과를 출력 하는 부분에서 각 효과별로 출력되는 내용이 다르고 사용되는 변수들이 다른데 이를 일일히 만들기에는 굉장히 **시간 낭비**인거 같고 또 **중복 코드** 들이 많이 생기게 된다. 이를 해결하기 위해서 Java에 기능 중 하나인 abstract 즉, **추상클래스** 를 활용 하였다.

**추상화**를 사용 하여 얻을수 있는 장점

* 중복 코드를 줄일수 있다.
* 비슷하지만 다른 로직들을 하나에 메소드로 처리 가능하다.

### 1. 이전 생각의 로직

* 추상클래스를 활용하기 전 로직이다.
  * Before는 이미지를 의미한다.

* 효과 정보 Vo

  * ~~~ java
    public class BeforeEffact {
    	int sTime;
    	int eTime;
    	int length;
    	Rect sLocation;
    	int now;
    	int size;
    	String ecName;
    
    	public BeforeEffact(int sTime, int eTime, int length, Rect sLocation, int now, int size, String ecName) {
    		super();
    		this.sTime = sTime;
    		this.eTime = eTime;
    		this.length = length;
    		this.sLocation = sLocation;
    		this.now = now;
    		this.size = size;
    		this.ecName = ecName;
    	}
        ...
    }
    ~~~

  * **불필요한 변수**들	

    * 각각의 효과들은 location만 계산이 필요한 경우, now만 필요한 경우, size만 필요한 경우 등 여러가지 케이스가 있다.

* Vo setting 

  * ~~~ java
    private List<BeforeCutVo2> SettingBefores(int caseNum) {
    		if (caseNum == 1) {
    			List<BeforeCutVo2> befores = new ArrayList<BeforeCutVo2>();
    			BeforeCutVo2 before1 = new BeforeCutVo2();
    			BeforeCutVo2 before2 = new BeforeCutVo2();
    			BeforeCutVo2 before3 = new BeforeCutVo2();
    			
    			before1.setPk(1);
    			before1.setsTime(1);
    			before1.seteTIme(10);
    			BeforeEffact effact1 = 
                    new BeforeEffact(3, 6, 0, null, 1, 1, "Blink", "");
    			effact1.setLocation("A");
    			before1.setEffect(effact1);
    			BeforeSceneChange startSC = 
                    new BeforeSceneChange(1, 2, 0, null, 0, 0, "Move");
    			startSC.setLocation("B");
    			before1.setStartSC(startSC);
    			BeforeSceneChange endSC = 
                    new BeforeSceneChange(7, 10, 0, null, 0, 0, "Opacity");
    			endSC.setLocation("C");
    			before1.setEndSC(endSC);
    			before1.setLocation("D");
    			
    			befores.add(before1);
    			befores.add(before2);
    			befores.add(before3);
    
    			return befores;
    		}
    
    		return null;
    	}
    ~~~

  * 가독성 x

    * effact와 SceneChange는 각각 같은 BeforeEffact와, BeforeSceneChange객체를 선언하여 Before 객체에 set...(); 하는 형식으로 setting 하였기 때문에 **어떤 효과**가 들어갔는지 데이터를 넣은 부분을 확인 하기 전까지는 알기 힘들다.

  * 메모리 낭비

    * 각 효과마다 사용되지 않는 변수들까지 객체를 선언할때 사용되기 때문에 **메모리**가 낭비 된다.

* 효과 출력

  * ~~~ java
    private void checkEffact(List<BeforeCutVo2> befores, int cut, int time) {
    		String effactName;
        effactName = effectName.toLowerCase();
    		effectName = befores.get(cut).effect.getEcName();
    
    		switch (effactName) {
    		case "blink":
    			System.out.println(befores.get(cut).effect.now++ + "번 깜박임");
    			break;
    		case "vibration":
    			int key = time % 4;
                 	switch(key) {
                        case 0 : 
                            befores.get(cut).effact.sLocation.x -= 3; 	                                       befores.get(cut).effact.sLocation.y -=3; 
                            break;
                        case 1 : befores.get(cut).effact.sLocation.x += 3;                                          befores.get(cut).effact.sLocation.y +=3; 
                            break;
                        case 2 : befores.get(cut).effact.sLocation.x -= 3;                                          befores.get(cut).effact.sLocation.y +=3; 
                            break;
                        case 3 : befores.get(cut).effact.sLocation.x -= 3;                                          befores.get(cut).effact.sLocation.y -=3; 
                            break;
                    }
    			System.out.println("진동 진행 후 위치 : [x=" + befores.get(cut).effact.sLocation.x + ", y=" + befores.get(cut).effact.sLocation.y + "]");
    			break;
    		case "wiggle":
    			System.out.println(befores.get(cut).effect.now + 10 + "%");
    			break;
    		case "zoominout":
    			int weight = 0;
    			String type;
    			type = befores.get(cut).effact.getType();
    			if("plus".equals(type)) {
    				weight = 10/100;
    			} else if("subtract".equals(type)) {
    				weight = -10/100;
    			}
    			
    			System.out.println("진행 후 보여지는 크기 [width=" + befores.get(cut).effact.sLocation.width*weight + ", height="
    					+ befores.get(cut).effact.sLocation.height*weight + "]");
    		}
    ~~~

  * **여러 기능**의 메소드

    * 메소드는 **하나의 메소드**당 **하나의 기능**을 하는것이 적절하다. 그러나 각 효과를 구분하고 처리 하는 메소드를 하나씩 메소드를 만드는것은 굉장히 시간이 낭비되고 메모리가 낭비된다.
    
    * 이 메소드도 효과가 무었인지 check하기 위한 용도지만 check는 물론이고 효과 계산, 출력등 **너무 많은 기능**들을 처리하고 있어 잘못된 방법이라고 볼수있다.

***

### 2. 추상화를 적용 후 로직

* 효과 정보 Vo

  * ~~~ java
    public abstract class effact {
    
    	protected int sTime;
    	protected int eTime;
    	protected int length;
    	protected Rect sLocation;
    	protected int now;
    
    	public effact(int sTime, int eTime, Rect sLocation) {
    		super();
    		this.sTime = sTime;
    		this.eTime = eTime;
    		this.length = eTime-sTime+1;
    		this.sLocation = sLocation;
    	}
        ...
        public abstract int getResult(int key);
    	public abstract String getEFName();
    	public abstract Rect returnLocation(int key);
    }
    ~~~

  * 공통 변수

    * 각 효과들이 공통적으로 사용하는 변수만 선언하였다.

  * 결과 처리 메소드 **추상화**

    * 각 효과 별로 처리되는 결과가 다르기 때문에 이를 추상 메소드화 하여 효과별로 메소드를 만드는 것이 아닌 **메소드**를 **상속**받아 처리함으로서 **공통 부분**을 줄일수 있다.

* Vo setting

  * ~~~ java
    private List<BeforeCutVo> SettingBefores(int caseNum) {
    		if (caseNum == 1) {
    			List<BeforeCutVo> befores = new ArrayList<BeforeCutVo>();
    			BeforeCutVo before1 = new BeforeCutVo();
    			BeforeCutVo before2 = new BeforeCutVo();
    			BeforeCutVo before3 = new BeforeCutVo();
    
    			// 첫번째 장면
    			before1.setPk(1);
    			before1.setsTime(1);
    			before1.seteTime(10);
    			before1.setLocation("A");
    			// 장면시작 ScaleUp
    			SCScaleUp scaleup = new SCScaleUp(1, 3, before1.getLocation(), 5, 5);
    			before1.setStartSecenChange(scaleup);
    			// 장면종료 move
    			SCMove move = new SCMove(8, 10, before1.getLocation(), 10, -10);
    			before1.setEndSecenChange(move);
    			// 효과 Blink
    			EFBlink blink = new EFBlink(4, 5, before1.getLocation(), 1);
    			before1.setEffect(blink);
                
    			...
                    
                befores.add(before1);
                befores.add(before2);
                befores.add(before3);
    
                return befores;
            }
        	return null;
    	}
    ~~~

  * 가독성 ↑

    * 각 효과들은 effact를 상속받아 효과 이름으로 객체가 선언되기 때문에 어떤 효과가 들어갔는지 알기 쉽다.

* 효과 출력

  * ~~~ java
    private void checkEffact(List<BeforeCutVo> befores, String effactName, int cut, int time) {
    		effactName = effectName.toLowerCase();
    
    		switch (effactName) {
    		case "blink":
    			System.out.println(befores.get(cut).effact.getResult(1) + "번 깜박임");
    			break;
    		case "vibration":
    			Rect nowLocation = befores.get(cut).effact.returnLocation(time % 4);
    			System.out.println("진동 진행 후 위치 : [x=" + nowLocation.x + ", y=" + nowLocation.y + "]");
    			break;
    		case "wiggle":
    			System.out.println(befores.get(cut).effact.getResult(1) + "%");
    			break;
    		case "zoominout":
    			System.out.println("진행 후 보여지는 크기 [width=" + befores.get(cut).effect.returnLocation(0).width + ", height="
    					+ befores.get(cut).effect.returnLocation(1).height + "]");
    		}
    
    	}
    ~~~

  * 여러 기능에 메소드

    * 1번에 효과 출력 메소드에서 언급한 것 처럼 **메소드**는 **하나의 기능**만을 가지고 있는것이 적절하다. 이 코드 또한 효과 구분과 출력을 동시에 하고 있긴 하지만 추상화를 적용하여 결과 처리 메소드를 하나로 처리 하였다. 

***

### 3.  더 보완된 추상화 로직

* 1번 코드에서 추상화를 진행하여 공통 로직을 처리 했지만 여전히 **메소드**는 **여러가지 기능**을 처리 하고있다. 이를 해결하기 위해 아래 로직은 보완된 로직이다. 

* 효과 정보 Vo

  * ~~~ java
    public abstract class effact {
    
    	protected int sTime;
    	protected int eTime;
    	protected int length;
    	protected Rect sLocation;
    	protected int now;
    
    	public effact(int sTime, int eTime, Rect sLocation) {
    		super();
    		this.sTime = sTime;
    		this.eTime = eTime;
    		this.length = eTime-sTime+1;
    		this.sLocation = sLocation;
    	}
        ...
        public abstract void setting(int key);
    	public abstract String getEFName();
    	public abstract Rect returnLocation(int key);
        public abstract void print();
    }
    ~~~
  * 변화된 점

    * 2번의 코드에서 결과를 리턴하는 메소드 대신 setting() 메소드와 출력을 나누기 위해 print() 메소드가 추가 됬다.

* effact를 상속 받은 변화된 효과 메소드

  * ex) EFVibrate

    ~~~ java
    package com.Moonlight.Util;
    
    import org.opencv.core.Rect;
    
    public class EFVibrate extends effact {
    	
    	int vibration;
    	
    	public EFVibrate(int sTime, int eTime, Rect sLocation, int vibration) {
    		super(sTime, eTime, sLocation);
    		this.vibration = vibration;
    	}
    	
    	
    	@Override
    	public void setting(int key) {
    		switch(key) {
    			case 0 : this.sLocation.x -= 3; this.sLocation.y -=3; break;
    			case 1 : this.sLocation.x += 3; this.sLocation.y +=3; break;
    			case 2 : this.sLocation.x -= 3; this.sLocation.y +=3; break;
    			case 3 : this.sLocation.x -= 3; this.sLocation.y -=3; break;
    		}
    	}
    	
    	@Override
    	public String getEFName() {
    		return "vibration";
    	}
    
    	@Override
    	public Rect returnLocation(int key) {
    		return null;
    	}
        
        @override
        public void print() {
            System.out.println("진동 진행 후 위치 : [x=" + this.location.x + ", y=" + this.location.y + "]");
        }
    
    }
    ~~~

  * 효과별 처리와 출력을 effact를 상속받은 각각의 효과 클래스에서 진행 하였다.

* 효과 출력

  * 추상화를 적용하기 전 효과 출력 메소드
    
    ~~~ java
    rivate void checkEffact(List<BeforeCutVo2> befores, int cut, int time) {
    		String effactName;
    effactName = effectName.toLowerCase();
    		effectName = befores.get(cut).effect.getEcName();
  
    		switch (effactName) {
    		case "blink":
    			System.out.println(befores.get(cut).effect.now++ + "번 깜박임");
    			break;
    		case "vibration":
    			int key = time % 4;
                 	switch(key) {
                        case 0 : 
                            befores.get(cut).effact.sLocation.x -= 3; 	                                       befores.get(cut).effact.sLocation.y -=3; 
                            break;
                        case 1 : befores.get(cut).effact.sLocation.x += 3;                                          befores.get(cut).effact.sLocation.y +=3; 
                            break;
                        case 2 : befores.get(cut).effact.sLocation.x -= 3;                                          befores.get(cut).effact.sLocation.y +=3; 
                            break;
                        case 3 : befores.get(cut).effact.sLocation.x -= 3;                                          befores.get(cut).effact.sLocation.y -=3; 
                            break;
                    }
    			System.out.println("진동 진행 후 위치 : [x=" + befores.get(cut).effact.sLocation.x + ", y=" + befores.get(cut).effact.sLocation.y + "]");
    			break;
    		case "wiggle":
    			System.out.println(befores.get(cut).effect.now + 10 + "%");
    			break;
    		case "zoominout":
    			int weight = 0;
    			String type;
    			type = befores.get(cut).effact.getType();
    			if("plus".equals(type)) {
    				weight = 10/100;
    			} else if("subtract".equals(type)) {
    				weight = -10/100;
    			}
    			
    			System.out.println("진행 후 보여지는 크기 [width=" + befores.get(cut).effact.sLocation.width*weight + ", height="
    					+ befores.get(cut).effact.sLocation.height*weight + "]");
    		}
    ~~~
    
  * 추상화를 적용한 후 효과 출력 메소드
  
    ~~~ java
    private void checkEffact(int cut) {
    		befores.get(cut).effact.print();
    }
    ~~~
  
    전과 후를 대충 봐도 알수 있듯이 코드가 훨씬 간결해지고 **메소드**는 **하나의 기능**만을 실행하고 있다.

***

### 결론

* 추상화

  * 객체에 공통적인 변수와 메소드를 묶는것

  ![Alt text](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQqYOiouoFlcgaSU8BDvzMo_WZo52F3FR3ZQzni8NHx8S4ZlUqZ)

  * 추상화를 활용하면 **메소드**의 **기능 독립**과 **중복코드**를 줄일수 있다.

* 후기
  
  * 이번 리펙토링을 통해 개념만 배웠던 추상화를 어떻게 활용 하는지에 대해 명확히 알게 되었다.

