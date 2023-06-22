 冷藏药品运输箱
====
# 项目整体概念
1.期望实现的功能：能够长时间续航制冷，解决冰袋短时制冷的问题并易于携带。  
2.潜在用户画像：从事医疗方面的医务医护人员、冷链运输人员和有携带冷藏物品需求的人。  
# 学习制作过程
**初始概念**：需要一个便携的，可以制冷/保持温度恒定的物品。*参考：车载冰箱、制冰机。*  
**元件与材料**：半导体制冷片TEC1-12706、隔热棉、导热膏、100x120mm散热片、60x45散热片、GD66硅脂x2、M4螺丝螺母垫片、9025风扇、4010风扇、网罩、ESP32合宙开发板、继电器、电阻、杜邦线若干、DS18Ｂ20温度传感器、12V锂电池  
**元件使用效果**：  
1.整体尺寸：长100x宽120x总厚度93（单位mm）。  
2.重量：约158g。  
3.制冷效果：24摄氏度以下结霜。   
4.整机功率：12v电压下46W+9025散热风扇3W+4010冷端风扇1.5W=50W  

## 控制思路
当温度高于某一设定值时，制冷器开始工作，低于该值时，停止工作。  
***代码***：  
```
// #include <OneWire.h> //可以不引入，因为DallasTemperature.h中已经引入了OneWire.h
#include <DallasTemperature.h>

#define ONE_WIRE_BUS 4               //1-wire数据总线连接在IO4
OneWire oneWire(ONE_WIRE_BUS);       //声明
DallasTemperature sensors(&oneWire); //声明

void setup(void)
{
  Serial.begin(115200);
  Serial.println("");

  sensors.begin(); //初始化总线
  sensors.setWaitForConversion(false); //设置为非阻塞模式
  pinMode(8, OUTPUT);
}

unsigned long previousMillis = 0; //毫秒时间记录
const long interval = 1000;       //时间间隔
void loop(void)
{
  //以下段落相当于每秒读取前次温度，并发起新一次温度转换
  unsigned long currentMillis = millis();         //读取当前时间
  if (currentMillis - previousMillis >= interval) //如果和前次时间大于等于时间间隔
  {
    previousMillis = currentMillis; //更新时间记录

   float tempC = sensors.getTempCByIndex(0); //获取索引号0的传感器摄氏温度数据
    if (tempC != DEVICE_DISCONNECTED_C)       //如果获取到的温度正常
    {
      Serial.print("\n当前温度是： ");
      Serial.print(tempC);
      Serial.println(" ℃");
    }

   Serial.println("发起温度转换");
    sensors.requestTemperatures(); //发起新的温度转换
    if(tempC<=24.00)
    digitalWrite(8, LOW);
    else
    digitalWrite(8,HIGH);   
  }

  delay(20);
  Serial.print(".");
}
```

