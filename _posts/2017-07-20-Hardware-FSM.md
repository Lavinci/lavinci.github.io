---
layout: post
title: "FSM"
excerpt: "有限状态机"
date: 2017-07-20 17:42
tag: [Hardware]
---
# 有限状态机

给学长写软件遇到了难题

下面是相关要求

A、计数仪面板布置和外型如图一所示。测长测速部分按键。              
1、清零键：当生产过程中的一个测长过程完成后，又需要重新计数时，可按一下“清零”键，此时六位数码管显示00000.0，并清除所有疵点数据，作好下一次计数的准备。
2、设定键：如显示为正常运行状态时，连续按下“设定”键5秒钟，则显示进入定长设定状态，下排显示SET1，上排高位数码管闪烁，此时，按“   ”和“  ”键可设定定长值；再按一下“设定”键，则进入系数设定状态，下排显示SET2，再按“  ”和“  ”键可设定系数值（系数值如何确定以下再祥述）；再按一下“设定”键, 进入定长继电器延时时间设定（一般设为00.0）；再按一下“设定”键, 进入键盘锁开、关状态(一般设为OFF,如设为ON后退出设定,则以后对按键的操作无效)；再按一下“设定”键,回到正常运行状态。在“码制”状态下（码制指示灯亮时），不能进入设定状态。
3、班次键：此键是用来设定操作员生产班次的按键。
4、累计键：仪表在非设定状态时，连续按住“累计”键5秒,仪表将以六位数整数形式显示从开机到目前的总产量及每班总产量；速度显示窗显示从开机到目前的总匹数；如此时再按“清零”键则总长及总匹数值复零；如再按“累计”键则显示返回计长状态。
码制键：在计长状态，连续按住“码制”键5秒，此时码制指示灯亮，仪表以码为单位进行测长并显示，原设定的定长值及总长也自动转换成码数；如再连续按住此键5秒，则码制指示灯灭，米制指示灯亮，仪表以米为单位进行测长并显示，原设定的计数值又自动转换成米数。

起初想的是用变量记录当前状态,然后不断深入写,写了一点后发现,如果后期改太麻烦了,而且不符合软件低耦合的要求,去网上查了一下,发现有这么个好东西

1、状态机的要素

状态机可归纳为4个要素，即现态、条件、动作、次态。“现态”和“条件”是因，“动作”和“次态”是果。详解如下：

①现态：是指当前所处的状态。

②条件：又称为“事件”。当一个条件被满足，将会触发一个动作，或者执行一次状态的迁移。

③动作：条件满足后执行的动作。动作执行完毕后，可以迁移到新的状态，也可以仍旧保持原状态。动作不是必需的，当条件满足后，也可以不执行任何动作，直接迁移到新状态。

④次态：条件满足后要迁往的新状态。“次态”是相对于“现态”而言的，“次态”一旦被激活，就转变成新的“现态”了。


拿来改写了下代码,感觉棒棒哒
写个java用例放在这里
```java
package property.state.stateMachine;  
import static tool.Print.*;//pln  
/** 
 * 空调Aircon。简单的模型： 
 * 遥控器有两个按钮(更多的按钮在下面的例子中引入)，power电源键和cool制冷键。 
 * 空调的运行呈现3个状态，停止/Off、仅送风/FanOnly、制冷/Cool。 
 * 起始状态为Off 
 * @author (yqj2065)  
 * @version 0.1 
 */  
public class Aircon0{  
    // off，FanOnly，AC  
    private int state=0;//起始状态为Off  
    public int getState(){return state;}  
    //两个Action  
    public void power(){//按power键  
        if(state==0){//off  
            state=1;  
            pln("start Fan");   
        }else if(state==1){//FanOnly  
            state=0;  
            pln("stop Fan");   
        }else{  
            state=0;  
            pln("stop Cool");   
        }  
    }  
  
    public void cool(){//按制冷键  
        if(state==0){//off  
            pln("nothing");  
        }else if(state==1){//FanOnly  
            state=2;  
            pln("start Cool");   
        }else{  
            state=1;  
            pln("stop Cool");   
        }  
    }  
}  
package property.state.stateMachine;  
public class ACCtrl{  
    public static void test(){  
        Aircon0 ac = new Aircon0();//power() cool()  
        System.out.println("Current State:" + ac.getState());  
        ac.cool();  
        ac.power();  
        ac.cool();  
        ac.cool();  
        ac.power();  
        ac.power();  
    }  
}  
```

总结:
显示放进了中断里
写按键KeyScan时因为有个5秒延时,无法同时返回时间和键值啊,起初想法是想Windows里那些函数那样,用形参返回
int KeyScan(int &);
后来一想,干脆返回值加10返回后判断就好了

```c
unsigned char code sm_d[]={	//yin
0x3f,0x06,0x5b,0x4f,		//0 1 2 3
0x66,0x6d,0x7d,0x07,		//4 5 6 7
0x7f,0x6f};					//8 9 
unsigned char code sm_dd[]={	//yin
0xBf,0x86,0xdb,0xcf,		//0 1 2 3
0xe6,0xed,0xfd,0x87,		//4 5 6 7
0xff,0xef,0x6d,0x79,			//8 9 S E 
0x31						// T
};	
```
显示有个小数点,起初是用了两个数组保存,一组每个数都有小数点,后来发现直接code|0x80即可


